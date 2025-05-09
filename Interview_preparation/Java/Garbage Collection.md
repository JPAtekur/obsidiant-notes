
## ✅ What is Garbage Collection in Java?

**Garbage Collection (GC)** is an automatic memory management process in Java. It **frees up memory** by removing objects that are **no longer reachable** in the program.

> You don’t need to manually deallocate memory like in C/C++.

---

## ✅ Why Is GC Important?

- Prevents **memory leaks**
    
- Improves **application performance**
    
- Ensures efficient use of **heap memory**
    

---

## ✅ How Does Java GC Work?

1. **Object Creation** → Heap memory
    
2. **Reference Check** → GC checks if the object is still accessible
    
3. **Reachability Analysis** → If unreachable, it's marked for GC
    
4. **Garbage Collector** runs in the background and removes unused objects
    

---

## ✅ Java Memory Areas Related to GC

|Area|Description|
|---|---|
|**Young Generation**|Newly created short-lived objects (Eden + Survivor)|
|**Old Generation**|Long-lived objects|
|**Metaspace**|Class metadata (since Java 8)|

---

### 🔁 Young Generation

- **Eden Space** → New objects allocated
    
- **Survivor Spaces (S0 & S1)** → Surviving objects are copied between them
    

### 🔁 Old Generation

- Stores objects that survived several GC cycles
    

---

## ✅ Types of GC in Java

### 1. **Serial GC (`-XX:+UseSerialGC`)**

- Single-threaded
    
- Best for small applications
    
- **Stop-the-world** events
    

### 2. **Parallel GC (`-XX:+UseParallelGC`)**

- Multi-threaded, high throughput
    
- Good for multi-core systems
    

### 3. **Concurrent Mark Sweep (CMS) (`-XX:+UseConcMarkSweepGC`)**

- Reduces pause times
    
- Concurrent phases; deprecated since Java 9
    

### 4. **G1 GC (Garbage First) (`-XX:+UseG1GC`)**

- Default since Java 9
    
- Heap is divided into regions (not fixed generations)
    
- Predictable low pause times
    

### 5. **ZGC (`-XX:+UseZGC`) and Shenandoah**

- Very low latency
    
- Designed for large heaps (up to TBs)
    
- Mostly concurrent and scalable
    

---

## ✅ GC Phases (Generalized)

1. **Mark** → Identify reachable objects
    
2. **Sweep** → Remove unreachable objects
    
3. **Compact** → Optional: reduce fragmentation (done in some GCs)
    
4. **Promotion** → Move long-lived objects to Old Gen
    

---

## ✅ JVM GC Tuning Parameters (Examples)

```bash
-Xms512m                  # Initial heap size
-Xmx1024m                 # Max heap size
-XX:+UseG1GC              # Use G1 Garbage Collector
-XX:+PrintGCDetails       # GC logging
-XX:MaxGCPauseMillis=200  # Desired max pause time
```

---

## ✅ Interview Questions & Answers

### Q1. **How does GC know an object is unused?**

Java uses **reachability analysis**. If no live thread or static reference points to an object, it’s considered unreachable and GC eligible.

---

### Q2. **What is Stop-the-World (STW)?**

A **STW event** pauses all application threads during GC so the JVM can reclaim memory safely.

---

### Q3. **What is the difference between Minor GC and Major GC?**

- **Minor GC:** Cleans Young Generation (short-lived objects)
    
- **Major GC (or Full GC):** Cleans Old Generation and Young Generation, often more expensive
    

---

### Q4. **What is the difference between G1 and CMS GC?**

|Feature|CMS|G1 GC|
|---|---|---|
|Default?|❌|✅ Java 9+|
|Fragmentation|Can occur|Compacting GC|
|Regions|Generational|Region-based|
|Pauses|Lower than Serial/Parallel|Predictable and tunable|

---

### Q5. **Can GC cause performance problems?**

Yes, frequent or long GC pauses (especially Full GC) can affect performance. Tuning GC and heap sizes is crucial for large-scale systems.

---

## ✅ Tools for GC Monitoring

- **VisualVM / JConsole / JMC**
    
- **GC logs** (`-Xlog:gc*` in Java 9+)
    
- **Heap dumps** (`jmap`, `MAT`)
    
- **`jstat` and `jcmd`** commands
    
