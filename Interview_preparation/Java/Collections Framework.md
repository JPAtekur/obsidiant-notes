The **Java Collections Framework (JCF)** is a set of interfaces and classes to store, manipulate, and retrieve groups of objects efficiently.

✅ **Core Interfaces**

|Interface|Description|
|---|---|
|**Collection**|Root of the collection hierarchy (List, Set, Queue extend it)|
|**List**|Ordered collection, allows duplicates|
|**Set**|Unordered collection, no duplicates|
|**Queue**|FIFO data structure|
|**Deque**|Double-ended queue|
|**Map** (not part of Collection)|Key-value pairs, no duplicate keys|
✅ **List Implementations**

| Class          | Features                                                   |
| -------------- | ---------------------------------------------------------- |
| **ArrayList**  | Dynamic array, fast for get/set, slower for insert/remove  |
| **LinkedList** | Doubly linked list, fast for insert/delete, slower get/set |
| **Vector**     | Synchronized, legacy                                       |
| **Stack**      | LIFO stack, extends Vector (use Deque instead now)         |

✅ **Set Implementations**

|Class|Features|
|---|---|
|**HashSet**|No duplicates, unordered, backed by HashMap|
|**LinkedHashSet**|Preserves insertion order|
|**TreeSet**|Sorted (natural or custom order), uses TreeMap (Red-Black tree)|

✅ **Map Implementations**

|Class|Features|
|---|---|
|**HashMap**|Unordered key-value pairs, allows one null key|
|**LinkedHashMap**|Maintains insertion order|
|**TreeMap**|Sorted by keys, no null keys|
|**Hashtable**|Synchronized, legacy|
|**ConcurrentHashMap**|Thread-safe, modern concurrent map|

## ✅ **10. Common Interview Questions**

### 🔸 What is the difference between `ArrayList` and `LinkedList`?

- `ArrayList`: Better for get/set
    
- `LinkedList`: Better for add/remove at middle or beginning
    

### 🔸 HashMap vs Hashtable?

- `HashMap` is unsynchronized and allows nulls; `Hashtable` is synchronized and does not allow null.
### 🔸 HashSet vs TreeSet?

- `HashSet` is unordered, faster; `TreeSet` is ordered, slower, and uses a comparator.
### 🔸 How does `HashMap` work internally?

- Uses hashCode + equals to determine bucket and key match
    
- Uses array of linked lists or red-black trees for collision handling
    

### 🔸 Fail-fast vs fail-safe iterators?

- **Fail-fast** (e.g., `ArrayList.iterator()`) throws `ConcurrentModificationException`
    
- **Fail-safe** (e.g., `CopyOnWriteArrayList`) avoids the error but may not reflect latest changes