% CRDT Distributed Safety Net
% Peter Schuck
% Bendyworks

# Overview
* Consistent High Availability
* Guidelines for Distributed Data Structures
* Example CRDT's

# CRDT Like Systems
* CrowdSourcing Estimates (e.g number of Jelly Beans)
* Flocks of birds
* Boarding a airplane (free for all)

# Non CRDT's
* Passage of legislation
* Game of Chess
* Boarding a airplane (by section)

# Bank Example
Deposit, withdraw, transfer, setup new accounts at any local branch or ATM. My local actions are independent of any other bank customers.

# Problem
Maintain a coherent state of the system with out coordination central arbitrator.

# Formal Definition
## Strong Eventual Consistency
