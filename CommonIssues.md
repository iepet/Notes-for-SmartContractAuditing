## PRIVATE IS NOT ACTUALLY PRIVATE!
If you have a Solidity variable that is for instance a passowrd stored as string private s_Privatepassword, it is not actually private.

If we use foundry command ``bash cast storage <contract address> <variable number>`` we can read it.

## ACCESS CONTROL
Is there a hirearchy in the contract? Can everyone access everything? or should only be certain contracts or owner?
