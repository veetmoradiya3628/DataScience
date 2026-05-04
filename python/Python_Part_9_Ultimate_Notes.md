# Object-Oriented Programming — Part 2: The Four Pillars

---

## Concept Overview

Object-Oriented Programming (OOP) organises code around **objects** (instances of classes) rather than sequential procedures. After mastering the foundational building blocks — classes, objects, constructors, and `self` — the next step is to understand the **four pillars** that make OOP powerful:

| Pillar | Core Idea |
|---|---|
| **Encapsulation** | Data hiding + scope control |
| **Abstraction** | Contract-based programming |
| **Inheritance** | Code reuse across related classes |
| **Polymorphism** | Different objects respond to the same interface |

> **Real-world analogy:** Think of a bank. The **vault** (encapsulation) hides cash. The **bank charter** (abstraction) mandates all branches must offer `pay` and `refund`. **Franchise branches** (inheritance) reuse the parent bank's systems. Every branch can `process_payment()` differently (polymorphism) while sharing the same interface.

These four pillars exist because:
- Procedural programming grows unmanageable at scale (1 million+ lines of code with no structure)
- They provide **security**, **structure**, **reusability**, and **standardisation**
- They are the primary topics in OOP interview questions

---

## Key Terminology

| Term | Meaning |
|---|---|
| **Encapsulation** | Bundling data and methods; controlling external access via scope modifiers |
| **Public** | Accessible from anywhere — no underscore prefix |
| **Protected** | Convention: should only be used inside the class and child classes — single underscore `_` |
| **Private** | Python restricts direct access — double underscore `__`; name mangling applied |
| **Name Mangling** | Python renames `__var` in class `Foo` to `_Foo__var` internally |
| **Getter** | A method that retrieves the value of a (usually private) attribute |
| **Setter** | A method that updates the value of a (usually private) attribute |
| **Inheritance** | A child class acquiring properties and methods of a parent class |
| **Single Inheritance** | One child inherits from one parent |
| **Multi-level Inheritance** | Chain: `C → B → A` |
| **Multiple Inheritance** | One child inherits from two or more parents simultaneously |
| **`super()`** | A reference to the **parent class**; used to call parent methods without naming the parent explicitly |
| **Method Overriding** | A child class redefines a method that already exists in the parent class |
| **MRO** | Method Resolution Order — Python's algorithm for deciding the inheritance chain |
| **Abstraction** | Hiding implementation details; enforcing a contract through abstract classes |
| **Abstract Class** | A class that cannot be instantiated and defines a set of methods that subclasses must implement |
| **`@abstractmethod`** | Decorator that marks a method as mandatory for subclasses |
| **`pass`** | A no-op placeholder that satisfies Python's syntax without executing any logic |
| **Is-a relationship** | Dog **is an** Animal → valid use of inheritance |
| **Has-a relationship** | Car **has an** Engine → use composition (attribute), not inheritance |
| **Polymorphism** | Ability of different objects to respond to the same method name in their own way |
| **Composition** | One class uses another class as an attribute (has-a) |

---

---

# PILLAR 1 — ENCAPSULATION

---

## Concept Overview

**Encapsulation** is the mechanism of bundling data (attributes) and the methods that operate on that data within a class, while controlling how that data can be accessed or modified from outside.

> **Why it exists:** In procedural programming, functions and data are separate. Sharing data across functions requires global variables, which leads to spaghetti code. Encapsulation binds data to its behaviour and provides **access control**.

> **Real-world analogy:** Your bank balance is hidden behind the bank's systems. You don't get to edit the database column directly — you can only deposit or check balance through official bank interfaces (ATM, app, counter). The bank **controls the access**.

> **In Python specifically:** Encapsulation is about **developer communication and convention**, not hard enforcement (unlike Java). Python trusts developers to follow conventions and uses name mangling as a soft barrier.

---

## Scope Levels in Python Classes

Python uses naming conventions to define three levels of access:

```python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name                   # PUBLIC   — no underscore
        self._account_type = "savings"     # PROTECTED — single underscore
        self.__balance = balance           # PRIVATE  — double underscore
```

### Summary Table

| Scope | Syntax | Access | Meaning |
|---|---|---|---|
| **Public** | `self.name` | Anywhere (inside, outside, child class) | Feel free to use everywhere |
| **Protected** | `self._account_type` | Convention: class + child classes only | A warning — "please don't use outside" |
| **Private** | `self.__balance` | Only inside the class (via methods) | Strongest restriction; name mangling applied |

---

## Syntax & Definition

```python
class ClassName:
    def __init__(self):
        self.public_var = value          # No underscore → public
        self._protected_var = value      # Single underscore → protected
        self.__private_var = value       # Double underscore → private
```

---

## Detailed Explanation with Examples

### Example 1: Basic Public, Protected, and Private

```python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name                   # public
        self._account_type = "savings"     # protected
        self.__balance = balance           # private

# Create object
ba1 = BankAccount("Alice", 100000)

print(ba1.name)             # ✅ Works — public
print(ba1._account_type)    # ✅ Works — but you SHOULD NOT use it outside
print(ba1.__balance)        # ❌ AttributeError — private
```

**Output:**
```
Alice
savings
AttributeError: 'BankAccount' object has no attribute '__balance'
```

