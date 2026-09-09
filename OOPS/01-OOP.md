# OOP — Object-Oriented Programming Interview Mastery

## Module Roadmap

```
Class & Object
     ↓
Encapsulation
     ↓
Abstraction
     ↓
Inheritance
     ↓
Polymorphism
     ↓
Overloading vs Overriding
     ↓
Abstract Class
     ↓
Interface
     ↓
Abstract Class vs Interface
     ↓
Default Methods
     ↓
Multiple Inheritance / Diamond Problem
     ↓
this / super
     ↓
Association / Aggregation / Composition
     ↓
Interviewer Grill Round
```

---

## Question 1: What are the four pillars of OOP?

**Priority:** 🔥 Must Know
**Source:** 📘 PDF Question 1

**Difficulty:** Beginner
**Interview Frequency:** Very High

### What the Interviewer Is Testing

The interviewer usually isn't interested in hearing only:

Encapsulation, Abstraction, Inheritance, Polymorphism.

They want to know whether you understand what problem each one solves.

The PDF introduces exactly these four pillars and describes abstraction as hiding implementation details, encapsulation as binding data and methods, inheritance as parent-child reuse, and polymorphism as multiple forms.

### Short Interview Answer

The four main OOP principles are encapsulation, abstraction, inheritance, and polymorphism. Encapsulation protects and controls access to an object's state. Abstraction exposes what an object can do while hiding unnecessary implementation details. Inheritance allows one class to acquire and specialize behavior from another class. Polymorphism allows the same abstraction or method call to behave differently depending on the actual object involved.

The most important thing is to understand that these concepts solve different problems.

```
Encapsulation
→ How do I protect/control object state?

Abstraction
→ How do I expose only what users need?

Inheritance
→ How can one type specialize another?

Polymorphism
→ How can the same contract support different behavior?
```

---

## Question 2: What is a class and what is an object?

**Priority:** 🔥 Must Know
**Source:** ➕ Added — important foundation not given a dedicated PDF question

**Difficulty:** Beginner
**Interview Frequency:** Very High

### Short Interview Answer

A class is a definition or blueprint describing the state and behavior of a type, while an object is a runtime instance of that class. The class defines fields and methods; an object has actual values for those fields and can execute those methods.

### Root-Level Understanding

Suppose we define:

```java
class Car {
    String color;

    void start() {
        System.out.println("Car started");
    }
}
```

The class tells Java:

```
A Car has:
    color

A Car can:
    start()
```

But this:

```java
Car car1 = new Car();
Car car2 = new Car();
```

creates two separate objects.

Conceptually:

```
             Car class
          ┌─────────────┐
          │ color       │
          │ start()     │
          └─────────────┘
              blueprint
                  ↓

        ┌────────────────┐
car1 ──►│ color = "Red"  │
        └────────────────┘

        ┌─────────────────┐
car2 ──►│ color = "Black" │
        └─────────────────┘
```

The method definition belongs to the class; instance state belongs to individual objects.

### Interview Follow-Ups

**Q: Can we have a class without creating objects?**
Yes.
Utility classes containing static methods are a common example.

**Q: Can one class produce many objects?**
Yes.

```java
new Car();
new Car();
new Car();
```

Each is a separate instance.

**Q: Where are objects stored?**
Ordinary Java objects are allocated in JVM-managed heap memory conceptually, though JVM optimization can make the actual implementation more sophisticated.

### Tricky Question

```java
Car c1 = new Car();
Car c2 = c1;
```

**How many objects exist?**
One.

There are two reference variables pointing to the same object.

```
c1 ──┐
     ├──► Car object
c2 ──┘
```

---

## Question 3: What is encapsulation?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF + interviewer expansion

### Short Interview Answer

Encapsulation means keeping an object's state and the operations that manage that state together while controlling how external code can access or modify it. In Java, we commonly achieve this using classes, access modifiers, and methods that enforce rules around the object's state.

### The Problem

Imagine:

```java
class BankAccount {
    public double balance;
}
```

Anyone can do:

```java
BankAccount account = new BankAccount();

account.balance = -5_000_000;
```

The object cannot protect itself.

That is the real problem encapsulation solves.

### Better Design

