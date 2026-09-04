## Question 1: What is the difference between JDK, JRE and JVM?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** ➕ Added

**Difficulty:** Beginner
**Interview Frequency:** Very High

### What the Interviewer Is Testing

Whether you understand the basic architecture behind running Java rather than simply knowing Java syntax.

### Short Interview Answer

JVM is the engine that executes Java bytecode. JRE represents the runtime environment required to run Java applications—the JVM plus runtime libraries. JDK is meant for development, so it contains the runtime plus tools such as javac, javadoc, and other development utilities. In simple terms, I write and compile using the JDK, and the resulting bytecode is executed by the JVM.

### Deep Explanation

Imagine you write:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

The CPU cannot directly understand Java source code.
Something must convert it.

```
Main.java
    |
    | javac
    v
Main.class
    |
    | ClassLoader
    v
JVM
    |
    | Interpreter / JIT
    v
Native machine instructions
    |
    v
CPU
```

**JVM (Java Virtual Machine)**

Responsibilities include:

- loading classes
- verifying bytecode
- managing memory
- executing bytecode
- garbage collection
- JIT compilation
- thread/runtime management

**JRE**

Traditionally:

```
JRE
├── JVM
└── Runtime libraries
```

Conceptually, it means everything required to run Java programs.

**JDK**

```
JDK
├── Runtime
├── javac
├── java
├── javadoc
├── javap
├── jdb
└── other development tools
```

So:

```
Developer
   |
   | uses
   v
  JDK
   |
   | produces
   v
Bytecode
   |
   | executed by
   v
  JVM
```

### Common Fresher Mistakes

❌ "JVM converts Java source code into bytecode."
No. `javac` does that.

❌ "JVM is platform-independent."
The bytecode is platform-independent.
The JVM implementation itself must work with the underlying OS and CPU.

### Interview Follow-Up Questions

**Q1. Who creates bytecode?**
`javac`, the Java compiler.

**Q2. Where is bytecode stored?**
Normally inside `.class` files.

**Q3. Who executes bytecode?**
The JVM.

**Q4. Is the JVM exactly the same binary on Windows and Linux?**
No. JVM implementations are platform-specific.

### Tricky / Counter Question

**Interviewer:** If Java is platform-independent, why do I download a different JDK for Windows and Linux?

**Expected reasoning:**
Because Java bytecode is portable, not the JVM implementation itself.

```
      Same .class
   /        |         \
Windows JVM Linux JVM macOS JVM
```

Each JVM translates the common bytecode for its own platform.

### Related Concepts to Revise

Bytecode → ClassLoader → JVM architecture → JIT → Garbage Collection.

---

## Question 2: How is a Java program compiled and executed?

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** ➕ Added + expanded from the PDF's class-loading discussion

**Difficulty:** Intermediate
**Interview Frequency:** Very High

### Short Interview Answer

Java uses a two-stage model. First, `javac` compiles `.java` source code into platform-independent bytecode stored in `.class` files. At runtime, the JVM loads those classes, verifies the bytecode, and its execution engine runs it. Frequently executed code can be compiled into native machine code by the JIT compiler for better performance.

### The Complete Flow

```
Source Code
Main.java
    |
    | javac
    v
Bytecode
Main.class
    |
    v
ClassLoader
    |
    v
Bytecode Verifier
    |
    v
JVM Runtime
    |
    +----------------+
    |                |
Interpreter        JIT Compiler
    |                |
    +-------+--------+
            |
            v
      Native Code
            |
            v
           CPU
```

The PDF correctly connects `.class` files with bytecode and class loading, although its loader hierarchy itself requires modernization.

### Why Not Compile Java Directly to Native Code?

The bytecode layer provides portability.

Instead of producing:

```
Windows machine code
```

`javac` produces:

```
JVM bytecode
```

The platform-specific JVM handles the final execution.

### What Does JIT Do?

JIT means Just-In-Time compiler.

Suppose a method executes thousands of times:

