
### **1. What is a Data Structure?**  
**Answer:** A data structure is a way of organizing and storing data in a computer so that it can be accessed and modified efficiently. Examples include arrays, linked lists, stacks, queues, trees, and graphs.

### **2. What is the difference between an Array and a Linked List?**  
**Answer:**  
- **Array:** Elements are stored in contiguous memory locations; fixed size; random access is allowed (O(1)).  
- **Linked List:** Elements are stored non-contiguously with pointers; dynamic size; sequential access (O(n)).  

### **3. Explain Stack and Queue with real-life examples.**  
**Answer:**  
- **Stack:** Follows LIFO (Last In First Out). Example: Browser back button.  
- **Queue:** Follows FIFO (First In First Out). Example: Printer job scheduling.  

### **4. What is a Binary Tree?**  
**Answer:** A tree data structure where each node has at most two children, referred to as the left child and right child.  

### **5. What is a Binary Search Tree (BST)?**  
**Answer:** A BST is a binary tree where:  
- The left subtree contains nodes with keys less than the root.  
- The right subtree contains nodes with keys greater than the root.  
- No duplicate keys are allowed.  

### **6. What is the time complexity of searching in a BST?**  
**Answer:**  
- **Average Case:** O(log n) (balanced BST).  
- **Worst Case:** O(n) (skewed tree).  

### **7. What is a Hash Table? How does it work?**  
**Answer:** A hash table stores key-value pairs using a hash function to compute an index into an array of buckets. Collisions can be resolved using chaining or open addressing.  

### **8. What is the time complexity of insert, delete, and search in a Hash Table?**  
**Answer:**  
- **Average Case:** O(1).  
- **Worst Case:** O(n) (if all keys collide).  

### **9. What is a Heap?**  
**Answer:** A heap is a complete binary tree where:  
- **Min-Heap:** Parent node ≤ children.  
- **Max-Heap:** Parent node ≥ children.  
Used in priority queues and heap sort.  

### **10. What is the difference between BFS and DFS?**  
**Answer:**  
- **BFS (Breadth-First Search):** Uses a queue, explores level by level.  
- **DFS (Depth-First Search):** Uses a stack (or recursion), explores as far as possible before backtracking.  

### **11. What is a Graph? How is it represented?**  
**Answer:** A graph consists of vertices (nodes) and edges (connections). Representations:  
- **Adjacency Matrix:** 2D array where `matrix[i][j] = 1` if edge exists.  
- **Adjacency List:** Array of linked lists.  

### **12. What is Dynamic Programming?**  
**Answer:** A method for solving complex problems by breaking them into simpler subproblems, storing solutions to avoid recomputation (memoization or tabulation).  

### **13. What is the difference between Quick Sort and Merge Sort?**  
**Answer:**  
- **Quick Sort:** In-place, average O(n log n), worst O(n²) (if pivot is bad).  
- **Merge Sort:** Not in-place, always O(n log n), stable.  

### **14. How does Dijkstra’s Algorithm work?**  
**Answer:** Finds the shortest path from a source node to all other nodes in a weighted graph (non-negative edges) using a priority queue.  

### **15. What is a Trie?**  
**Answer:** A tree-like data structure for storing strings, where each node represents a character. Used in autocomplete and spell-checking.  

### **16. What is the time complexity of inserting into a linked list?**  
**Answer:**  
- **At head:** O(1).  
- **At tail (if tail pointer is maintained):** O(1).  
- **At arbitrary position:** O(n).  

### **17. What is an AVL Tree?**  
**Answer:** A self-balancing BST where the heights of left and right subtrees differ by at most 1. Ensures O(log n) search, insert, and delete.  

### **18. What is the difference between ArrayList and LinkedList in Java?**  
**Answer:**  
- **ArrayList:** Dynamic array, O(1) random access, slower insert/delete in the middle.  
- **LinkedList:** Doubly linked list, O(n) random access, faster insert/delete.  

### **19. What is a Circular Linked List?**  
**Answer:** A linked list where the last node points back to the first node, forming a loop.  

### **20. Explain the concept of Collision in Hashing.**  
**Answer:** When two different keys hash to the same index in a hash table. Resolved by:  
- **Chaining:** Store collided keys in a linked list.  
- **Open Addressing:** Find next available slot (linear probing, quadratic probing).  

### **21. What is the difference between a Singly Linked List and a Doubly Linked List?**  
**Answer:**  
- **Singly Linked List:** Each node has a `data` field and a `next` pointer (points to the next node). Traversal is only forward.  
- **Doubly Linked List:** Each node has `data`, `next`, and `prev` (points to the previous node). Allows traversal in both directions.  

---

### **22. How do you detect a cycle in a Linked List?**  
**Answer:**  
- **Floyd’s Cycle-Finding Algorithm (Tortoise and Hare):**  
  - Use two pointers, `slow` (moves 1 step) and `fast` (moves 2 steps).  
  - If they meet, a cycle exists.  
  - Time: **O(n)**, Space: **O(1)**.  

---

### **23. What is the time complexity of inserting an element in the middle of an Array vs. a Linked List?**  
**Answer:**  
- **Array:** **O(n)** (shifting required).  
- **Linked List:** **O(1)** (if node is given, else **O(n)** to traverse).  