```java
class BankAccount {

    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException(
                    "Amount must be positive"
            );
        }

        balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

Now external code cannot directly corrupt:

```
balance
```

Instead:

```
External code
      |
      v
deposit(500)
      |
      v
validation/business rules
      |
      v
private balance
```

### Why Encapsulation Is More Than Getters and Setters

A common fresher answer is:

Encapsulation means fields private + getter + setter.

That's incomplete.

This:

```java
private int age;

public void setAge(int age) {
    this.age = age;
}
```

might technically control access, but if every internal field is blindly exposed through unrestricted getters/setters, you haven't gained much protection.

Better:

```java
public void updateAge(int age) {
    if (age < 0 || age > 150) {
        throw new IllegalArgumentException();
    }

    this.age = age;
}
```

The real idea is:

The object controls its own valid state.

### Real-World Spring Boot Example

Consider an entity:

```java
class Ride {

    private RideStatus status;

    public void startRide() {
        if (status != RideStatus.ACCEPTED) {
            throw new IllegalStateException();
        }

        status = RideStatus.IN_PROGRESS;
    }
}
```

Instead of:

```java
ride.setStatus(anything);
```

the object protects business rules.

### Common Fresher Mistakes

❌ Encapsulation = only data hiding.
❌ Encapsulation = only getters/setters.
❌ Every private field must have a setter.

### Follow-Up Question

**Is encapsulation possible without setters?**
Absolutely.
In many well-designed classes, removing unrestricted setters improves encapsulation.

---

## Question 4: What is abstraction?

**Priority:** 🔥 Must Know
**Source:** 📘 PDF + expansion

The PDF describes abstraction as hiding implementation details and exposing functionality, using examples such as a TV remote and car gears.

### Short Interview Answer

Abstraction means exposing the essential behavior of something while hiding details that a caller doesn't need to know. In Java, interfaces and abstract classes are common language mechanisms for expressing abstraction, but abstraction is fundamentally a design principle rather than just an `abstract` keyword.

### Root Cause

Suppose payment code had to understand:

```
Open socket
Generate signature
Create request
Encrypt payload
Retry network call
Parse response
Validate transaction
```

every time it made a payment.

That's too much detail.

Instead:

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

The caller only knows:

```java
processor.pay(1000);
```

The implementation complexity is hidden.

```
Application
    |
    | pay(1000)
    v
PaymentProcessor
    |
    +---------------------------
    | implementation details
    | authentication
    | networking
    | retrying
    | validation
    +---------------------------
```

### Encapsulation vs Abstraction

This is a very common follow-up.

```
Encapsulation
Protect/control HOW state is accessed.

Abstraction
Hide unnecessary implementation details
and expose WHAT the component provides.
```

Example:

```java
interface NotificationService {
    void send(String message);
}
```

That's abstraction.

Inside:

```java
class EmailNotificationService {

    private String smtpPassword;
}
```

Protecting `smtpPassword` is encapsulation.

### Interview Trap

**Can abstraction exist without an abstract class?**
Yes.
Interfaces are one mechanism, but ordinary APIs can also provide abstraction by exposing simple operations over hidden implementation details.

---

## Question 5: What is inheritance?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF + expansion

The PDF describes inheritance as establishing a parent-child relationship and emphasizes reuse.

### Short Interview Answer

Inheritance allows one class to derive from another class, inheriting accessible behavior and state and optionally extending or specializing that behavior. It represents an is-a relationship. Although code reuse is one advantage, inheritance should mainly be used when the subtype genuinely represents the parent type.

### Example

```java
class Vehicle {

    void start() {
        System.out.println("Vehicle starting");
    }
}

class Car extends Vehicle {

    void openBoot() {
        System.out.println("Boot opened");
    }
}
```

A Car:

```
IS-A Vehicle
```

So:

```java
Car car = new Car();

car.start();
car.openBoot();
```

### Important Engineering Point

Do not say:

"Inheritance exists mainly for code reuse."

That's too shallow.

Imagine:

```java
class DatabaseConnection {
    void connect() {}
}
```

and:

```java
class UserService extends DatabaseConnection {
}
```

You might reuse `connect()`.

But:

```
UserService IS-A DatabaseConnection
```

doesn't make conceptual sense.

Composition is often better:

```java
class UserService {

