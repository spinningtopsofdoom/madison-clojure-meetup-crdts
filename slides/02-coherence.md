# Eventual Consistency
## Messages (a, b, c)
 a - b - c => r
 b - a - a - c => t
 c - c - b - b - a => r

# Strong Eventual Consistency
## Messages (a, b, c) lead to r
 a - b - c => r
 b - a - a - c => r
 c - c - b - b - a => r

# Network Constraints
"Hello" - "World"
"Hello" - __"World"__
"Hello" ------------------- "World"
"World" - "Hello"
"Hello" - ()
"Hello" - "World" - "World" - "World"

# Constraints
* Handle node failures
* Partitions
* Small messages
* Minimal messages

# Non concerns
## Treat servers like cpu cores
* Maximize efficiency of nodes