```java
calculatePrice();
```

Interpreting the same bytecode repeatedly can be inefficient.

The JVM can identify frequently executed code and compile it into native machine instructions.

### Real-World Example

Your Spring Boot application may contain thousands of `.class` files.

At startup:

```
JVM starts
   ↓
classes are loaded as required
   ↓
bytecode verified
   ↓
application executes
   ↓
hot code gets optimized
```

### Common Fresher Mistakes

❌ Java is "only interpreted."
Modern JVMs use both interpretation and JIT compilation.

❌ `javac` produces machine code.
It normally produces bytecode.

### Interview Follow-Up Questions

**What command compiles Java?**
```
javac Main.java
```

**What command executes it?**
```
java Main
```

**Does the JVM load every application class immediately?**
Not necessarily. Classes are generally loaded when required according to runtime loading rules.

### Tricky Question

**Can `.class` bytecode execute directly on a CPU?**
No.
The CPU executes its native instruction set. JVM execution bridges that gap.

### Related Concepts

ClassLoader, bytecode verifier, JIT, interpreter, JVM runtime data areas.

---

## Question 3: What is bytecode?

**Priority:** 🔥 Must Know
**Source:** ➕ Added

**Difficulty:** Beginner
**Interview Frequency:** High

### Short Interview Answer

Bytecode is the intermediate instruction format produced by the Java compiler. It is stored in `.class` files and is designed for the JVM rather than for a specific CPU. That intermediate representation is one of the main reasons the same compiled Java application can run on different platforms with compatible JVMs.

### Core Idea

Java does not normally go:

```
Java → Intel instructions
```

Instead:

```
Java source
    ↓
Java bytecode
    ↓
JVM
    ↓
native instructions
```

### Example

```java
int result = 10 + 20;
```

`javac` produces JVM instructions rather than Windows/x86-specific instructions.

You can inspect compiled bytecode using:

```
javap -c Main
```

### Tricky Question

**Is bytecode human-readable?**
Not in its binary `.class` representation.
Tools such as `javap` can disassemble it into a readable representation.

### Related Concepts

JVM specification, JIT, class files, platform independence.

---

## Question 4: Why is Java platform-independent?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** ➕ Added

### Short Interview Answer

Java achieves platform independence mainly through bytecode. `javac` compiles the source into JVM bytecode rather than directly into operating-system-specific machine instructions. A compatible JVM on Windows, Linux, or macOS can then execute that same bytecode.

### Root Cause

Suppose C/C++ compilation produces:

```
Source
 ↓
Windows executable
```

That native executable depends on the target environment.

Java inserts another abstraction:

```
Java source
      ↓
   Bytecode
      ↓
+-----+------+------+
|            |      |
Windows     Linux  macOS
 JVM         JVM    JVM
```

### Important Interview Sentence

Java bytecode is platform-independent; JVM implementations are platform-dependent.

That sentence handles the most common counter-question.

### Tricky / Counter Question

**Interviewer:** Is Java 100% platform-independent?

**Better answer:**
Java's bytecode/runtime model provides strong portability, but an application itself can still depend on operating-system-specific native libraries, file paths, system commands, or environment assumptions.

Excellent examples:

```java
Runtime.getRuntime().exec("cmd.exe");
```

or hardcoding:

```
C:\Users\...
```

Now application portability has been reduced.

---

## Question 5: Why is main() declared `public static void main(String[] args)`?

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** ➕ Added

**Difficulty:** Beginner
**Interview Frequency:** Very High

### Short Interview Answer

In the traditional Java application entry point, `main` is `public` so the launcher can access it, `static` so it can be invoked without first creating an object of the application class, `void` because it doesn't return a value to the caller, and `String[] args` receives command-line arguments.

### Break It Down

```java
public static void main(String[] args)
```

**public**
The method must be accessible to the launcher.

**static**
Suppose `main` were:

```java
public void main(String[] args)
```

Then Java would need an object:

```java
Main obj = new Main();
obj.main(args);
```

