## 1. Lambda Expression
### What:
Introduced functional programming to Java
Anonymous functions — a concise way to pass behavior.
Lambda expressions can only be used with **functional interfaces**—interfaces that have exactly **one abstract method**. Example: `Runnable`, `Comparator`, `Callable`, `Function`, `Predicate`, etc.

### Syntax:
```
(parameters) -> expression
```
### Example:
```
List<String> names = Arrays.asList("John", "Alice", "Bob");
names.sort((a, b) -> a.compareToIgnoreCase(b));
```

## 2. Functional Interfaces (Java 8)

### What:
A **functional interface** in Java is an **interface with exactly one abstract method**. It can have multiple **default or static methods**, but only one method must be abstract.  
This single abstract method can be implemented using a **lambda expression** or a method reference.

### Example

```
@FunctionalInterface
interface MyPrinter {
    void print(String message);
}
```
```
MyPrinter printer = message -> System.out.println(message);
printer.print("Hello!");
```

Java provides common ones: `Predicate`, `Function`, `Consumer`, `Supplier`

Common Built-in Functional Interfaces (from `java.util.function`):

|Interface|Abstract Method|Use Case|
|---|---|---|
|`Predicate<T>`|`boolean test(T t)`|Conditional checks (filtering).|
|`Function<T, R>`|`R apply(T t)`|Transforming data.|
|`Consumer<T>`|`void accept(T t)`|Performing actions (printing, saving, etc).|
|`Supplier<T>`|`T get()`|Supplying values (e.g., factory).|
|`BiFunction<T,U,R>`|`R apply(T t, U u)`|Function with two inputs.|

## 3. Stream API (Java 8)
The **Streams API**, introduced in **Java 8**, provides a **declarative and functional approach** to processing collections (like `List`, `Set`). It allows you to **perform complex data manipulations (filtering, mapping, sorting, etc.)** using a fluent, readable pipeline of operations.

### 🔁 Streams Workflow (Pipeline):

1. **Source** – A collection or array to be processed.
    
2. **Intermediate Operations** – Transformations (e.g., `filter`, `map`, `sorted`) that return a new stream.
    
3. **Terminal Operation** – Triggers processing and returns a result (e.g., `collect`, `forEach`, `count`).

### Basic Example:
```
List<String> names = List.of("John", "Jane", "Jack", "Tom");

names.stream()
     .filter(name -> name.startsWith("J"))   // Intermediate
     .map(String::toUpperCase)               // Intermediate
     .forEach(System.out::println);          // Terminal
```
### 🌟 Benefits of Streams API:

|Benefit|Description|
|---|---|
|**Clean & Concise**|Less code compared to loops.|
|**Declarative Style**|Focus on _what_ to do, not _how_.|
|**Easy Parallelism**|Use `.parallelStream()` for multithreading.|
|**Immutable Data**|Doesn't modify the original collection.|
|**Chainable Operations**|Fluent API allows chaining multiple operations.|

## 4. Optional (Java 8)
`Optional<T>` is a **container object** introduced in **Java 8** that may or may not contain a non-null value. It is used to **avoid `NullPointerException`** and to **make the presence or absence of a value explicit**.

### Why `Optional` is Useful

- Replaces returning `null` to indicate absence of a value.
    
- Encourages better handling of potentially missing values.
    
- Improves code readability and safety.

### Safe Handling of Nulls:
```
Optional<String> empty = Optional.empty();  Optional<String> name = Optional.ofNullable(null); // Safe way to wrap a null
```

### Common Methods:

|Method|Description|
|---|---|
|`isPresent()`|Returns `true` if a value is present|
|`ifPresent(Consumer)`|Executes code if value is present|
|`orElse(T)`|Returns value or default if empty|
|`orElseGet(Supplier)`|Lazy default value|
|`orElseThrow()`|Throws exception if empty|
|`map(Function)`|Transforms the value|
|`flatMap(Function)`|Flattens nested Optionals|
|`filter(Predicate)`|Returns value if matches condition|
### When _Not_ to Use Optional:

- Don't use `Optional` in **fields** or **method parameters** (only for return types).
    
- Don't use it for **serialization or large-scale performance-sensitive code**.

Avoiding Null Checks:
```
String name = Optional.ofNullable(user.getName())
                      .orElse("Default Name");

```
### Benefits of Using `Optional`:

