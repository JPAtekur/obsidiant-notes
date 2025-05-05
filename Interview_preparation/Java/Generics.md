
Generics let you define **classes, interfaces, and methods** with **type parameters**.

🔹 Without Generics (before Java 5):

```
List list = new ArrayList();
list.add("Hello");
String str = (String) list.get(0); // Needs casting
```

🔹 With Generics:
```
List<String> list = new ArrayList<>();
list.add("Hello");
String str = list.get(0); // No casting needed
```
✅**Type-safety** and **code clarity**

🔷 2. Why Use Generics?

|Benefit|Description|
|---|---|
|**Type Safety**|Catches type errors at compile time|
|**Code Reusability**|Write one class or method for multiple types|
|**Avoids Casting**|Reduces boilerplate and runtime errors|
|**Improved Readability**|Makes code clearer and more self-documenting|

🔷 3. Generic Classes

```
public class Box<T> {
    private T value;

    public void set(T value) { this.value = value; }
    public T get() { return value; }
}
```

🔹 Usage:

```
Box<Integer> intBox = new Box<>();
intBox.set(123);
System.out.println(intBox.get());
```

🔷 4. Generic Methods

```
public class Utility {
    public static <T> void printArray(T[] array) {
        for (T item : array) {
            System.out.println(item);
        }
    }
}
```

## 🔷 5. Bounded Type Parameters

### 🔹 Upper Bound (`extends`):

```
public <T extends Number> void printNumber(T num) {
    System.out.println(num.doubleValue());
}
```

✅ Restricts `T` to `Number` or its subclasses (`Integer`, `Double`, etc.)

### 🔹 Lower Bound (`super` — with wildcards):

```
public void addToList(List<? super Integer> list) {
    list.add(10); // Allowed
}
```

✅ Allows the list to accept `Integer` or any supertype of it (like `Number`, `Object`)

🔷 6. Wildcards

| Wildcard        | Description                     |
| --------------- | ------------------------------- |
| `<?>`           | Unknown type                    |
| `<? extends T>` | Upper bound: any subtype of T   |
| `<? super T>`   | Lower bound: any supertype of T |