**Line-by-line breakdown:**
- `self.name = name` — stores name as a **public** attribute; anyone can access it directly
- `self._account_type = "savings"` — stored as **protected**; Python does not prevent access, but the underscore is a signal to developers: "don't use this outside the class"
- `self.__balance = balance` — stored as **private**; Python applies name mangling, renaming it internally to `_BankAccount__balance`

**Why this works:** Python does not truly enforce access control the way Java does. The difference between public, protected, and private is **convention-based** (except private, which uses name mangling to create a practical barrier).

---

### Example 2: Getter and Setter Methods

Since private attributes cannot be accessed directly, we provide **controlled access** through methods.

```python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name
        self._account_type = "savings"
        self.__balance = balance

    # GETTER — retrieves the private balance
    def get_balance(self):
        print(f"Your current balance is: {self.__balance}")

    # SETTER — deposits amount into the private balance
    def deposit(self, amount):
        self.__balance += amount
        print(f"Deposited {amount}. New balance: {self.__balance}")


ba1 = BankAccount("Alice", 100000)
ba1.get_balance()       # ✅ Controlled read
ba1.deposit(5000)       # ✅ Controlled write
ba1.get_balance()       # ✅ Shows updated balance
```

**Output:**
```
Your current balance is: 100000
Deposited 5000. New balance: 105000
Your current balance is: 105000
```

**Why this works:**
- `get_balance` and `deposit` are methods **inside** the class, so they have access to `self.__balance`
- The user/external code can only interact with the balance **through these controlled gates**
- The developer decides what the user is allowed to do

> **Pro tip:** This is the essence of encapsulation — **we as developers grant access, not the user.** The user never directly touches sensitive data; they use the interface we provide.

---

### Example 3: Name Mangling Deep Dive

```python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name
        self.__balance = balance

    def get_balance(self):
        return self.__balance


ba1 = BankAccount("Alice", 100000)

# Python renames __balance to _BankAccount__balance internally
# So accessing via mangled name DOES work (but you shouldn't):
print(ba1._BankAccount__balance)     # ✅ 100000 — accesses via mangled name

# Attempting to SET the private variable from outside using __ notation:
ba1.__balance = 999999               # This does NOT edit the private var!
print(ba1.get_balance())             # Still 100000 — original is unchanged!
print(ba1.__balance)                 # ✅ 999999 — but this is a NEW public variable!
```

**Output:**
```
100000
100000
999999
```

**Line-by-line breakdown:**
- `ba1._BankAccount__balance` — accesses the true private variable via its mangled name (works, but is considered a bad practice)
- `ba1.__balance = 999999` — Python sees `__balance` set on an **instance from outside the class**; since name mangling only applies to declarations *inside* a class definition, Python simply **creates a new public variable** called `__balance` on the object
- `ba1.get_balance()` — still returns 100000 because `get_balance` uses `self.__balance`, which inside the class resolves to `_BankAccount__balance`, not the newly created public `__balance`

> **Interview question:** "Can you edit Python private variables?" — **Yes, technically.** Via name mangling (`_ClassName__var`) or by accidentally creating a new variable with the same `__` name from outside. This is why Python's private is often called a **"gentleman's agreement"**, not true enforcement.

> **Warning:** Python will let you do weird things. In Java, accessing a private variable from outside would be a compile-time error. In Python, you can find workarounds. This is Python's design philosophy — it **trusts the developer**. To achieve real security, use the `@property` decorator (covered in a later class).

---

## How It Works Internally (Name Mangling)

When Python compiles a class, any attribute starting with `__` (double underscore) but **not** ending with `__` gets **renamed** by Python:

```
Rule:
  self.__varname   inside class  Foo
  →  renamed to  →  _Foo__varname
```

```
Example:
  Class:   BankAccount
  Attribute: self.__balance
  Internally stored as: _BankAccount__balance

When you try:
  ba1.__balance      → Python says: AttributeError (no such attribute)
  ba1._BankAccount__balance  → Python says: OK, here it is
```

This is called **Name Mangling**. It prevents accidental overrides in subclasses (not true privacy), but a knowledgeable developer can always bypass it.

---

## Common Mistakes & Debugging

### Mistake 1: Accessing private variable directly

**Wrong:**
```python
class Employee:
    def __init__(self, salary):
        self.__salary = salary

emp = Employee(50000)
print(emp.__salary)  # Trying to access directly
```

**Error:**
```
AttributeError: 'Employee' object has no attribute '__salary'
```

**Why it fails:** Python renamed `__salary` to `_Employee__salary` internally. `__salary` as an external attribute simply does not exist.

**Correct:**
```python
class Employee:
    def __init__(self, salary):
        self.__salary = salary

    def get_salary(self):          # Getter
        return self.__salary

emp = Employee(50000)
print(emp.get_salary())            # ✅ 50000
```

---

### Mistake 2: Thinking you're editing private when you're creating a new variable

**Wrong assumption:**
```python
class Counter:
    def __init__(self):
        self.__count = 0

    def get_count(self):
        return self.__count

c = Counter()
c.__count = 999           # Thinking this edits the private count
print(c.get_count())      # Expecting 999
```

**Output:**
```
0
```

**Why it fails:** `c.__count = 999` outside the class creates a **new public attribute** called `__count` on the instance. The original `_Counter__count` remains untouched.