    private DatabaseConnection connection;
}
```

### Real-World Example

```java
abstract class Payment {
    abstract void process();
}

class CreditCardPayment extends Payment {
    @Override
    void process() {}
}
```

Here:

```
CreditCardPayment IS-A Payment
```

is meaningful.

---

## Question 6: What is polymorphism?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF

The PDF associates compile-time polymorphism with overloading and runtime polymorphism with overriding.

### Short Interview Answer

Polymorphism means one common abstraction can represent multiple concrete forms. In Java, method overloading is commonly described as compile-time polymorphism, while method overriding provides runtime polymorphism, where a parent reference can invoke a subclass implementation depending on the actual runtime object.

### Runtime Polymorphism

```java
class Payment {

    void pay() {
        System.out.println("Generic payment");
    }
}
```

```java
class UpiPayment extends Payment {

    @Override
    void pay() {
        System.out.println("UPI payment");
    }
}
```

```java
Payment payment = new UpiPayment();

payment.pay();
```

Output:

```
UPI payment
```

Now ask the critical question:

```
Reference type = Payment
Object type    = UpiPayment
```

Who determines the overridden method?

```
Runtime object
```

Therefore:

```java
payment.pay();
```

dispatches to:

```java
UpiPayment.pay()
```

### Why This Matters

Without polymorphism:

```java
if (type.equals("UPI")) {
    ...
} else if (type.equals("CARD")) {
    ...
} else if (type.equals("CASH")) {
    ...
}
```

With polymorphism:

```java
Payment payment = getPaymentMethod();

payment.pay();
```

The caller doesn't need to know which concrete implementation exists.

### Spring Example

This is everywhere in Spring:

```java
UserRepository repository;
```

You code against an abstraction.

The runtime provides the actual implementation.

That is one reason interface-driven design works so well with dependency injection.

---

## Question 7: What is the difference between method overloading and overriding?

**Priority:** 🔥 Must Know | ⚠️ Tricky | 💻 Coding
**Source:** 📘 PDF Questions 10–12

The PDF states that overloading requires the same method name with different arguments, while overriding requires the same name/parameters, compatible return type, no broader checked exception, and no more restrictive access modifier.

### Short Interview Answer

Overloading means defining multiple methods with the same name but different parameter lists, and selection is made at compile time. Overriding happens when a subclass provides a new implementation of an inherited instance method with the same signature and compatible return type; the actual overridden method is selected at runtime based on the object.

### Comparison

| Feature | Overloading | Overriding |
|---|---|---|
| Where | Usually same class | Parent-child relationship |
| Method name | Same | Same |
| Parameters | Must differ | Same |
| Resolution | Compile time | Runtime |
| Return type | Can differ, but not alone | Same/covariant |
| Inheritance required | No | Yes |
| Static methods | Can overload | Hidden, not overridden |
| Final methods | Can overload | Cannot override |
| Private methods | Can overload | Cannot override |

### Overloading Example

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

### Can We Overload Only by Return Type?

No.

This is invalid:

```java
int calculate() {
    return 1;
}

