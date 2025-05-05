
## 1. Encapsulation
**Encapsulation** is one of the core principles of **Object-Oriented Programming (OOP)**. It means **wrapping data (fields) and methods (behaviors)** into a single unit — a class. The encapsulation restricts direct access to important data.
Encapsulation can be achieved by declaring all the variables in a class as private and writing public methods in the class to set and get the values of the variables.

✅ Key Concepts

|Feature|Description|
|---|---|
|**Private Fields**|Internal data is hidden from outside classes|
|**Public Getters/Setters**|Controlled access through methods|
|**Data Hiding**|Prevents unauthorized access or modification|

## 2. Abstraction
In object-oriented programming, an abstraction is a technique of hiding internal details implementations and showing functionalities. The abstract classes and interfaces are used to achieve abstraction in Java.
### 🌟 Benefits of Abstraction

| Benefit                      | Description                                                |
| ---------------------------- | ---------------------------------------------------------- |
| **Reduces complexity**       | Hides implementation logic                                 |
| **Increases reusability**    | Common code in abstract classes or interfaces              |
| **Improves maintainability** | Easier to update or extend functionality                   |
| **Supports loose coupling**  | Clients depend on interfaces, not concrete implementations |

## 3. Inheritance
**Inheritance** is an **OOP concept** where one class (**child/subclass**) **inherits** properties and behaviors (fields and methods) from another class (**parent/superclass**).
Inheritance promotes **code reuse**, **hierarchical classification**, and **extensibility**.

### 🌟 Benefits of Inheritance

|Benefit|Description|
|---|---|
|**Code Reusability**|Reuse common logic in parent class|
|**Polymorphism**|Call methods dynamically based on object type|
|**Logical Structure**|Create organized class hierarchies|
|**Extensibility**|Add new features without changing existing code|

## 4. Polymorphism
**Polymorphism** means **"many forms"** — it's the ability of a single interface or method to behave **differently based on the object that implements or invokes it**.

### ✅ Types of Polymorphism in Java

| Type             | Also Known As      | Example                                        |
| ---------------- | ------------------ | ---------------------------------------------- |
| **Compile-time** | Method Overloading | Same method name, different parameters         |
| **Runtime**      | Method Overriding  | Subclass changes behavior of superclass method |