**Correct:**
```python
class Counter:
    def __init__(self):
        self.__count = 0

    def get_count(self):
        return self.__count

    def set_count(self, value):    # Setter — controlled write
        self.__count = value

c = Counter()
c.set_count(999)
print(c.get_count())               # ✅ 999
```

---

## Best Practices & Pythonic Way

- **Use `_` (protected) to signal "internal use"** — it is Python's community convention for "don't use outside"
- **Use `__` (private) only when you need name mangling** to avoid conflicts in inheritance hierarchies
- **Always provide getter/setter methods** for private attributes — never expose raw private access
- **Do not rely on Python's encapsulation for real security** — use `@property` decorator for production-level control (covered in decorator session)

```python
# Pythonic: use @property for real getter/setter (preview)
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance

    @property
    def balance(self):             # getter
        return self.__balance

    @balance.setter
    def balance(self, amount):     # setter with validation
        if amount < 0:
            raise ValueError("Balance cannot be negative")
        self.__balance = amount
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|---|---|
| You want to hide implementation details | Every attribute in your class is simple and public is fine |
| You need to add validation before setting a value | You're building a tiny, internal-use-only script |
| Working in a team and need to signal access intent | You're just prototyping |
| Designing banking, user auth, or sensitive data models | You'd have to write 50 trivial getters/setters for no benefit |

---

---

# PILLAR 2 — INHERITANCE

---

## Concept Overview

**Inheritance** allows one class (child/subclass) to acquire the **properties and methods** of another class (parent/superclass), enabling **code reuse** without rewriting.

> **Why it exists:** If `Dog`, `Cat`, and `Bird` all need `breathe()` and `eat()` methods, why write them three times? Define them once in `Animal`, and let all animals inherit.

> **Real-world analogy:** You inherit your parents' traits (eye colour, height tendencies) plus develop your own. In Python, a `SmartBulb` class can inherit `Device`'s `power_on()` and add its own `dim_lights()` method.

---

## Types of Inheritance

### 1. Single Inheritance

```
Parent (A)
    ↑
Child (B)
```

One child inherits from one parent.

### 2. Multi-level Inheritance

```
Grandparent (A)
    ↑
Parent (B)  ← inherits from A
    ↑
Child (C)   ← inherits from B (and transitively from A)
```

### 3. Multiple Inheritance

```
Parent A    Parent B
     ↑         ↑
       Child C
```

One child inherits from two or more parents simultaneously.

---

## Syntax & Definition

```python
# Single
class Child(Parent):
    pass

# Multi-level
class C(B):    # B inherits A, so C gets both A and B
    pass

# Multiple
class Child(ParentA, ParentB):
    pass
```

---

## Detailed Explanation with Examples

### Example 1: Single Inheritance — `Device` and `TV`

```python
class Device:
    def power_on(self):
        print("Device is ON")


class TV(Device):             # TV inherits Device
    def show_channels(self):
        print("Playing channels...")


tv1 = TV()
tv1.show_channels()   # TV's own method
tv1.power_on()        # Inherited from Device — no need to rewrite!
```

**Output:**
```
Playing channels...
Device is ON
```

**Line-by-line breakdown:**
- `class TV(Device)` — the bracket syntax signals inheritance; `TV` now has all methods of `Device`
- `tv1.show_channels()` — `TV`'s own method
- `tv1.power_on()` — Python looks in `TV` first (not found), then looks in `Device` (found) → executes it

**Why this works:** Python's method lookup chain automatically traverses the parent class when a method is not found in the child.

---

### Example 2: Multi-level Inheritance

```python
class Device:
    def a1(self):
        print("Device method A1")


class TV(Device):
    def a2(self):
        print("TV method A2")


class SmartBulb(TV):         # SmartBulb → TV → Device
    def a3(self):
        print("SmartBulb method A3")


sb = SmartBulb()
sb.a3()   # Own method
sb.a2()   # From TV
sb.a1()   # From Device (through TV)
```

**Output:**
```
SmartBulb method A3
TV method A2
Device method A1
```

**Why this works:** `SmartBulb` inherits from `TV`, and `TV` inherits from `Device`. The chain is `SmartBulb → TV → Device`, so `SmartBulb` has access to all three levels.

---

### Example 3: Multiple Inheritance — `Camera`, `Phone`, `Smartphone`

```python
class Camera:
    def click_photo(self):
        print("Photo clicked!")


class Phone:
    def make_call(self):
        print("Calling...")


class Smartphone(Camera, Phone):    # Inherits BOTH
    pass


sp = Smartphone()
sp.click_photo()    # From Camera
sp.make_call()      # From Phone
```

**Output:**
```
Photo clicked!
Calling...
```

**Why this works:** `Smartphone` lists both `Camera` and `Phone` in its brackets. Python combines their namespaces into `Smartphone`'s method resolution order.

---

## The `pass` Keyword

`pass` is a **no-operation placeholder**. Python requires a body for class/function/if-else blocks; `pass` satisfies this requirement without executing anything.

```python
# Without pass — ERROR
class EmptyClass:
    # SyntaxError: expected an indented block

# With pass — OK
class EmptyClass:
    pass    # Python reads this line but does nothing

# Useful when structuring code before writing logic
if condition:
    pass    # TODO: implement later
else:
    do_something()