double calculate() {
    return 1.0;
}
```

Because invocation:

```java
calculate();
```

would give the compiler no parameter information with which to choose.

### Overriding Example

```java
class Animal {

    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {

    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

```java
Animal animal = new Dog();

animal.sound();
```

Output:

```
Bark
```

### Covariant Return Type

Parent:

```java
class Parent {
    Animal create() {
        return new Animal();
    }
}
```

Child:

```java
class Child extends Parent {

    @Override
    Dog create() {
        return new Dog();
    }
}
```

This is valid when `Dog` is a subtype of `Animal`.

---

## Question 8: Can private, static and final methods be overridden?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF Questions 11–12 + expansion

The PDF answers that final methods cannot be overridden and that constructors/private methods cannot be overridden.

### Final Method

```java
class Parent {
    final void show() {}
}
```

Cannot override.

### Private Method

```java
class Parent {

    private void show() {}
}
```

Child does not inherit this method in the normal overridable sense.

If child writes:

```java
class Child extends Parent {

    private void show() {}
}
```

this is a completely separate method.

### Static Method

Static methods are hidden, not overridden.

```java
class Parent {

    static void show() {
        System.out.println("Parent");
    }
}
```

```java
class Child extends Parent {

    static void show() {
        System.out.println("Child");
    }
}
```

Now:

```java
Parent obj = new Child();

obj.show();
```

prints:

```
Parent
```

because static method resolution is based on the reference/compiler type rather than dynamic dispatch.

### Constructor

Constructors are not inherited.

Therefore they cannot be overridden.

---

## Question 9: What is an abstract class?

**Priority:** 🔥 Must Know
**Source:** 📘 PDF Question 2

The PDF correctly notes that an abstract class is declared with `abstract`, may contain both abstract and concrete methods, cannot be instantiated directly, and requires concrete subclasses to implement inherited abstract methods unless those subclasses remain abstract.

### Short Interview Answer

An abstract class is a class that cannot be instantiated directly and is intended to act as a partially implemented base type. It may contain abstract methods that subclasses must implement as well as concrete methods, fields, constructors, static methods, and shared state.

### Why Was It Needed?

Imagine several payment classes share:

```
transactionId
createdAt
validateAmount()
logTransaction()
```

but processing differs.

```java
abstract class Payment {

    protected String transactionId;

    void validateAmount(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException();
        }
    }

    abstract void process(double amount);
}
```

Then:

```java
class UpiPayment extends Payment {

    @Override
    void process(double amount) {
        System.out.println("UPI processing");
    }
}
```

The abstract class says:

All Payments share this implementation/state,
but subclasses MUST define `process()`.

### Can an Abstract Class Have Zero Abstract Methods?

Yes.

```java
abstract class BaseService {

    void log() {
        System.out.println("Logging");
    }
}
```

This is valid.

Making it abstract prevents direct instantiation even though all methods are concrete.

### Can an Abstract Method Be Private?

No.

Think about the contradiction:

```
abstract method
→ subclass must implement it

private method
→ subclass cannot access/inherit it as an overridable method
```

Therefore:

```java
private abstract void process();
```

is invalid.

---

## Question 10: If we cannot instantiate an abstract class, why does it have a constructor?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF Question 3

The PDF uses code examples showing that when a subclass object is created, the abstract superclass constructor executes first.

The PDF's page 23 example visually shows:

```
new AbstractDemo()
      ↓
MyAbstractClass constructor
      ↓
AbstractDemo constructor
```

### Short Interview Answer

Yes, an abstract class can have a constructor. We cannot instantiate the abstract class itself, but when a concrete subclass object is created, its superclass portion still needs initialization. Therefore the abstract superclass constructor executes through `super()` before the subclass constructor continues.

### Example

```java
abstract class Vehicle {

    String registrationNumber;

    Vehicle(String registrationNumber) {
        this.registrationNumber = registrationNumber;
        System.out.println("Vehicle constructor");
    }
}
```

```java
class Car extends Vehicle {

    Car(String registrationNumber) {
        super(registrationNumber);

        System.out.println("Car constructor");
    }
}
```

```java
new Car("KA01AB1234");
```

Output:

```
Vehicle constructor
Car constructor
```

### Mental Model

Creating:

```
new Car()
```

does not mean:

only Car gets initialized

The Car object includes inherited superclass state.

```
Car Object
┌──────────────────────┐
│ Vehicle portion      │
│ registrationNumber   │
├──────────────────────┤
│ Car portion          │
│ ...                  │
└──────────────────────┘
```

Superclass initialization happens first.

---

## Question 11: What is an interface?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF Question 4 + 🔄 modern clarification

The PDF describes an interface as a contract specifying what implementing classes should provide, notes that interface fields are constants, and mentions Java 8 default/static methods.

### Short Interview Answer

An interface defines a contract or capability that implementing types agree to provide. It lets clients depend on an abstraction rather than a concrete implementation. Interfaces can contain abstract methods, default methods, static methods and, in modern Java, private helper methods. Interface fields are implicitly `public static final`.

### Root Problem

Imagine:

```java
class OrderService {

    EmailNotificationService service;
}
```

Now `OrderService` is tightly coupled specifically to email.

What if tomorrow we need SMS?

Better:

```java
interface NotificationService {

    void send(String message);
}
```

Implementations:

```java
class EmailNotificationService
        implements NotificationService {

    @Override
    public void send(String message) {
        System.out.println("Email");
    }
}
```

```java
class SmsNotificationService
        implements NotificationService {

    @Override
    public void send(String message) {
        System.out.println("SMS");
    }
}
```

Business code:

```java
class OrderService {

    private final NotificationService notificationService;

    OrderService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
}
```

Now:

```
OrderService
     |
     v
NotificationService
    / \
   /   \
Email  SMS
```

`OrderService` depends on the contract, not implementation.

That's the deeper motivation behind interfaces.

---

## Question 12: Abstract class vs interface

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF Question 5 + 🔄 modernization

The PDF emphasizes that a class can extend only one class but implement multiple interfaces, and distinguishes the state/method capabilities of the two mechanisms.

### Short Interview Answer

I use an abstract class when related subclasses need shared state or common base implementation and genuinely form one inheritance hierarchy. I use an interface mainly to define a contract or capability that potentially unrelated classes can implement. A class can extend only one class but implement multiple interfaces.

| Abstract Class | Interface |
|---|---|
| Can contain instance state | No ordinary instance fields |
| Can have constructors | No constructors |
| Can have abstract methods | Yes |
| Can have concrete instance methods | Yes |
| Supports access modifiers | Interface abstract methods are public |
| One class can extend one class | A class can implement many interfaces |
| Models common base implementation | Models contract/capability |

### 🔄 Modern Java Clarification

Do not memorize the old statement:

"Interface contains only abstract methods."

That stopped being true years ago.

Modern interfaces may contain:

- abstract methods
- default methods
- static methods
- private methods

But the important difference remains:

An interface doesn't provide ordinary per-object instance state like a class does.

---

## Question 13: When should we choose an interface vs abstract class?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF Question 6 + interviewer expansion

The PDF recommends interfaces in many cases and emphasizes "coding in terms of interfaces."

### Better Interview Decision Rule

Ask:

```
Do these types represent one closely related
family sharing state/implementation?
                |
              Yes
                ↓
        Abstract class may fit


Do I mainly need a capability/contract
that many unrelated classes can implement?
                |
              Yes
                ↓
             Interface
```

### Example — Interface

```java
interface Flyable {
    void fly();
}
```

Potential implementations:

```
Bird
Drone
Airplane
```

They aren't necessarily one natural class hierarchy.

They simply share:

```
CAN-FLY
```

### Example — Abstract Class

```java
abstract class Employee {

    protected long id;
    protected String name;

    void login() {}

    abstract double calculateSalary();
}
```

```
Employee
├── Developer
├── Manager
└── Tester
```

These share a meaningful base state and behavior.

---

## Question 14: Why were default methods introduced in interfaces?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF Question 7

The PDF gives the classic motivation: suppose 100 classes already implement an interface; adding a new abstract method would otherwise force all implementations to change. Default methods allow the interface to evolve while providing an implementation that existing implementers can inherit.

### Short Interview Answer

Default methods were introduced mainly to allow interfaces to evolve without breaking every existing implementation. An interface can add a new operation with a default implementation, and existing implementing classes continue to work unless they need to override that behavior.

### Problem Before Default Methods

```java
interface Collection {
    void add();
}
```

Suppose thousands of classes implement it.

Then Java designers want:

```java
stream()
```

If added as an abstract method:

```java
interface Collection {
    void add();

    Stream stream();
}
```

every implementation would suddenly need modification.

Default method:

```java
interface Collection {

    default void print() {
        System.out.println("Default");
    }
}
```

provides backward-compatible evolution.

---

## Question 15: What happens if two interfaces have the same default method?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF

The PDF includes a visual example on page 28 where `Interface1` and `Interface2` both define `hello()`, causing the implementing class to receive conflicting defaults.

The PDF then states the implementing class must resolve the common default method.

### Example

```java
interface A {

    default void show() {
        System.out.println("A");
    }
}
```

```java
interface B {

    default void show() {
        System.out.println("B");
    }
}
```

Then:

```java
class Child implements A, B {

    @Override
    public void show() {
        System.out.println("Child");
    }
}
```

The class must resolve the ambiguity.

### Can We Explicitly Call a Particular Interface Version?

Yes.

```java
class Child implements A, B {

    @Override
    public void show() {
        A.super.show();
    }
}
```

### Important Interview Rule

```
Two unrelated interface defaults conflict
                ↓
Implementing class must resolve ambiguity
```

---

## Question 16: Why does Java not support multiple inheritance with classes?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF Question 9 + deeper interviewer explanation

The PDF explains the ambiguity that would arise if two parent classes contained the same method and one child inherited both, identifying the classic diamond problem.

### Short Interview Answer

Java doesn't allow a class to extend multiple classes because it can introduce ambiguity and complexity involving inherited behavior and state. The classic example is the diamond problem: if two parents provide the same method, the child would need rules for deciding which implementation to inherit. Java instead allows one class superclass and multiple interfaces.

### Classic Problem

Imagine Java allowed:

```java
class A {
    void show() {
        System.out.println("A");
    }
}

class B {
    void show() {
        System.out.println("B");
    }
}
```

Then:

```java
class C extends A, B {
}
```

Calling:

```java
new C().show();
```

raises:

```
Which implementation?

A.show()?
or
B.show()?
```

### Why Do Interfaces Allow Multiple Inheritance Then?

Because interfaces primarily represent contracts.

```java
class SmartPhone
        implements Camera, MusicPlayer, GPS {
}
```

That's useful:

```
SmartPhone IS Camera-capable
SmartPhone IS MusicPlayer-capable
SmartPhone IS GPS-capable
```

And when default implementations conflict, Java forces an explicit resolution.

### Strong Interview Answer

Avoid saying only:

"Java doesn't support multiple inheritance because of diamond problem."

Better:

"Java avoids multiple class inheritance mainly to keep inheritance of implementation and state unambiguous and simpler. The diamond problem is the classic demonstration."

---

## Question 17: What is `this` in Java?

**Priority:** 🔥 Must Know
**Source:** ➕ Added — no dedicated question in the PDF

### Short Interview Answer

`this` refers to the current object whose instance method or constructor is executing. It's commonly used to distinguish instance variables from parameters, invoke another constructor in the same class, pass the current object, or return the current object.

### Most Common Usage

```java
class User {

    private String name;

    User(String name) {
        this.name = name;
    }
}
```

Here:

```
name       → constructor parameter
this.name  → current object's field
```

### Constructor Chaining

```java
class User {

    User() {
        this("Guest");
    }

    User(String name) {
        System.out.println(name);
    }
}
```

Important rule:

```java
this(...)
```

must be the first constructor statement when explicitly used.

### Can `this` Be Used in Static Method?

No.

Why?

A static method has no current object.

```
static context
      ↓
belongs to class
      ↓
no implicit current instance
      ↓
no this
```

---

## Question 18: What is `super`?

**Priority:** 🔥 Must Know
**Source:** ➕ Dedicated expansion; PDF references `super` in constructor and variable-hiding examples

The PDF's abstract-class constructor example explicitly shows subclass construction invoking superclass initialization through `super(...)`.

### Short Interview Answer

`super` refers to the immediate superclass portion of the current object. We commonly use it to invoke the superclass constructor, access a hidden parent field, or call an overridden superclass method.

### Parent Constructor

```java
class Parent {

    Parent(int value) {
        System.out.println(value);
    }
}
```

```java
class Child extends Parent {

    Child() {
        super(10);
    }
}
```

### Parent Method

```java
class Parent {

    void show() {
        System.out.println("Parent");
    }
}
```

```java
class Child extends Parent {

    @Override
    void show() {
        super.show();

        System.out.println("Child");
    }
}
```

Output:

```
Parent
Child
```

### this() vs super()

```
this()
→ another constructor in SAME class

super()
→ constructor in PARENT class
```

You cannot explicitly use both as the first constructor invocation:

```java
this();
super();
```

because both must occupy the first-statement position.

---

## Question 19: What is association?

**Priority:** ⭐ Important
**Source:** ➕ Added — not covered as an OOP question in the PDF

### Short Interview Answer

Association represents a relationship where one object knows about or works with another object. It is the general "has-a" or "uses-a" relationship. Aggregation and composition are stronger forms of association.

Example:

```java
class Driver {

    private Car car;
}
```

```
Driver ───── Car
```

They are associated.

---

## Question 20: What is aggregation?

**Priority:** ⭐ Important | ⚠️ Often Confused
**Source:** ➕ Added

### Core Idea

Aggregation is a weak has-a relationship.

The contained object can meaningfully exist independently of the owner.

Example:

```java
class Department {

    private List<Teacher> teachers;
}
```

A teacher could exist independently and potentially move to another department.

```
Department
    |
    | has
    v
Teacher

Teacher's lifetime
does NOT strictly depend
on Department
```

### Interview Example

```java
class Team {
    List<Player> players;
}
```

Deleting a team does not logically mean every Player must cease to exist.

---

## Question 21: What is composition?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** ➕ Added

### Short Interview Answer

Composition is a stronger has-a relationship where one object owns another object as an integral part of itself, and the contained object's lifecycle is conceptually tied closely to the owner.

Example:

```java
class House {

    private final Room room = new Room();
}
```

Conceptually:

```
House
  |
  | owns
  v
Room
```

### Aggregation vs Composition

```
Aggregation
Team HAS Players
Players can exist independently.


Composition
Order HAS OrderLines
OrderLine conceptually belongs to that Order.
```

---

## Question 22: Why is composition often preferred over inheritance?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** ➕ Interviewer expansion

### Short Interview Answer

Composition is often preferred when the relationship is "has-a" rather than "is-a." It reduces tight coupling to a superclass, allows behavior to be changed by replacing dependencies, and avoids fragile inheritance hierarchies. Inheritance is still appropriate when there is a true subtype relationship.

### Inheritance

```java
class UserService extends DatabaseConnection {
}
```

Bad model:

```
UserService IS-A DatabaseConnection ❌
```

### Composition

```java
class UserService {

    private final DatabaseConnection connection;

    UserService(DatabaseConnection connection) {
        this.connection = connection;
    }
}
```

Now:

```
UserService HAS-A DatabaseConnection ✅
```

This is also closely related to Spring dependency injection.

---

## OOP Mental Model

Do not memorize the pillars independently.

Connect them:

```
                 OOP
                   |
       ┌───────────┼───────────┐
       |           |           |
 Encapsulation  Abstraction  Inheritance
       |           |           |
 protect state   expose      model IS-A
                 contract
                               |
                               v
                         Polymorphism
                               |
                     same abstraction
                     many implementations
```

And then:

```
IS-A relationship
      ↓
Inheritance

HAS-A relationship
      ↓
Composition / Aggregation
```

---

## Common Fresher Mistakes

**❌ 1. "Encapsulation means private variables with getters and setters."**

Too shallow.

Say:

Encapsulation means controlling access to state and preserving object invariants.

**❌ 2. "Abstraction means abstract class."**

`abstract` classes are one tool for achieving abstraction.

Abstraction is the broader design principle.

**❌ 3. "Inheritance is mainly for code reuse."**

Reuse is a benefit, but good inheritance models a meaningful:

```
IS-A
```

relationship.

**❌ 4. "Static methods are overridden."**

They are hidden.

**❌ 5. "Private methods are overridden."**

They are not dynamically overridable.

**❌ 6. "Interfaces contain only abstract methods."**

Not modern Java.

**❌ 7. "An abstract class cannot have a constructor."**

It can, and that constructor participates in subclass initialization.

**❌ 8. "Java supports multiple inheritance through interfaces exactly like C++ classes."**

Be more precise:

Java allows a class to implement multiple interfaces, providing multiple inheritance of type/contracts, while Java prevents multiple class inheritance of implementation/state.

---

## Interviewer Grill Round — OOP

Now we move from definitions into an actual fresher interview.

**Interviewer**
What are the four pillars of OOP?

**Candidate**
Encapsulation, abstraction, inheritance and polymorphism.

**Interviewer**
Don't define them. Tell me what problem each solves.

**Candidate**
Encapsulation protects and controls object state. Abstraction hides unnecessary implementation details behind a simpler contract. Inheritance models subtype relationships and allows specialization of common behavior. Polymorphism lets code work with one abstraction while runtime objects provide different implementations.

**Interviewer**
What's the difference between encapsulation and abstraction?

**Candidate**
Encapsulation focuses on controlling access to an object's internal state, while abstraction focuses on exposing only necessary behavior and hiding implementation complexity.

**Interviewer**
If all fields are private and I provide getters/setters for everything, is my class well encapsulated?

**Candidate**
Not necessarily. If setters allow invalid or unrestricted modification, the object isn't protecting its invariants. Good encapsulation means the object controls how its state can change.

**Interviewer**
What is inheritance?

**Candidate**
It's a mechanism where one class derives from another and represents an is-a relationship, inheriting accessible members and possibly specializing behavior.

**Interviewer**
Why shouldn't inheritance be used simply to reuse code?

**Candidate**
Because it creates a strong subtype relationship and tight coupling to the parent. If the child isn't genuinely a form of the parent, composition usually gives cleaner reuse.

**Interviewer**
What is runtime polymorphism?

**Candidate**
A parent/interface reference can point to a subclass implementation, and overridden instance-method selection happens based on the runtime object's actual type.

**Interviewer**
```java
Animal a = new Dog();
a.sound();
```
Which `sound()` executes?

**Candidate**
If `Dog` overrides the instance method, `Dog.sound()` executes because overriding uses runtime dynamic dispatch.

**Interviewer**
What if `sound()` is static?

**Candidate**
Then it isn't overridden. Static methods are hidden, and method selection is based on the reference/compiler type, so `Animal.sound()` would be selected.

**Interviewer**
Can an abstract class have a constructor?

**Candidate**
Yes. When a concrete subclass object is created, the abstract superclass portion still needs initialization, so the abstract superclass constructor executes through constructor chaining.

**Interviewer**
Can an abstract class contain no abstract methods?

**Candidate**
Yes. The class may simply be declared abstract to prevent direct instantiation or to serve as a controlled base class.

**Interviewer**
Can an abstract method be private?

**Candidate**
No, because a subclass must implement an abstract method, while a private method isn't inherited as an overridable method.

**Interviewer**
Why do we need interfaces if abstract classes already exist?

**Candidate**
Interfaces primarily define contracts and allow a class to implement multiple capabilities without committing to one implementation hierarchy. Abstract classes are more suitable when closely related subclasses need common state and implementation.

**Interviewer**
Why were default methods introduced?

**Candidate**
Primarily for interface evolution and backward compatibility. A new operation can be added to an existing interface with a default implementation without forcing every existing implementer to change immediately.

**Interviewer**
Two interfaces have the same default method. My class implements both. What happens?

**Candidate**
Java sees an ambiguity, so the implementing class must override the method and resolve the conflict. It can also explicitly invoke a chosen interface implementation using syntax such as `A.super.show()`.

**Interviewer**
Why doesn't Java allow multiple class inheritance?

**Candidate**
Because inheriting implementation and state from multiple classes can introduce ambiguity and significantly complicate inheritance semantics. The diamond problem is the classic example. Java instead permits one superclass and multiple interfaces.

---

## Final 60-Second OOP Revision

Before an interview, be able to say these without thinking:

```
Encapsulation
→ Protect/control state.

Abstraction
→ Hide unnecessary details; expose contract.

Inheritance
→ IS-A relationship.

Composition
→ HAS-A relationship.

Polymorphism
→ Same abstraction, different implementations.

Overloading
→ Compile-time selection.

Overriding
→ Runtime dynamic dispatch.

Abstract class
→ Partial base implementation + possible state.

Interface
→ Contract/capability.

this
→ Current object.

super
→ Immediate superclass portion.

static methods
→ Hidden, not overridden.

private methods
→ Not overridable.

final methods
→ Cannot be overridden.

Constructors
→ Not inherited, therefore not overridden.
```

### The OOP chain you should mentally master

```
Why OOP?
   ↓
How do we model objects?
   ↓
How do we protect their state?
Encapsulation
   ↓
How do we hide implementation?
Abstraction
   ↓
How do types relate?
Inheritance
   ↓
How can implementations vary?
Polymorphism
   ↓
How do we define contracts?
Interfaces
   ↓
When inheritance doesn't fit?
Composition
```

---

## Next: 03-Object-Class.md

This is one of the most important interview modules because we'll connect:

```
Object
  ↓
equals()
  ↓
hashCode()
  ↓
HashMap / HashSet
  ↓
custom key
  ↓
broken equals/hashCode contract
  ↓
toString()
clone()
getClass()
finalize()
  ↓
15-question Object-Class grill
```

That module will especially go root-level into why overriding `equals()` without `hashCode()` breaks hash-based collections, which the PDF also tests through multiple custom-key HashMap scenarios.