|Benefit|Description|
|---|---|
|**Avoids `NullPointerException`**|Eliminates risky null references.|
|**Expressive API**|Clearly communicates intent.|
|**Safer Code**|Forces handling of missing values.|
|**Functional Style**|Combines well with lambdas and streams.|

## 5. Default and Static Methods in Interfaces (Java 8)
Java 8 introduced **`default`** and **`static`** methods in interfaces to support **backward compatibility** and **functional programming** without breaking existing implementations.

### Why Introduced?

- Allow interfaces to have **method implementations**.
- Enable adding **new methods to interfaces** without affecting existing classes.
- Support **lambda expressions** and **functional-style APIs** (like Streams).
### Default Methods

- Declared with the `default` keyword inside an interface.
- Provide a **default implementation**.
- Can be **overridden** by implementing classes.

#### ✅ Syntax:

#### 🔸 Example:
```
interface MyInterface {
    default void show() {
        System.out.println("Default show()");
    }
}
```
Example:
```
interface Vehicle {
    default void start() {
        System.out.println("Starting vehicle...");
    }
}

class Car implements Vehicle {
    // Inherits start() by default
}
```

### Static Methods

- Declared with the `static` keyword inside an interface.
- Belong to the interface **itself**, not to any instance.
- **Cannot be overridden**.
- Accessed using `InterfaceName.methodName()`.

✅ Syntax:
```
interface Utility {
    static int add(int a, int b) {
        return a + b;
    }
}
```

### ⚠ Important Notes:

- A class implementing multiple interfaces with the **same default method** must **override it** to resolve the conflict.
#### Example Conflict:
```
interface A {
    default void greet() { System.out.println("Hello from A"); }
}

interface B {
    default void greet() { System.out.println("Hello from B"); }
}

class C implements A, B {
    public void greet() {
        A.super.greet(); // or custom implementation
    }
}
```


## 6. Method References (Java 8)
**Method references** are a shorthand syntax for calling methods through **lambda expressions**. They enhance **readability** and **reduce boilerplate code** when you're just calling a method directly in a lambda.

✅ Syntax:
```
ClassName::methodName
```
### 🧩 Types of Method References

| Type                                  | Example                     | Description                                                                   |
| ------------------------------------- | --------------------------- | ----------------------------------------------------------------------------- |
| 1. **Static method**                  | `ClassName::staticMethod`   | Refers to a static method.                                                    |
| 2. **Instance method (of an object)** | `instance::instanceMethod`  | Calls a method on a specific object.                                          |
| 3. **Instance method (of a class)**   | `ClassName::instanceMethod` | Java figures out the object. Used when the method is called on the parameter. |
| 4. **Constructor reference**          | `ClassName::new`            | Creates a new object.                                                         |

## 7. New Date and Time API (Java 8)
**`java.time` package replaces legacy `Date`/`Calendar`**
### 🔹 Example:
```
LocalDate today = LocalDate.now();
LocalDate nextWeek = today.plusWeeks(1);
```
### 🔹 Use Case:

- Thread-safe date/time handling
- Immutability, formatting, parsing made easier

## 8. Streams – Parallel Processing (Java 8)
**Parallel Streams** allow Java to process data **concurrently** using multiple threads — improving performance for large data sets on multicore processors. It’s a feature of the **Streams API** introduced in Java 8.

### ✅ What is a Parallel Stream?

A **parallel stream** splits the data into multiple **substreams**, processes them in **parallel**, and then combines the results.
```
List<String> names = List.of("Alice", "Bob", "Charlie", "David");

names.parallelStream()
     .map(String::toUpperCase)
     .forEach(System.out::println);
```
⚠️ `forEach` in parallel streams may print results in **non-deterministic order**.

### 🧪 Example: Sum of Squares

#### ✅ Sequential:
```
long sum = IntStream.range(1, 1000000)
                    .map(x -> x * x)
                    .sum();
```
✅ Parallel:
```
long sum = IntStream.range(1, 1000000)
                    .parallel()
                    .map(x -> x * x)
                    .sum();
```

## 9. Try-With-Resources Enhancement (Java 9+)
**Try-with-resources** is a feature introduced in **Java 7** (and improved in Java 9) that automatically **closes resources** like files, streams, sockets, etc., when they are no longer needed — avoiding **resource leaks**.

✅ Basic Syntax
```
try (ResourceType resource = new ResourceType()) {
    // use the resource
} catch (Exception e) {
    e.printStackTrace();
}
```
The resource must implement the **`AutoCloseable`** interface (or `Closeable` for IO classes).