```

> **Key difference from `None`:** `pass` is a **statement** that satisfies syntax. `None` is a **value**. `pass` → "nothing to execute here"; `None` → "this has no value".
> 
> **Key difference from `continue`:** `continue` is a **loop control** statement that skips the rest of the current loop iteration. `pass` is a **general placeholder** that works anywhere. Do not confuse them.

---

## The `super()` Keyword

`super()` returns a **proxy object** that delegates method calls to the parent class. It is the clean, Pythonic way to call a parent's methods without hardcoding the parent class name.

```python
# Without super() — hardcoded, fragile
class Child(Parent):
    def __init__(self):
        Parent.__init__(self)   # must pass self manually, fragile in MRO

# With super() — clean, MRO-aware
class Child(Parent):
    def __init__(self):
        super().__init__()      # Python figures out which parent to call
```

---

## Method Overriding

When a child class defines a method with the **same name** as a parent class method, the child's version **overrides** (replaces) the parent's version for that object.

```python
class Animal:
    def speak(self):
        print("Animal speaks")


class Dog(Animal):
    def speak(self):             # Overrides Animal.speak
        print("Dog barks: Woof!")


d = Dog()
d.speak()   # Calls Dog's version, not Animal's
```

**Output:**
```
Dog barks: Woof!
```

---

### Using `super()` to Call Overridden Parent Method

```python
class Animal:
    def speak(self):
        print("Animal speaks")


class Dog(Animal):
    def speak(self):
        super().speak()          # Explicitly call parent's speak FIRST
        print("Dog barks: Woof!")


d = Dog()
d.speak()
```

**Output:**
```
Animal speaks
Dog barks: Woof!
```

**Why this works:** `super().speak()` tells Python: "call `speak` from my parent class (`Animal`)". This lets you **extend** rather than **completely replace** the parent's behaviour.

---

## Real-World Example: Computer Inheriting Motherboard

```python
class Motherboard:
    def __init__(self, ram, cpu_speed, cpu_type):
        self.ram = ram
        self.cpu_speed = cpu_speed
        self.cpu_type = cpu_type

    def start(self):
        print("Power flow started")

    def stop(self):
        print("Power flow stopped")


class Computer(Motherboard):
    def __init__(self, ram, cpu_speed, cpu_type):
        super().__init__(ram, cpu_speed, cpu_type)  # Initialize parent
        self.os = self.__detect_os()                # Private method call

    def __detect_os(self):                          # Private method
        if self.cpu_type.lower() == "mac chip":
            return "macOS"
        else:
            return "Other OS"

    def show_properties(self):
        print(f"RAM: {self.ram}")
        print(f"CPU Speed: {self.cpu_speed}")
        print(f"OS: {self.os}")


comp1 = Computer("16GB", "4.2GHz", "Mac Chip")
comp1.show_properties()
comp1.start()           # Inherited from Motherboard
comp1.stop()            # Inherited from Motherboard
```

**Output:**
```
RAM: 16GB
CPU Speed: 4.2GHz
OS: macOS
Power flow started
Power flow stopped
```

**Key teaching points:**
- `super().__init__(...)` initialises `Motherboard`'s attributes; without this, `self.cpu_type` would not exist when `__detect_os` tries to use it
- `self.__detect_os()` — a private method; calling it with `self.__` inside the class is valid; calling `comp1.__detect_os()` from outside would fail
- The `Computer` user gets `start()` and `stop()` without them being redefined — pure inheritance

---

## MRO — Method Resolution Order

When using multiple inheritance, Python determines **which class's method to call** using MRO, which follows the **C3 Linearisation** algorithm.

```python
class A:
    def __init__(self):
        super().__init__()
        print("A")


class B(A):
    def __init__(self):
        super().__init__()
        print("B")


class C(A):
    def __init__(self):
        super().__init__()
        print("C")


class D(B, C):             # Diamond problem — B and C both inherit A
    def __init__(self):
        super().__init__()
        print("D")


# View the MRO chain
print(D.__mro__)
```

**Output:**
```
(<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
```

**Creating the object:**
```python
d = D()
```

**Output:**
```
A
C
B
D
```

**Dry-run (bottom-up then print):**

```
D.__init__ called → calls super().__init__() → goes to B (per MRO: D→B→C→A)
B.__init__ called → calls super().__init__() → goes to C (per MRO: next after B is C)
C.__init__ called → calls super().__init__() → goes to A
A.__init__ called → prints "A" → returns
C continues → prints "C" → returns
B continues → prints "B" → returns
D continues → prints "D"
```

> **The Diamond Problem:** `D` inherits `B` and `C`; both `B` and `C` inherit `A`. Without MRO, Python wouldn't know whether to go `D→B→A` or `D→C→A`, potentially running `A.__init__` twice. MRO creates a **linear chain** (`D→B→C→A`) that guarantees each class is visited exactly once.

> **Java vs Python:** Java does **not** support multiple inheritance for classes (only interfaces) precisely to avoid the diamond problem. Python handles it with MRO. Use multi-level inheritance when you want a clear, unambiguous chain.

---

## Is-A vs Has-A (Design Guideline)

| Relationship | When to use | Example |
|---|---|---|
| **Is-a** | Child class *is a type of* parent → use inheritance | `Dog` is an `Animal` → `class Dog(Animal)` |
| **Has-a** | Class *contains* another class as a component → use composition | `Car` has an `Engine` → `self.engine = Engine()` |

```python
# IS-A: correct inheritance
class Animal:
    def breathe(self):
        print("Breathing...")

class Dog(Animal):          # Dog IS-A Animal ✅
    def bark(self):
        print("Woof!")

# HAS-A: correct composition
class Engine:
    def start(self):
        print("Engine started")

class Car:
    def __init__(self):
        self.engine = Engine()   # Car HAS-A Engine ✅

    def drive(self):
        self.engine.start()
        print("Driving...")
```

> **Pro tip:** Before writing `class Child(Parent)`, ask yourself: "Is `Child` a type of `Parent`?" If yes → inherit. If no → use composition.

---

## Common Mistakes & Debugging

### Mistake 1: Forgetting `super().__init__()` when parent has an `__init__`

**Wrong:**
```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        self.breed = breed     # Forgot super().__init__(name)!

d = Dog("Rex", "Labrador")
print(d.name)    # AttributeError: 'Dog' object has no attribute 'name'
```

**Correct:**
```python
class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)    # Initialise parent's attributes
        self.breed = breed

