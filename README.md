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

- **Actors, Roles, and Privileges**
  - Do you have all actors, roles, and privileges documented?
- **External Dependencies**
  - Do you keep documentation of all the external services, contracts, and oracles you rely on?
- **Incident Response**
  - Do you have a written and tested incident response plan?
- **Attack Documentation**
  - Do you document the best ways to attack your system?
- **Employee Security Verification**
  - Do you perform identity verification and background checks on all employees?
- **Security-Focused Team Member**
  - Do you have a team member with security defined in their role?
- **Hardware Security Keys**
  - Do you require hardware security keys for production systems?
- **Key Management**
  - Does your key management system require multiple humans and physical steps?
- **System Invariants**
  - Do you define key invariants for your system and test them on every commit?
- **Automated Security Tools**
  - Do you use the best automated tools to discover security issues in your code?
- **External Audits and Bug Bounty**
  - Do you undergo external audits and maintain a vulnerability disclosure or bug bounty program?
- **User Protection**
  - Have you considered and mitigated avenues for abusing users of your system?

### CLOC Tool Usage

The **CLOC** (Count Lines of Code) tool can be useful to analyze the codebase and gain an overview of its structure. Use it to count the number of lines and get an overview of the files within the codebase:

```bash
cloc . # Count lines of code in the current directory
cloc <file_or_folder_name> # Specify files or folders to be inspected