🧪 Example: Reading a File
✅ Traditional Try-Finally (Before Java 7):
```
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("file.txt"));
    System.out.println(reader.readLine());
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

✅ Try-With-Resources (Java 7+):
```
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    System.out.println(reader.readLine());
} catch (IOException e) {
    e.printStackTrace();
}
```

✅ Custom Resource with `AutoCloseable`
```
class MyResource implements AutoCloseable {
    public void doSomething() {
        System.out.println("Doing something...");
    }

    @Override
    public void close() {
        System.out.println("Closing resource...");
    }
}

try (MyResource res = new MyResource()) {
    res.doSomething();
}
```
Output:
```
Doing something...
Closing resource...
```

Java 9 introduced a **minor but powerful enhancement** to try-with-resources:  
You can now use **effectively final** resources **declared outside** the `try` block.
#### 🔸 **Java 7/8 Limitation:**

You **had to declare the resource inside** the `try()` statement:
```
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    // use br
}
```
### ✅ Java 9 Improvement:

Now, in **Java 9**, you can declare the resource **before the try block**, and still use it:
```
BufferedReader br = new BufferedReader(new FileReader("file.txt"));
try (br) {
    System.out.println(br.readLine());
}
```


## 10. `var` keyword (Java 10)

Java 10 introduced the `var` keyword to allow **local variable type inference** — meaning the compiler can infer the type based on the assigned value.

✅ Basic Syntax:
```
var name = "Alice";        // inferred as String
var count = 10;            // inferred as int
var list = new ArrayList<String>(); // inferred as ArrayList<String>
```

⚠️ `var` can **only** be used for **local variables**, **index variables in loops**, and **`try` resources** — **not** for method parameters, return types, fields, or class members.

## 11. Records (Java 16/17)

A **record** is a special type of class introduced in **Java 14 (preview)** and finalized in **Java 16**, designed to model **immutable data objects** with **minimal boilerplate**.

✅ Basic Syntax
```
public record Person(String name, int age) { }
```
This one line is equivalent to:
```
public final class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) { 
        this.name = name; 
        this.age = age; 
    }

    public String name() { return name; }
    public int age() { return age; }

    @Override
    public boolean equals(Object o) { ... }

    @Override
    public int hashCode() { ... }

    @Override
    public String toString() { 
        return "Person[name=" + name + ", age=" + age + "]";
    }
}
```

📦 Features of Records

|Feature|Description|
|---|---|
|`final` and immutable|Fields are implicitly `private final`|
|Auto-generated methods|Constructor, `getters`, `equals()`, `hashCode()`, and `toString()`|
|Cannot extend other classes|But can implement interfaces|
|Compact and clean|Ideal for **DTOs**, **value objects**, etc.|

🧪 Example
```
public record Product(String id, String name, double price) { }

public class Main {
    public static void main(String[] args) {
        Product p = new Product("P101", "Mouse", 29.99);

        System.out.println(p.name());       // Mouse
        System.out.println(p);              // Product[id=P101, name=Mouse, price=29.99]
    }
}
```

### 🛠 Custom Methods and Validation

You can add methods or compact constructors:
```
public record User(String username, String email) {
    public User {
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }

    public String domain() {
        return email.substring(email.indexOf("@") + 1);
    }
}
```

🔒 Limitations:

| Limitation                  | Why                                                |
| --------------------------- | -------------------------------------------------- |
| Cannot be abstract          | Records are implicitly final                       |
| No mutable fields           | All fields are final                               |
| No inheritance              | Cannot extend any class                            |
| Field names are part of API | Cannot rename later without breaking compatibility |
### 🌟 Use Cases

- **DTOs** (Data Transfer Objects)
- **API responses**
- **Event objects** in event-driven systems
- **Immutable configurations**
- **Model classes** for streams or functional programming

## 12. Sealed Classes (Java 17)

**Sealed classes** (finalized in **Java 17**) allow you to explicitly **control which classes or interfaces can extend or implement a class or interface**.

✅ Basic Syntax
```
public sealed class Shape permits Circle, Rectangle, Square {
    // common shape methods
}

public final class Circle extends Shape { }
public final class Rectangle extends Shape { }
public non-sealed class Square extends Shape { }
```

📦 Keywords Explained

|Keyword|Meaning|
|---|---|
|`sealed`|The base class or interface — restricts which classes can extend it|
|`permits`|Declares the allowed subclasses|
|`final`|The subclass cannot be extended further|
|`non-sealed`|The subclass lifts the restriction and can be freely extended|

