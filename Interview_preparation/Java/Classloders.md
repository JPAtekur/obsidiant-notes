
## ✅ What is a ClassLoader in Java?

A **ClassLoader** is a part of the **JVM** that loads `.class` files into memory when needed.

It dynamically loads classes **at runtime**, not compile time, enabling features like modular loading, plugin systems, and lazy loading.

---

## ✅ Responsibilities of ClassLoader

1. **Loading** – Reads the `.class` file and loads bytecode into memory.
    
2. **Linking**
    
    - **Verification** – Ensures the bytecode is valid and safe.
        
    - **Preparation** – Allocates memory for static fields.
        
    - **Resolution** – Replaces symbolic references with actual memory addresses.
        
3. **Initialization** – Executes static initializers and blocks.
    

---

## ✅ Types of ClassLoaders

|ClassLoader|Description|
|---|---|
|**Bootstrap ClassLoader**|Loads core Java classes (`rt.jar`, `java.lang.*`, `java.util.*`)|
|**Extension ClassLoader**|Loads classes from `JAVA_HOME/lib/ext`|
|**Application (System) ClassLoader**|Loads classes from the application classpath|
|**Custom/User-defined ClassLoader**|You can create your own by extending `ClassLoader`|

---

### 🔁 ClassLoader Hierarchy

Java follows the **Parent Delegation Model**:

```
          Bootstrap ClassLoader (null in code)
                   ↓
       
```