---

### **24. What is a Priority Queue? How is it implemented?**  
**Answer:**  
- A queue where elements are dequeued based on priority (not just FIFO).  
- **Implementation:**  
  - **Heap (Binary/Min-Heap/Max-Heap):** O(log n) insertion/deletion.  
  - **Unordered Array:** O(1) insert, O(n) delete.  

---

### **25. What is the difference between a Stack and a Heap memory?**  
**Answer:**  
| **Stack** | **Heap** |  
|-----------|---------|  
| Fixed size, fast access | Dynamic size, slower access |  
| Stores local variables, function calls | Stores dynamically allocated memory (`malloc`, `new`) |  
| Memory managed automatically (LIFO) | Memory managed manually (or via GC) |  

---

### **26. What is a Red-Black Tree?**  
**Answer:**  
- A self-balancing BST with properties:  
  1. Every node is **RED** or **BLACK**.  
  2. Root and leaves (NULL) are **BLACK**.  
  3. No two consecutive **RED** nodes.  
  4. Every path from root to leaf has the same number of **BLACK** nodes.  
- Ensures **O(log n)** operations.  

---

### **27. What is the time complexity of Bubble Sort?**  
**Answer:**  
- **Best Case:** O(n) (already sorted).  
- **Average & Worst Case:** O(n²).  

---

### **28. What is the difference between Open Addressing and Chaining in Hash Tables?**  
**Answer:**  
| **Chaining** | **Open Addressing** |  
|--------------|---------------------|  
| Uses linked lists for collisions | Finds next available slot |  
| No clustering issues | Suffers from clustering |  
| Extra memory for pointers | No extra memory |  

---

### **29. What is a B-Tree? Where is it used?**  
**Answer:**  
- A self-balancing tree where each node can have **> 2 children**.  
- Used in **databases & file systems** (e.g., MySQL InnoDB).  
- Ensures **O(log n)** operations even on disk.  

---

### **30. How does LRU (Least Recently Used) Cache work?**  
**Answer:**  
- Uses a **Hash Map + Doubly Linked List** for O(1) access and O(1) eviction.  
- When cache is full, the least recently used item is removed.  

---

### **31. What is the difference between In-order, Pre-order, and Post-order traversal?**  
**Answer:**  
| **Traversal** | **Order** |  
|--------------|----------|  
| **In-order** | Left → Root → Right |  
| **Pre-order** | Root → Left → Right |  
| **Post-order** | Left → Right → Root |  

---

### **32. What is a Segment Tree?**  
**Answer:**  
- A tree used for **range queries** (e.g., sum/min/max in a subarray).  
- **Build Time:** O(n).  
- **Query Time:** O(log n).  

---

### **33. What is the time complexity of DFS and BFS on a Graph?**  
**Answer:**  
- **DFS:** O(V + E) (Vertices + Edges).  
- **BFS:** O(V + E).  

---

### **34. What is the difference between a Tree and a Graph?**  
**Answer:**  
| **Tree** | **Graph** |  
|----------|----------|  
| No cycles | Can have cycles |  
| Hierarchical | No hierarchy |  
| n nodes → n-1 edges | No fixed edge rule |  

---

### **35. What is a Disjoint Set (Union-Find)?**  
**Answer:**  
- A data structure for tracking partitions (used in Kruskal’s MST algorithm).  
- Supports:  
  - **Union:** Merge two sets.  
  - **Find:** Check which set an element belongs to.  

---

### **36. What is the time complexity of Heapify?**  
**Answer:**  
- **O(n)** (building a heap from an unsorted array).  

---

### **37. What is a Skip List?**  
**Answer:**  
- A probabilistic data structure with multiple layers of linked lists for **O(log n)** search.  
- Used in Redis and MemSQL.  

---

### **38. What is the difference between Prim’s and Kruskal’s MST algorithms?**  
**Answer:**  
| **Prim’s** | **Kruskal’s** |  
|------------|--------------|  
| Greedy (starts from a node) | Greedy (sorts all edges) |  
| Uses Priority Queue | Uses Disjoint Set |  
| O(E log V) | O(E log E) |  

---

### **39. What is a Suffix Tree?**  
**Answer:**  
- A compressed trie storing all suffixes of a string.  
- Used in **pattern searching (O(m) time)**.  

---

### **40. How do you reverse a Linked List?**  
**Answer:**  
**Iterative Approach:**  
```python
prev = None  
current = head  
while current:  
    next_node = current.next  
    current.next = prev  
    prev = current  
    current = next_node  
return prev  
```
**Time: O(n), Space: O(1).**  

---

### **Bonus: Top 5 Data Structures Asked in Interviews**  
1. **Arrays & Strings** (Manipulation, Sliding Window).  
2. **Linked Lists** (Cycle detection, Reversal).  
3. **Trees & Graphs** (DFS/BFS, BST, Heaps).  
4. **Hash Tables** (Collision handling).  
5. **Stacks & Queues** (Monotonic stacks, Priority Queues).  

Would you like **coding problems** related to these? Let me know! 🚀