d = Dog("Rex", "Labrador")
print(d.name)     # ✅ Rex
print(d.breed)    # ✅ Labrador
```

---

### Mistake 2: Confusing `super()` with inheritance itself

`super()` is **not** how you inherit — you inherit via the bracket syntax `class Child(Parent)`.  
`super()` is used **after** inheriting to **call a method on the parent**.

```python
# Inheritance = bracket syntax
class Child(Parent):    # This is inheritance
    pass

# super() = call parent method
class Child(Parent):
    def __init__(self):
        super().__init__()   # This calls Parent.__init__
```

---

## Best Practices & Pythonic Way

- **Always call `super().__init__()`** in the child's `__init__` when the parent has one — ensures parent state is initialised
- **Prefer multi-level over multiple inheritance** to avoid MRO complexity
- **Follow is-a rule** when designing class hierarchies
- **Use composition (has-a) for loosely related classes** — it produces less coupling

---

## When to Use / When NOT to Use Inheritance

| Use When... | Avoid When... |
|---|---|
| There is a clear is-a relationship | The relationship is has-a |
| You want to extend/override parent behaviour | You just want to reuse a few utility functions |
| Building hierarchies (Animal → Dog → GoldenRetriever) | You'll need deep nesting with many levels (prefer composition) |
| Framework/library design where contracts matter | Quick scripts with no future reuse |

---

---

# PILLAR 3 — ABSTRACTION

---

## Concept Overview

**Abstraction** creates a **contract** — a set of rules (method signatures) that any conforming class must implement. It hides the complexity of implementation from the consumer while enforcing a standard interface.

> **Why it exists:** Without abstraction, different developers might implement the same concept (e.g., `pay()`) in completely different ways — some call it `make_payment()`, others call it `send_money()`. Abstraction enforces a **common vocabulary and structure**.

> **Real-world analogy:** Tata Motors has an internal **SOP (Standard Operating Procedure)** for every car model: 4 doors, 4 wheels, 1 engine, 300-point welding. Any new car model must satisfy this contract. The contract (abstract class) defines the *what*, not the *how*.

> **Google Form analogy:** An abstract class is like a Google Form with mandatory (`*`) fields. You cannot submit (instantiate) unless all required fields (abstract methods) are filled in (implemented).

> **Java vs Python:** Java calls this an **interface**. Python calls it an **abstract class** using the `ABC` module. The concept is identical.

---

## Syntax & Definition

```python
from abc import ABC, abstractmethod   # Step 1: Import ABC and abstractmethod

class ContractName(ABC):              # Step 2: Inherit ABC to make it abstract
    
    @abstractmethod                   # Step 3: Mark each required method
    def required_method(self, ...):
        pass                          # No logic needed — just the signature

    @abstractmethod
    def another_required_method(self, ...):
        pass
```

**Rules:**
1. Import `ABC` and `abstractmethod` from the `abc` module
2. The abstract class must inherit `ABC`
3. Every method that **must** be implemented by subclasses must be decorated with `@abstractmethod`
4. Any class inheriting the abstract class **must implement all abstract methods**, or it cannot be instantiated

---

## Detailed Explanation with Examples

### Example 1: Payment Contract — Basic Abstraction

```python
from abc import ABC, abstractmethod


class Payment(ABC):                  # Abstract class — the CONTRACT
    
    @abstractmethod
    def pay(self, amount, currency):
        pass                         # No logic — just the contract

    @abstractmethod
    def refund(self, amount):
        pass


class IDFCBank(Payment):             # Inheriting the contract
    
    def pay(self, amount, currency):                 # Must implement
        print(f"IDFC paying {amount} {currency}")

    def refund(self, amount):                        # Must implement
        print(f"IDFC refunding {amount}")

    def new_user(self):                              # Own method
        print("New IDFC user registered")


# Create object — works because all abstract methods are implemented
idfc = IDFCBank()
idfc.pay(500, "INR")
idfc.refund(100)
idfc.new_user()
```

**Output:**
```
IDFC paying 500 INR
IDFC refunding 100
New IDFC user registered
```

---

### Example 2: What happens if you don't implement all abstract methods?

```python
from abc import ABC, abstractmethod


class Payment(ABC):
    @abstractmethod
    def pay(self, amount, currency):
        pass

    @abstractmethod
    def refund(self, amount):
        pass


