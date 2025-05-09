https://www.tpointtech.com/difference-between-jdk-jre-and-jvm

### **1. What is the difference between JVM, JDK, and JRE?**

- **JVM (Java Virtual Machine):** Executes Java bytecode.
    
- **JRE (Java Runtime Environment):** JVM + libraries to run Java programs.
    
- **JDK (Java Development Kit):** JRE + compilers and tools to develop Java applications.
    

---

### **2. How does the JVM handle memory management?**

JVM divides memory into different runtime areas (heap, stack, metaspace, etc.) and uses **Garbage Collection** to reclaim memory from unreachable objects.

---

### **3. What are different memory areas in JVM?**

- **Heap:** Stores objects and class instances.
    
- **Stack:** Stores method calls, local variables (one per thread).
    
- **Method Area (part of Metaspace in Java 8+):** Class metadata, static variables.
    
- **Program Counter (PC) Register:** Tracks current bytecode instruction.
    
- **Native Method Stack:** For native method executions.
    

---

### **4. Explain the GC process in JVM.**

GC identifies unreachable objects and reclaims memory. It works in generations:

- **Young Generation (Eden + Survivor):** Short-lived objects.
    
- **Old Generation:** Long-lived objects.
    
- **Metaspace:** Class metadata (Java 8+).
    

GC algorithms include Serial, Parallel, CMS, G1, ZGC, etc.

---

### **5. What is the JIT compiler?**

**Just-In-Time (JIT)** compiler compiles frequently executed bytecode into native machine code at runtime to improve performance. It works with the interpreter in the execution engine.

---

### **6. What is the difference between stack and heap memory?**

|Stack Memory|Heap Memory|
|---|---|
|Stores method calls, local variables|Stores objects and instance data|
|Thread-specific|Shared across all threads|
|Faster|Slower|
|LIFO structure|Managed via GC|

---

### **7. How does class loading work in JVM?**

The **ClassLoader Subsystem** loads, links, and initializes classes using:

- **Bootstrap ClassLoader** → loads core Java classes
    
- **Extension ClassLoader** → loads JDK extension libraries
    
- **Application ClassLoader** → loads user-defined classes
    

It follows **parent delegation model** to avoid class conflicts.

---

### **8. What are the different GC algorithms?**

- **Serial GC:** Single-threaded; best for small applications.
    
- **Parallel GC:** Multi-threaded; for high-throughput systems.
    
- **CMS (Concurrent Mark Sweep):** Low-pause GC; deprecated.
    
- **G1 GC (Garbage First):** Splits heap into regions; balances throughput and latency.
    
- **ZGC/Shenandoah:** Designed for low-latency, large heaps.
    

---

### **9. How can you monitor JVM performance?**

- **JVM Tools:** `jconsole`, `jvisualvm`, `jstat`, `jstack`, `jmap`, `jcmd`
    
- **Logs:** GC logs, heap dumps, thread dumps
    
- **Flags:** `-XX:+PrintGCDetails`, `-Xmx`, `-Xms`
    
- **Profilers:** JProfiler, YourKit, Flight Recorder