But which constructor should be used?
What if there are dependencies?
What if no accessible constructor exists?

Using `static` gives a straightforward entry point.

**void**
The traditional Java entry-point method does not return an application result through its Java return type.

**String[] args**
Allows:

```
java Main Abhilash 25
```

Then:

```java
args[0] // "Abhilash"
args[1] // "25"
```

### Follow-Ups

**Can `main()` be overloaded?**
Yes.

```java
public static void main(String[] args) {}

public static void main(int x) {}
```

But the launcher uses the supported entry-point signature.

**Can `main()` be inherited?**
Static methods participate in inheritance, although static methods are hidden rather than dynamically overridden.

**Can I write `String... args`?**
Yes.

```java
public static void main(String... args)
```

Varargs is represented as an array.

### Common Mistake

❌ "main() is static because JVM cannot create objects."
The JVM obviously creates/manages objects.
The real point is that the entry method can be invoked without requiring an application object first.

---

## Question 6: What are Java's primitive data types?

**Priority:** 🔥 Must Know
**Source:** ➕ Added

### Short Interview Answer

Java has eight primitive types: `byte`, `short`, `int`, `long`, `float`, `double`, `char`, and `boolean`. They represent basic values rather than ordinary object instances. Java also provides wrapper classes such as `Integer`, `Long`, and `Boolean` when object behavior is required.

| Primitive | Typical size | Wrapper |
|---|---|---|
| byte | 8-bit | Byte |
| short | 16-bit | Short |
| int | 32-bit | Integer |
| long | 64-bit | Long |
| float | 32-bit | Float |
| double | 64-bit | Double |
| char | 16-bit UTF-16 code unit | Character |
| boolean | JVM-specific representation | Boolean |

### Important Point

Do not confidently answer:

"boolean occupies exactly 1 bit."

Java defines its logical values but does not promise a simple universal object/memory layout corresponding to one bit.

### Why Wrapper Classes?

Collections use reference types:

```java
List<Integer> numbers = new ArrayList<>();
```

not:

```java
// invalid
List<int> numbers;
```

Generics operate on reference types.

### Connection to Autoboxing

```java
Integer x = 10;
```

conceptually involves:

```java
Integer.valueOf(10);
```

And:

```java
int y = x;
```

unboxes the value.

### Interview Trap

```java
Integer a = 100;
Integer b = 100;

System.out.println(a == b);
```

This can lead into wrapper caching and why `equals()` rather than `==` should normally be used for value comparison.

We'll cover that deeply later.

---

## Question 7: What is widening and narrowing type casting?

**Priority:** ⭐ Important | 💻 Coding
**Source:** ➕ Added

### Short Interview Answer

Widening converts a smaller compatible numeric type to a larger one and is normally implicit because it usually doesn't require an explicit cast. Narrowing converts a larger type to a smaller one and requires an explicit cast because information can be lost.

### Widening

```java
int x = 100;

long y = x;
double z = y;
```

Conceptually:

```
int → long → double
```

### Narrowing

```java
double price = 99.99;

int amount = (int) price;

System.out.println(amount); // 99
```

Information was lost.

### Important Counter-Example

Do not think:

larger storage type means every possible value remains mathematically exact.

For example:

```java
long x = 9_007_199_254_740_993L;
double y = x;
```

`double` may not exactly represent every large `long` integer because floating-point precision works differently.

That's an excellent advanced follow-up.

### Common Mistakes

❌ Assuming casts validate values.

```java
int x = 130;
byte b = (byte) x;
```

The result does not remain 130; narrowing can overflow/wrap according to conversion rules.

### Related Concepts

Numeric promotion, overflow, floating point, wrapper conversion.

---

## Question 8: Is Java pass-by-value or pass-by-reference?

**Priority:** 🔥 Must Know | ⚠️ Extremely Tricky
**Source:** ➕ Added

**Difficulty:** Intermediate
**Interview Frequency:** Very High

