## PRIVATE IS NOT ACTUALLY PRIVATE!
If you have a Solidity variable that is for instance a passowrd stored as string private s_Privatepassword, it is not actually private.

If we use foundry command ``bash cast storage <contract address> <variable number>`` we get in hex and then we can use ``cast parse-bytes32-string <0xblabla>`` to read it.

## ACCESS CONTROL
Is there a hirearchy in the contract? Can everyone access everything? or should only be certain contracts or owner?


## LOOPS! CAn allow for DoS
In the following example from UpdraftCyfrin course. For every new player, the gas cost willbe higher because you have to loop more times. if the attackar calls the function gasilion times, at the end only gasilioners will be able to enter raffle.

´´´javacript
function enterRaffle(address[] memory newPlayers) public payable {
        //Audit I: It is not possible to enter yourself multiple times, as the function will revert if there are any duplicates
        //Audit I: No error is being emmited if the entranceFee is not sent
        require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
        for (uint256 i = 0; i < newPlayers.length; i++) {
            players.push(newPlayers[i]);
        }

        // Check for duplicates
        //Audit I: This is very gas inneficient
        for (uint256 i = 0; i < players.length - 1; i++) {
            for (uint256 j = i + 1; j < players.length; j++) {
                require(players[i] != players[j], "PuppyRaffle: Duplicate player");
            }
        }
        emit RaffleEnter(newPlayers);
    }
´´´