class IncompleteBank(Payment):
    def pay(self, amount, currency):     # Implemented ✅
        print("Paying...")

    # refund NOT implemented ❌


# Trying to create object
bank = IncompleteBank()   # TypeError!
```

**Error:**
```
TypeError: Can't instantiate abstract class IncompleteBank
with abstract methods refund
```

**Why it fails:** Python enforces the contract. Until all `@abstractmethod` methods are implemented, the class cannot be instantiated. This is the **entire purpose** of abstraction — enforcing standards.

---

### Example 3: Multiple Banks Conforming to the Same Contract

```python
from abc import ABC, abstractmethod


class Payment(ABC):
    @abstractmethod
    def pay(self, amount, currency):
        pass

    @abstractmethod
    def refund(self, amount):
        pass


class IDFCBank(Payment):
    def pay(self, amount, currency):
        print(f"[IDFC] Processing payment: {amount} {currency}")

    def refund(self, amount):
        print(f"[IDFC] Processing refund: {amount}")


class SBIBank(Payment):
    def pay(self, amount, currency):
        print(f"[SBI] Processing payment: {amount} {currency}")

    def refund(self, amount):
        print(f"[SBI] Processing refund: {amount}")


# Both banks follow the SAME contract, with DIFFERENT implementations
idfc = IDFCBank()
sbi = SBIBank()

idfc.pay(1000, "INR")
sbi.pay(1000, "INR")
```

**Output:**
```
[IDFC] Processing payment: 1000 INR
[SBI] Processing payment: 1000 INR
```

**Why this matters:** Even though `IDFCBank` and `SBIBank` implement `pay` differently, they both guarantee it exists. This powers **polymorphism** — you can call `.pay()` on any `Payment` subclass without knowing which bank it is.

---

## How It Works Internally

```
from abc import ABC, abstractmethod
         ↓
ABC is a helper class provided by Python's 'abc' module
abstractmethod is a decorator that flags a method as mandatory

When you do:
  class Payment(ABC):  →  Payment is marked as abstract
  @abstractmethod      →  pay() and refund() are flagged

When Python tries to instantiate IncompleteBank:
  Python checks: "Does IncompleteBank implement all @abstractmethod methods?"
  If NO  → raises TypeError before __init__ even runs
  If YES → allows instantiation
```

---

## Common Mistakes & Debugging

### Mistake 1: Forgetting to inherit `ABC`

**Wrong:**
```python
from abc import ABC, abstractmethod

class Payment:              # Not inheriting ABC!
    @abstractmethod
    def pay(self):
        pass

class Bank(Payment):
    pass

b = Bank()    # No error — @abstractmethod is silently ignored!
```

**Why it fails:** `@abstractmethod` only works when the class inherits `ABC`. Without `ABC`, it is just a regular decorator with no enforcement.

**Correct:**
```python
class Payment(ABC):         # Must inherit ABC
    @abstractmethod
    def pay(self):
        pass
```

---

### Mistake 2: Forgetting `@abstractmethod` on some methods

```python
class Payment(ABC):
    @abstractmethod
    def pay(self):
        pass

    def refund(self):       # No @abstractmethod — optional, NOT enforced!
        pass
```

**Result:** `refund` is no longer mandatory. Subclasses can skip it without error. If your intent was to make it mandatory, always add `@abstractmethod`.

---

## Best Practices

- **Use abstraction when designing frameworks or APIs** where multiple implementations will exist
- **Keep abstract classes thin** — only declare the contract, not the logic
- **Combine with inheritance**: abstract class defines the what, subclasses define the how
- **Use `pass` or `raise NotImplementedError`** inside abstract method bodies for clarity

```python
# Extra clarity variant
@abstractmethod
def pay(self, amount, currency):
    raise NotImplementedError("Subclasses must implement pay()")
```

---

## When to Use / When NOT to Use Abstraction

| Use When... | Avoid When... |
|---|---|
| Multiple classes must share the same interface | Only one class will ever implement the contract |
| You're building a plugin/framework architecture | You're building a quick prototype |
| You need to enforce a standard across a team | There's no need for multiple implementations |
| Designing payment gateways, database adapters, notification systems | All logic lives in a single class |

---

---

# PILLAR 4 — POLYMORPHISM (Preview)

---

## Concept Overview

**Polymorphism** means "many forms." It is the ability of **different objects to respond to the same method call** in their own way.

> **Real-world analogy:** Pressing the "volume up" button on a TV remote, a phone, or a speaker all increase volume — the same interface (`volume_up`), but different internal behaviour.

Polymorphism is closely tied to abstraction: when every `Payment` subclass implements `pay()`, you can call `.pay()` on any of them without caring which specific bank it is. This will be covered in depth in the next class.

```python
# Preview
for bank in [IDFCBank(), SBIBank()]:
    bank.pay(500, "INR")   # Same call, different behaviour — POLYMORPHISM