### Short Interview Answer

Java is always pass-by-value. For primitives, a copy of the primitive value is passed. For objects, the value being copied is the object reference. That's why a method can use the copied reference to modify the same object, but assigning that parameter to a different object does not change the caller's reference.

That is the sentence to master.

### First Case — Primitive

```java
static void change(int x) {
    x = 100;
}

public static void main(String[] args) {
    int a = 10;

    change(a);

    System.out.println(a);
}
```

Output:

```
10
```

Think:

```
main:
a = 10

change:
x = COPY of 10
```

Changing `x` doesn't change `a`.

### Second Case — Object Reference

```java
class User {
    String name;
}
```

```java
static void changeName(User user) {
    user.name = "Rahul";
}

public static void main(String[] args) {
    User u = new User();
    u.name = "Abhilash";

    changeName(u);

    System.out.println(u.name);
}
```

Output:

```
Rahul
```

Freshers sometimes conclude:

"Therefore objects are passed by reference."

No.

Think of memory conceptually:

```
Caller:

u
│
│ reference value
▼
User object
name = "Abhilash"


Method receives COPY of reference:

user
│
│ same reference value
▼
same User object
```

Two variables contain copies of the same reference value.

Therefore:

```java
user.name = "Rahul";
```

changes the shared object.

### Proof That Java Is Not Pass-by-Reference

```java
static void replace(User user) {
    user = new User();
    user.name = "New User";
}
```

```java
User u = new User();
u.name = "Original";

replace(u);

System.out.println(u.name);
```

Output:

```
Original
```

Why?

```
Before:

u -----------+
             |
user --------+----> Original object


inside replace():

u ----------------> Original object

user -------------> New object
```

Only the method's copied variable was reassigned.

### Interviewer Counter Question

**Interviewer:** Then why can the method modify my object?

Because both reference variables initially contain reference values pointing to the same object. We passed the reference by value; we did not pass the caller's variable itself by reference.

That's the interview-quality answer.

---

## Question 9: Explain the static keyword.

**Priority:** 🔥 Must Know | 🧠 Deep Understanding
**Source:** 📘 PDF + expanded

The PDF correctly introduces the central idea: a static member belongs to the class rather than to an individual instance, and discusses static variables, methods, blocks, and nested classes.

### Short Interview Answer

`static` makes a member associated with the class rather than a particular object. A static field is shared at class level, a static method can be invoked without an instance, a static initializer executes during class initialization, and a static nested class doesn't require an instance of the enclosing class.

### Problem

Imagine every employee stores:

```java
class Employee {
    String company = "OpenAI";
}
```

If 10,000 employee objects all logically share the same company property, making it class-level can model that shared state:

```java
class Employee {
    static String company = "OpenAI";
}
```

Conceptually:

```
Class Employee
     |
 company
     ^
     |
+----+----+
|    |    |
e1   e2   e3
```

### Static Method

```java
class Calculator {

    static int add(int a, int b) {
        return a + b;
    }
}
```

Usage:

```java
Calculator.add(10, 20);
```

No `Calculator` instance is required.

### Why Can't a Static Method Directly Access Instance Variables?

```java
class Employee {

    String name;

    static void print() {
        // System.out.println(name);
    }
}
```

Which object's `name`?

```
Employee e1 → "Alice"
Employee e2 → "Bob"
Employee e3 → "John"
```

There is no implicit `this` inside a static context.

### Static Block

```java
class Config {

    static String environment;

    static {
        environment = "production";
    }
}
```

Useful for class-level initialization.

### Real-World Backend Usage

Constants:

```java
public static final String API_VERSION = "v1";
```

Factory/helper operations:

```java
Objects.requireNonNull(value);
```

Utility methods:

```java
Collections.sort(list);
```

### Common Mistakes

❌ "Static means stored permanently."
No.

❌ "Static methods cannot access objects."
They certainly can:

```java
static void print(User user) {
    System.out.println(user.getName());
}
```

