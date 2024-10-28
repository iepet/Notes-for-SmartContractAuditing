# Notes for Smart Contract Auditing

This repository gathers structured notes, tools, and best practices for auditing smart contracts. The repository was created while following guidance from UpdraftCyfrin.

---

## Table of Contents
1. [Scoping](#1-scoping)
2. [Rekt Test Questions](#rekt-test-questions)
3. [CLOC Tool Usage](#cloc-tool-usage)

---

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