```

---

---

# COMPREHENSIVE COMPARISON TABLE

| Feature | Encapsulation | Inheritance | Abstraction | Polymorphism |
|---|---|---|---|---|
| **Core purpose** | Data hiding & access control | Code reuse | Contract enforcement | Unified interface, multiple behaviours |
| **Python mechanism** | `_`, `__`, getters/setters | `class Child(Parent)` | `ABC` + `@abstractmethod` | Method overriding |
| **Can be instantiated?** | Yes | Yes | No (abstract class itself) | Yes |
| **Forces implementation?** | No | No | Yes (`@abstractmethod`) | No (but overriding is by convention) |
| **Java equivalent** | `private`, `protected`, `public` | `extends` | `interface` / `abstract class` | Method overriding |
| **Python uniqueness** | Convention-based, not enforced | Supports multiple inheritance (MRO) | `ABC` module required | Duck typing also enables it |

---

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What are the four pillars of OOP?**  
   **A:** Encapsulation, Inheritance, Abstraction, and Polymorphism.

2. **Q: What is the difference between public, protected, and private in Python?**  
   **A:** Public (no underscore) is accessible anywhere. Protected (single `_`) is a convention — accessible but signals "internal use only." Private (double `__`) applies name mangling (`_ClassName__var`), making direct access harder but not impossible.

3. **Q: Can you access a private variable in Python from outside the class?**  
   **A:** Yes, technically, via the mangled name: `obj._ClassName__varname`. Python does not truly enforce private access the way Java does.

4. **Q: What is name mangling?**  
   **A:** Python automatically renames `self.__var` defined inside `class Foo` to `_Foo__var`. This prevents accidental clashes in subclasses.

5. **Q: What is the difference between method overriding and method overloading?**  
   **A:** Overriding: child class redefines a parent method with the same name. Overloading: same method name with different parameter signatures. Python does **not** natively support overloading (the last definition wins). It can be simulated with `*args` or default arguments.

6. **Q: What is `super()` used for?**  
   **A:** `super()` is a reference to the parent class. Used to call the parent's `__init__` or any other method from within a child class, without hardcoding the parent's name.

7. **Q: What is the difference between multi-level and multiple inheritance?**  
   **A:** Multi-level is a chain: `C → B → A`. Multiple is a fork: `D` inherits both `B` and `C` simultaneously. Java supports multi-level but not multiple inheritance. Python supports both.

8. **Q: What is the diamond problem?**  
   **A:** When `D` inherits `B` and `C`, and both `B` and `C` inherit `A`, there is ambiguity about which `A` to call. Python resolves this using MRO (Method Resolution Order).

9. **Q: What is an abstract class? Can it be instantiated?**  
   **A:** An abstract class defines a contract — a set of methods that subclasses must implement. It cannot be instantiated directly. In Python, created by inheriting `ABC` and using `@abstractmethod`.

10. **Q: What is MRO?**  
    **A:** Method Resolution Order — Python's C3 Linearisation algorithm that creates a linear chain from a potentially complex inheritance graph. View it with `ClassName.__mro__`.

11. **Q: What is the `pass` keyword?**  
    **A:** A no-op statement that satisfies Python's syntax requirements for block structures (class, function, if/else) when no logic is needed yet.

12. **Q: What is the difference between `pass` and `continue`?**  
    **A:** `pass` is a general placeholder usable anywhere. `continue` is a loop control statement that skips the rest of the current iteration and only works inside loops.

---

### Tricky Output Questions

**Q1: What will this print?**
```python
class A:
    def __init__(self):
        self.__x = 10

a = A()
print(a.__x)
```
**Answer:** `AttributeError: 'A' object has no attribute '__x'`  
**Why:** Name mangling renames `__x` to `_A__x`. Accessing `a.__x` looks for a normal attribute named `__x`, which doesn't exist.

---

**Q2: What will this print?**
```python
class A:
    def speak(self):
        print("A speaks")

class B(A):
    def speak(self):
        super().speak()
        print("B speaks")

b = B()
b.speak()
```
**Answer:**
```
A speaks
B speaks
```
**Why:** `super().speak()` calls `A.speak()` first (prints "A speaks"), then `B.speak()` continues (prints "B speaks").

---

**Q3: What will this print?**
```python
class A:
    def __init__(self):
        self.__val = 100

    def get_val(self):
        return self.__val

a = A()
a.__val = 999
print(a.get_val())
print(a.__val)
```
**Answer:**
```
100
999
```
**Why:** `a.__val = 999` creates a **new public attribute** called `__val` on the instance (Python doesn't apply name mangling for outside-class assignment). `get_val()` still accesses `_A__val` (which is 100). The direct `a.__val` returns the new public attribute (999).

---

**Q4: What will this print?**
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    pass

c = Circle()
```
**Answer:** `TypeError: Can't instantiate abstract class Circle with abstract method area`  
**Why:** `Circle` inherits `Shape` but does not implement `area()`, so it cannot be instantiated.

---

### One-Liner Challenges

- **Challenge:** "Check the MRO of class D that inherits B and C"  
  **Solution:** `print(D.__mro__)`

- **Challenge:** "Access a private `__balance` variable of `BankAccount` from outside"  
  **Solution:** `obj._BankAccount__balance`

- **Challenge:** "Create an empty class that does nothing"  
  **Solution:** `class Empty: pass`

---

## Quick-Reference Cheat Sheet