They simply don't have an implicit instance through `this`.

### Follow-Up Questions

**Can we override a static method?**
No dynamic overriding. A subclass can declare a same-signature static method; this is called method hiding.

**Can static methods use `this`?**
No.

**Can constructors be static?**
No.

**Can a class itself be static?**
A top-level class cannot be declared static, but a nested class can.

### Tricky Question

```java
Parent p = new Child();
p.show();
```

If `show()` is an overridden instance method:

runtime object influences dispatch

If `show()` is static:

reference/compiler type determines resolution

This distinction is frequently used to test whether a candidate really understands polymorphism.

---

## Question 10: Explain the final keyword.

**Priority:** 🔥 Must Know | ⚠️ Tricky
**Source:** 📘 PDF + expanded

The PDF correctly identifies the three major uses: final variables, methods, and classes.

### Short Interview Answer

`final` restricts modification in different contexts. A final variable can be assigned only once, a final method cannot be overridden, and a final class cannot be extended. For a reference variable, `final` prevents reassigning the reference—it does not automatically make the referenced object immutable.

### Final Primitive

```java
final int x = 10;

// x = 20; // compile-time error
```

### Final Reference

```java
final List<String> names = new ArrayList<>();

names.add("Alice"); // valid
```

But:

```java
// names = new ArrayList<>(); // invalid
```

Important distinction:

```
final reference
     |
     | cannot point somewhere else
     v
mutable ArrayList
     |
     +-- contents CAN change
```

### Final Method

```java
class Parent {
    final void calculate() {}
}
```

A child cannot override it.

### Final Class

```java
final class SecurityToken {
}
```

Cannot:

```java
class MyToken extends SecurityToken {
}
```

### Common Fresher Mistake

"A final object cannot be modified."

Incorrect.

`final` protects the reference assignment, not necessarily the object's internal state.

---

## Question 11: Is final enough to make a class immutable?

**Priority:** 🔥 Must Know | ⚠️ Tricky | 🧠 Deep Understanding
**Source:** ➕ Added; connected to PDF Question 63

### Short Interview Answer

No. Making a class final only prevents subclassing. For true immutability, its state must not be changeable after construction. That usually means private final fields, no mutating methods, controlled construction, and defensive copies for mutable objects.

### Wrong Example

```java
final class Employee {

    private final List<String> skills;

    Employee(List<String> skills) {
        this.skills = skills;
    }

    List<String> getSkills() {
        return skills;
    }
}
```

Looks immutable.

But:

```java
List<String> skills = new ArrayList<>();
skills.add("Java");

Employee e = new Employee(skills);

skills.add("Python");
```

The employee's state changed indirectly.

Also:

```java
e.getSkills().add("Spring");
```

changes it.

### Better Version

```java
final class Employee {

    private final List<String> skills;

    Employee(List<String> skills) {
        this.skills = List.copyOf(skills);
    }

    public List<String> getSkills() {
        return skills;
    }
}
```

The important principle is not a checklist.

It is:

After construction, no external path should be able to mutate observable object state.

This prepares you for the later String immutability interview chain.

---

## Question 12: What is the difference between && and & with booleans?

**Priority:** ⭐ Important | ⚠️ Tricky | 💻 Coding
**Source:** ➕ Added

### Short Interview Answer

`&&` is conditional AND and uses short-circuit evaluation. If the left side is false, Java doesn't need to evaluate the right side. Boolean `&` evaluates both operands.

Example:

```java
String name = null;

if (name != null && name.length() > 3) {
}
```

This is safe.

Why?

```
name != null
     ↓
   false
     ↓
Java stops
```

`name.length()` is never evaluated.

But:

```java
if (name != null & name.length() > 3) {
}
```

both expressions are evaluated, so this can throw `NullPointerException`.

### Real-World Usage

Very common in validation:

```java
if (user != null && user.isActive()) {
}
```

### Counter Question

**What's the difference between `|` and `||`?**

Same principle:

`||` short-circuits
boolean `|` evaluates both

