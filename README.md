# Notes for Smart Contract Auditing

This repository gathers structured notes, tools, and best practices for auditing smart contracts. The repository was created while following guidance from UpdraftCyfrin.

---

## Table of Contents
1. [Scoping](#1-scoping)
2. [Rekt Test Questions](#rekt-test-questions)
3. [CLOC Tool Usage](#cloc-tool-usage)

---

## 0. Onboarding

Make sure there the clienthas the minimal onboarding information for us to start the security review. A good document to check is the one provided by updraftcyfrin.io [Minimal Onboarding](minimalOnboarding.md)

## 1. Scoping

Scoping is the first step in any smart contract audit. This step involves understanding the scope of the audit, including identifying the roles, privileges, external dependencies, and potential vulnerabilities in the smart contract.

### Rekt Test Questions

A useful approach to initiate scoping is to ask the client the following **Rekt Test questions**. These questions help establish the security baseline of the project and identify any potential weak points:

- **Do you have all actors, roles, and privileges documented?**
- **Do you keep documentation of all the external services, contracts, and oracles you rely on?**
- **Do you have a written and tested incident response plan?**
- **Do you document the best ways to attack your system?**
- **Do you perform identity verification and background checks on all employees?**
- **Do you have a team member with security defined in their role?**
- **Do you require hardware security keys for production systems?**
- **Does your key management system require multiple humans and physical steps?**
- **Do you define key invariants for your system and test them on every commit?**
- **Do you use the best automated tools to discover security issues in your code?**
- **Do you undergo external audits and maintain a vulnerability disclosure or bug bounty program?**
- **Have you considered and mitigated avenues for abusing users of your system?**

### CLOC Tool Usage

The **CLOC** (Count Lines of Code) tool can be useful to analyze the codebase and gain an overview of its structure. Use it to count the number of lines and get an overview of the files within the codebase:

```bash
cloc . # Count lines of code in the current directory
cloc <file_or_folder_name> # Specify files or folders to be inspected
````
## 2. Recon

### THE TINCHO METHOD
#### 1. Download the code, use git to clone the repo locally, adapt the environment to whatever you are familiar with (FOUNDRY FOR THE WIN)
#### 2. Read the Documentation, understand the architecture, learn specifc vocabullary and keyboards, other audit reports
#### 3. Code and Contract ranking, use either Solidity Code Metrics or CLOC to get an overview, and rank the contracts based on complexity
#### 4. Start going through the code and TAKE NOTES! either on code, using a plugin or whatever.
#### 5. Write a propper report. 

## 3. Creating the report

### 1. The first step is to document the findings following [Go to Cyfrin Finidng template](CyfrinFindingLayout.md) An example of it would be:
## 1. [H-1] Storing the s_password on chain  makes it visible to anyone and no longer "private"

**Description:**

All data stored on-chain is visible to anyone. Therefore the private `PasswordStore::s_password'` variable which is intended to be private and only readble by owner via `PasswordStore::getPassword` function, is actually readble by everyone.

**Impact:**

 Anyone can read the private password, severly breaking  the functionality of the protocol


**Proof of Concept:**

- Create a locally running chain
`` Make Anvil``
- Deploy the contract to the chain
`` Make Deploy``
- Use the Foundry Storage tool
`` cast storage <ADDRESS_HERE> 1 --rpc-url http://127.0.0.1:8545``
- You will get 
``0x6d7950617373776f726400000000000000000000000000000000000000000014``
- You can then parse it with
``cast parse-bytes32-string 0x6d7950617373776f726400000000000000000000000000000000000000000014``
- And get the output
``myPassword``

**Recommended Mitigation:** 

Due to this, the overall architecture of the contract should be rethought. One could encrypt the password off-chain, and then store the encrypted password on-chain. This would require the user to remember another password off-chain to decrypt the stored password. However, you're also likely want to remove the view function as you wouldn't want the user to accidentally send a transaction with this decryption key.

### 3. Create now the report using the [Cyfrin Report template](report_exmaple.md)

### 4. Create the pdf report using Pandoc and Latex plus the eisvogel.latex