```
OOP PART 2 — CHEAT SHEET
=========================

ENCAPSULATION
─────────────
self.name          → public       accessible anywhere
self._name         → protected    convention: class + child class
self.__name        → private      name mangled to _ClassName__name
get_xxx()          → getter       controlled read of private attr
set_xxx(val)       → setter       controlled write of private attr
name mangling      → self.__x in class Foo → stored as _Foo__x
GOTCHA: a.__x = 5 from outside creates NEW var, does NOT edit private

INHERITANCE
───────────
class Child(Parent):          → single inheritance
class C(B): / class B(A):     → multi-level
class Child(A, B):            → multiple inheritance
super().__init__(...)         → call parent constructor
super().method()              → call parent method
Child.__mro__                 → view method resolution order
pass                          → empty placeholder block
is-a  → inherit
has-a → composition (store as attribute)
GOTCHA: Java doesn't support multiple; Python does (via MRO)

METHOD OVERRIDING
─────────────────
Same method name in child → child's version wins
Use super().method() to ALSO call the parent version

ABSTRACTION
───────────
from abc import ABC, abstractmethod
class MyABC(ABC):             → create abstract class
    @abstractmethod           → mark method as mandatory
    def must_implement(self): pass

class Child(MyABC):           → must implement all @abstractmethod
    def must_implement(self): → or TypeError on instantiation

POLYMORPHISM (preview)
──────────────────────
Same method name → different behaviours in different classes
Works naturally with abstract classes + method overriding
```

---

## Summary (TL;DR)

- **Encapsulation**: Use naming conventions (`_`, `__`) to signal data access intent. Python does not truly enforce privacy — it relies on developer responsibility. Use getter/setter methods to provide controlled access to private data.
- **Name Mangling**: Python renames `__var` in `class Foo` to `_Foo__var`. Accessing via `__var` from outside fails; via `_Foo__var` works but is bad practice.
- **Inheritance**: Child class reuses parent's code via `class Child(Parent)`. Types: single, multi-level, multiple. Use `super()` to call parent methods. Method overriding lets child redefine parent methods.
- **`pass`**: A no-op placeholder — satisfies syntax without executing anything. Not a loop control statement (`continue` is).
- **`super()`**: A reference to the parent class; used inside child methods to call parent's `__init__` or any other method without hardcoding the parent name.
- **MRO**: Python's algorithm for creating a linear chain from a complex inheritance graph. Prevents the diamond problem. View via `ClassName.__mro__`.
- **Abstraction**: Create contracts using `ABC` + `@abstractmethod`. Subclasses must implement all abstract methods or they cannot be instantiated. Python's equivalent of Java's `interface`.

---

## Flashcard-Style Recall (Q&A pairs)

- **Q: What are the three access modifiers in Python?**  
  **A:** Public (no underscore), Protected (single `_`), Private (double `__`).

- **Q: What does name mangling do?**  
  **A:** Python renames `self.__var` in `class Foo` to `_Foo__var` internally to prevent accidental external access.

- **Q: What is `super()` used for?**  
  **A:** To call a method on the parent class from within the child class without hardcoding the parent's name.

- **Q: What is the difference between multi-level and multiple inheritance?**  
  **A:** Multi-level is a chain (A→B→C); multiple is one child inheriting from two parents simultaneously (class D(B, C)).

- **Q: What is MRO?**  
  **A:** Method Resolution Order — Python's algorithm for determining the order in which classes are searched when a method is called in a multiple/multi-level inheritance hierarchy.

- **Q: What is `pass`?**  
  **A:** A no-operation statement that satisfies Python's block syntax without executing any logic. Used as a placeholder.

- **Q: Can you instantiate an abstract class?**  
  **A:** No. Attempting to instantiate an abstract class (one with unimplemented `@abstractmethod` methods) raises a `TypeError`.

- **Q: What modules are needed for abstraction in Python?**  
  **A:** `from abc import ABC, abstractmethod`.

- **Q: What is the is-a vs has-a rule?**  
  **A:** is-a → use inheritance (`Dog` is an `Animal`); has-a → use composition (`Car` has an `Engine`).

- **Q: What error do you get when accessing a private variable directly?**  
  **A:** `AttributeError: 'ClassName' object has no attribute '__varname'`.

- **Q: What error occurs when inheriting an abstract class without implementing all abstract methods?**  
  **A:** `TypeError: Can't instantiate abstract class ClassName with abstract method(s) method_name`.

- **Q: How do you view the MRO of a class?**  
  **A:** `ClassName.__mro__` or `ClassName.mro()`.

- **Q: What is the difference between `pass` and `continue`?**  
  **A:** `pass` is a general no-op placeholder usable anywhere. `continue` is a loop control statement that skips the current iteration — only valid inside loops.

- **Q: Self and the object — are they the same?**  
  **A:** Yes. `self` inside a method and the object variable outside both point to the same memory address. `self` is the internal reference; the variable name is the external reference.

---

## Related Topics & Connections

- **Prerequisite knowledge:** Classes, objects, `__init__`, `self`, scope (local vs global), decorators (basic understanding)
- **Closely connected to this topic:** `@property` decorator (real getter/setter enforcement), `@classmethod`, `@staticmethod`, Duck Typing, Dunder/Magic methods
- **What to learn next:** Polymorphism (in depth) → `@property` decorator → Magic methods (`__str__`, `__repr__`, `__len__`) → Design Patterns (Factory, Strategy, Observer)
- **Where you'll see this in the wild:** Every major Python library uses inheritance and abstraction. E.g., Ultralytics YOLO's `PosePredictor(DetectionPredictor)` — a pose predictor *is a* detection predictor.