---

## Module 1 — Interviewer Grill Round

Now imagine this is the real interview.

**Interviewer**
What is JVM?

**Candidate**
JVM is the runtime engine responsible for executing Java bytecode. It also handles areas such as class loading, memory management, JIT compilation, garbage collection, and runtime execution.

**Interviewer**
Then what does javac do?

**Candidate**
javac is the Java compiler. It converts Java source code into JVM bytecode stored in `.class` files.

**Interviewer**
So JVM converts .java into .class?

**Candidate**
No. javac performs that compilation. The JVM executes the resulting bytecode.

**Interviewer**
Why is Java platform-independent?

**Candidate**
Because compilation targets JVM bytecode rather than one operating system's native instructions. A compatible JVM on each platform can execute the same bytecode.

**Interviewer**
Is JVM platform-independent?

**Candidate**
The JVM specification provides a common execution model, but actual JVM implementations interact with the underlying OS and hardware. The portable part from an application's perspective is primarily the bytecode.

**Interviewer**
Then explain JDK vs JRE vs JVM.

**Candidate**
JVM executes bytecode. The runtime environment supplies the JVM and runtime facilities needed by Java applications. The JDK adds development tools such as the Java compiler, debugger and other utilities.

**Interviewer**
Why is main() static?

**Candidate**
So the application entry point can be invoked without first creating an instance of the application class.

**Interviewer**
Is Java pass-by-reference for objects?

**Candidate**
No. Java is always pass-by-value. When an object is passed, a copy of the reference value is passed.

**Interviewer**
Then how can a method modify my object?

**Candidate**
Because the caller's reference and the copied parameter reference initially point to the same object. The object can therefore be mutated through either reference.

**Interviewer**
Can the method replace my original reference?

**Candidate**
No. Reassigning the method parameter only changes its local copy of the reference.

**Interviewer**
What does static mean?

**Candidate**
It associates the member with the class rather than with one particular instance.

**Interviewer**
Can a static method access instance variables?

**Candidate**
Not directly through an implicit `this`, because there is no current instance. But it can access instance members if an explicit object reference is supplied.

**Interviewer**
Can static methods be overridden?

**Candidate**
No in the polymorphic sense. A subclass can declare a static method with the same signature, but that's method hiding.

**Interviewer**
If I write:

```java
final ArrayList<String> list = new ArrayList<>();
```

can I add elements?

**Candidate**
Yes. `final` prevents assigning `list` to another ArrayList; it doesn't make the current ArrayList immutable.

**Interviewer**
Therefore is `final class Employee` immutable?

**Candidate**
Not automatically. `final` only prevents subclassing. The fields and exposed state must also be protected from mutation.

---

## Module 1 — Rapid Revision

```
JDK
 ↓ contains development tools + runtime

javac
 ↓
.java → .class

.class
 ↓
Bytecode

JVM
 ↓
ClassLoader
Bytecode verification
Interpreter/JIT
Memory management
GC
Execution
```

Remember these interview sentences:

- Java is always pass-by-value.
- For objects, the value being copied is the reference.
- Bytecode is platform-independent; JVM implementations are platform-specific.
- Static belongs to class-level context, not an individual instance.
- Final reference ≠ immutable object.
- JIT compiles frequently executed bytecode into native code at runtime.

---

## Next module in the master sequence

`02-OOP.md` will start from the PDF's Questions 1–13 and rebuild them into the deeper interviewer chain you requested:

```
Class vs Object
       ↓
Abstraction
       ↓
Encapsulation
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
Why interfaces?
       ↓
Default methods
       ↓
Diamond problem
       ↓
Why no multiple class inheritance?
       ↓
this / super
       ↓
Association / Aggregation / Composition
       ↓
15-question OOP grill round
```

That module also needs an important correction to the PDF's abstract-class-vs-interface discussion, because modern interfaces can contain more than just abstract/default/static methods, and several of the book's 2020-era statements need Java 9+ context.