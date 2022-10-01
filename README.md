# Chord Protocol - A Distributed Hash Table

What is Chord?
* In short: a peer-to-peer lookup service
* Solves problem of locating a data item in a collection of distributed nodes, considering frequent node arrivals and departures
* Core operation in most p2p systems is efficient location of data items
* Supports just one operation: given a key, it maps the key onto a node
# The Base Chord Protocol
* Specifies how to find the locations of keys
* How new nodes join the system
* How to recover from the failure or planned departure of existing nodes
# Chord characteristics 
* Simplicity, provable correctness, and provable performance
* Each Chord node needs routing information about only a few other nodes
* Resolves lookups via messages to other nodes (iteratively or recursively)
* Maintains routing information as nodes join and leave the system

# Addressed Difficult Problems
* Load balance: distributed hash function, spreading keys evenly over nodes
* Decentralization: chord is fully distributed, no node more important than other, improves robustness
* Scalability: logarithmic growth of lookup costs with number of nodes in network, even very large systems are feasible
* Availability: chord automatically adjusts its internal tables to ensure that the node responsible for a key can always be found
* Flexible naming: no constraints on the structure of the keys – key-space is flat, flexibility in how to map names to Chord keys
# The Chord algorithm –
Construction of the Chord ring
* Hash function assigns each node and key an m-bit identifier using a base hash function such as SHA-1
* ID(node) = hash(IP, Port)
* ID(key) = hash(key)
* Both are uniformly distributed
* Both exist in the same ID space 
 Properties of consistent hashing:
* Function balances load: all nodes receive roughly the same number of keys – good?
* When an Nth node joins (or leaves) the network, only an O(1/N) fraction of the keys are moved to a different location
* Identifiers are arranged on a identifier circle modulo 2    =>  Chord ring
* A key k is assigned to the node whose identifier is equal to or greater than the key‘s identifier
* This node is called successor(k) and is the first node clockwise  from k.
# The Chord algorithm –
Simple node localization
// ask node n to find the successor of id

~~~bash
n.find_successor(id)
   if (id    (n; successor])
       return successor;
   else
       // forward the query around the
          circle
     return successor.find_successor(id);
~~~     
# The Chord algorithm –
Scalable node localization
* Additional routing information to accelerate lookups
* Each node n contains a routing table with up to m entries (m: number of bits of the identifiers) => finger table
* i entry in the table at node n contains the first node s that succeds n by at least 2
* s = successor (n + 2    )
* s is called the i    finger of node n

Important characteristics of this scheme:
* Each node stores information about only a small number of nodes (m)
* Each nodes knows more about nodes closely following it than about nodes farer away
* A finger table generally does not contain enough information to directly determine the successor of an arbitrary key k


# The Chord algorithm –
Node joins and stabilization
* To ensure correct lookups, all successor pointers must be up to date
* => stabilization protocol running periodically in the background
* Updates finger tables and successor pointers
Stabilization protocol:
* Stabilize(): n asks its successor for its predecessor p and decides whether p should be n‘s successor instead (this is the case if p recently joined the system).
* Notify(): notifies n‘s successor of its existence, so it can change its predecessor to n
* Fix_fingers(): updates finger tables

