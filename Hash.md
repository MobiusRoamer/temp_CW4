# Hashing
Why do we need hashing? Suppose we want to store a large amount of data strings into a a database. 
For example, each string is a unique identifier, like an URL for a web address. We need to save these data 
and lookup an arbitrary element fast enough to answer the users' query efficiently. 

Of course a brute force solution would be to store each piece information exactly as it is. 
This works for small datasets, but the linearity in lookups, whereby one needs to iterate through all elements, one by one,
can depreciate the efficiency quickly as the size of the database grows, the search time is typically $O(n)$. 
Hashing provides a $O(1)$ solution. Examples of this method are `HashMap` and `HashSet` which is implemented using a HashMap 
preserving only keys but ignoring the values. A hashMap can locate an element quickly by: 
Compute hash -> locate bucket in hashTable -> compare using `equals()` in the table. 

The idea is as follows: given an input, use its identifying information to create a hashCode as defined by a hashFunction, then 
use the code as the key to store the value in a hashTable. HashTable is an array of buckets to store items with head of the table
defined by the unique hashCodes. 

**A motivating question is what makes a good hash function.** Common desirable features are
1. Evenly distributes results.

2. Sensitive to small input changes.

3. Fast to compute.

4. Must satisfy: if a.equals(b) then a.hashCode()==b.hashCode() 
## HashFunction
A typical hash function first converts a search key into an integer value called a hash code then compresses the hash code into an index to the hash table
The general hashCode cookbook process is
1. Override the hashCode method whenever equals method is overridden to ensure two equal objects return the same
hash code
2. During the execution of a program, invoking the hashCode method multiple times returns the same integer if the object's data has not changed
3. Two equal objects may have the same hashCode but the method should avoid too many surjections (multiple objects mapping to a common hashCode)
## Collisions
Collisions refer to when two keys are mapped to the same index in a hash table. 
They can be handled using open addressing & separate chaining
### Open addressing:
1. Linear probing one method treats the hasTable as circular, take modulo N where N is the size of the table
 to find the next available unoccupied hashTable keyholder.
The index of a key in the hashTable is key % N . 
The search goes from `hashTable[k % N]` to `hashTable[(k+1) % N]` etc, stops upon first unoccupied key

2. Quadratic probing as open addressing sidesteps the clustering problem in linear probing by searching for
`[(k + j^2) % N]`, hence one searches for `[k % N], [k + 1 % N], [k + 4 % N]`, etc. But quadratic probing has its 
own secondary clustering problem: if two probings sequences agree somewhere, they agree everywhere again leading to surjections

### Separate Chaining:
Entries with the same hash index are placed in the same bucket. The common approach is using `equals` to distinguish between
 objects in the same bucket. 

## LoadFactor_Rehash
Load factor measures the proportion of positions already filled with keys in the hashTable. Keeping a threshold below 1, one 
may increase the hashTable size whenever the threshold is met. Increasing the table size is called rehashing. 

## HashSet_HashMap
HashSet: implements java set interface, duplicate values allowed but no duplicate keys allowed. No guarantee of retrieval by
 any particular order so search requires iterating through all elements leading to $O(n)$

HashMap: implements java map interface, duplicates not allowed for both keys and values. Loops up items based on keys so 
search complexity tightens to $O(1)$, direct retrieval. 
