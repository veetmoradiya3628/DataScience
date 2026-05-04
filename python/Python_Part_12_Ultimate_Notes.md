# Python Day 14 — Special Methods, Special Functions & Comprehensions

> **The Final Python Class** — This session completes the Python module, covering Dunder/Special Methods in OOP, Property Decorators, the `__name__` guard, Lambda, `map`, `filter`, `zip`, `enumerate`, and List/Dictionary Comprehension.

---

## Concept Overview

This session covers two major pillars:

1. **Special Methods (Dunder Methods)** — Built-in hooks that Python calls automatically when you use operators or built-in functions (`+`, `len()`, `[]`, `print()`) on your custom objects. They let you make your classes behave like native Python types.

2. **Special Functions & Comprehensions** — Concise, Pythonic tools (`lambda`, `map`, `filter`, `zip`, `enumerate`) and a powerful syntax (`list/dict comprehension`) that makes iteration elegant and expressive.

**Why they exist:** Without special methods, you could never write `wallet1 + wallet2`. Without comprehensions, filtering a list always required a 5-line function. These features allow you to write *less code that does more*.

**Real-world analogy:** Special methods are like **standard electrical sockets** — your custom appliance (class) can plug into any Python power source (`+`, `len()`, `[]`) as long as it has the right pins (implements the right dunder methods). Comprehensions are like an **assembly line with a filter** — you feed in raw material, apply a rule, and receive finished goods in one step.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Dunder Method** | Double-underscore method (`__init__`, `__str__`, etc.); Python calls these automatically |
| **Special Method** | Synonym for dunder method |
| **Operator Overloading** | Defining what `+`, `==`, etc. do for your custom class |
| **Attribute** | A variable that belongs to a class or object (e.g., `self.balance`) |
| **Property Decorator** | `@property` — turns a method into an attribute-like accessor |
| **Getter** | A function/method that **reads** a value |
| **Setter** | A function/method that **writes/updates** a value |
| **Deleter** | A function/method that **removes** a value |
| **CRUD** | Create, Read, Update, Delete — the four basic data operations |
| **Lambda** | An anonymous, one-line function |
| **Iterator / Iterable** | Any object you can loop over (list, tuple, dict, range, zip, etc.) |
| **List Comprehension** | A compact syntax to build a list from an iterable in one line |
| **Dictionary Comprehension** | Same concept but the output is a dictionary |
| **Name Mangling** | Python renames `__attr` to `_ClassName__attr` internally to enforce privacy |
| **`__name__` guard** | `if __name__ == "__main__":` — runs code only when the file is executed directly |

---

## Part 1 — Special (Dunder) Methods in OOP

---

### Syntax & Definition

```python
class MyClass:
    def __init__(self, value):          # Constructor
        self.value = value

    def __str__(self):                  # Human-readable string (for users)
        return f"MyClass with value={self.value}"

    def __repr__(self):                 # Official representation (for developers)
        return f"MyClass(value={self.value})"

    def __add__(self, other):           # Enables +
        return MyClass(self.value + other.value)

    def __len__(self):                  # Enables len()
        return self.value

    def __getitem__(self, index):       # Enables [] indexing
        return self.value[index]

    def __eq__(self, other):            # Enables ==
        return self.value == other.value
```

Each dunder method maps to a **Python operator or built-in function**:

| Dunder Method | Triggered by |
|---------------|-------------|
| `__init__` | `ClassName(args)` — object creation |
| `__str__` | `str(obj)`, `print(obj)` with `{obj!s}` in f-string |
| `__repr__` | `repr(obj)`, just writing `obj` in REPL/Jupyter |
| `__add__` | `obj1 + obj2` |
| `__sub__` | `obj1 - obj2` |
| `__mul__` | `obj1 * obj2` |
| `__eq__` | `obj1 == obj2` |
| `__len__` | `len(obj)` |
| `__getitem__` | `obj[index]` |

---

### Example 1: `__str__` and `__repr__`

```python
class Car:
    def __init__(self, manufacturer, car_name):
        self.manufacturer = manufacturer
        self.car_name = car_name

    def __str__(self):
        # Intended for end users — simple, readable description
        return f"Manufacturer: {self.manufacturer} — We produce cars."

    def __repr__(self):
        # Intended for developers — detailed technical information
        return (
            f"Car(manufacturer='{self.manufacturer}', "
            f"car_name='{self.car_name}')"
        )

# Usage
car1 = Car("Toyota", "Innova")

print(str(car1))   # calls __str__
print(repr(car1))  # calls __repr__
print(car1)        # calls __repr__ as fallback when __repr__ is defined

# F-string specifiers
print(f"{car1!s}")   # forces __str__
print(f"{car1!r}")   # forces __repr__
```

**Output:**
```
Manufacturer: Toyota — We produce cars.
Car(manufacturer='Toyota', car_name='Innova')
Car(manufacturer='Toyota', car_name='Innova')
Manufacturer: Toyota — We produce cars.
Car(manufacturer='Toyota', car_name='Innova')
```

**Line-by-line breakdown:**
- `__str__`: Returns a friendly string. When you call `str(car1)` or use `{car1!s}` in an f-string, Python calls this.
- `__repr__`: Returns a developer-friendly string. When you type just `car1` in a Jupyter cell or use `{car1!r}`, Python calls this.
- **Fallback rule**: If `__str__` is NOT defined but `__repr__` IS, `print(obj)` uses `__repr__`. If neither is defined, you get `<__main__.Car object at 0x...>`.

**Why this works:** Python internally calls `type(obj).__str__(obj)` when you do `str(obj)`. It's just a regular method call — you define the logic, Python calls it at the right time.

> **Pro tip:** Always implement `__repr__`. It is more important than `__str__` because `repr()` is called in debugging, logging, and the Python REPL. `__str__` is optional — it's cosmetic.

---

### Example 2: `__add__` — Operator Overloading

**Problem:** You have two `Wallet` objects and want to write `w1 + w2` to combine their cash values.

```python
class Wallet:
    def __init__(self, cash):
        self.cash = cash

    def __repr__(self):
        return f"Wallet(cash={self.cash})"

    def __add__(self, other):
        # self  → the LEFT  operand (w1)
        # other → the RIGHT operand (w2)
        combined_cash = self.cash + other.cash
        return Wallet(combined_cash)   # Return a NEW Wallet object

    def __sub__(self, other):
        return Wallet(self.cash - other.cash)

    def __eq__(self, other):
        return self.cash == other.cash

# Usage
w1 = Wallet(50)
w2 = Wallet(30)

combined = w1 + w2          # Python internally calls: w1.__add__(w2)
print(combined)             # Wallet(cash=80)
print(combined.cash)        # 80

# Subtraction
diff = w1 - w2
print(diff)                 # Wallet(cash=20)

# Equality
w3 = Wallet(50)
print(w1 == w3)             # True
print(w1 == w2)             # False
```

**Output:**
```
Wallet(cash=80)
80
Wallet(cash=20)
True
False
```

**How Python translates `w1 + w2`:**
```
w1 + w2
  └── Python sees the + operator on a Wallet object
  └── Looks for __add__ on the LEFT operand (w1)
  └── Calls: w1.__add__(w2)
  └── Inside __add__: self=w1, other=w2
  └── Returns: Wallet(80)
```

**Why does `__add__` return a `Wallet`?**  
Because `1 + 1` returns an `int`, not just a number floating in space. When you add two `Wallet` objects, the result should also be a `Wallet` (so you can chain operations and access `.cash`).

> **Pro tip:** `w1 + w2` calls `w1.__add__(w2)`. `w2 + w1` calls `w2.__add__(w1)`. They are different calls! Both objects have their own `__add__`.

---

### Example 3: `__len__` and `__getitem__`

```python
class ATM:
    def __init__(self):
        self.transaction_history = []

    def do_transaction(self, amount):
        self.transaction_history.append(amount)
        print(f"Transaction done: {amount}")

    def __len__(self):
        # Defines what len(atm_object) means
        return len(self.transaction_history)

    def __getitem__(self, index):
        # Defines what atm_object[index] means
        return self.transaction_history[index]

# Usage
atm = ATM()
atm.do_transaction(500)
atm.do_transaction(200)
atm.do_transaction(-100)
atm.do_transaction(1000)
atm.do_transaction(300)

print(len(atm))          # 5  → calls __len__
print(atm[2])            # -100 → calls __getitem__(2)
print(atm[0])            # 500
```

**Output:**
```
Transaction done: 500
Transaction done: 200
Transaction done: -100
Transaction done: 1000
Transaction done: 300
5
-100
500
```

**Why this matters:** Without `__len__`, calling `len(atm)` raises `TypeError: object of type 'ATM' has no len()`. Without `__getitem__`, `atm[0]` raises `TypeError`. These dunder methods tell Python exactly what to do with your custom class when native functions are applied to it.

---

### `isinstance()` — Type Checking

```python
# Syntax: isinstance(object, classinfo)
# Returns True if object is an instance of classinfo, else False

print(isinstance(1, int))          # True
print(isinstance(1, float))        # False
print(isinstance(1, (int, float))) # True  ← check multiple types at once

# Custom class usage
w = Wallet(100)
print(isinstance(w, Wallet))       # True
print(isinstance(w, int))          # False
```

**Real-world use:** Inside `__add__`, you can validate the type before adding:

```python
def __add__(self, other):
    if not isinstance(other, Wallet):
        raise TypeError(f"Cannot add Wallet with {type(other)}")
    return Wallet(self.cash + other.cash)
```

---

### How Special Methods Work Internally

```
Python sees:  w1 + w2
                │
                ▼
        Python checks type(w1) → Wallet
                │
                ▼
        Looks up __add__ in Wallet class
                │
                ▼
        Calls Wallet.__add__(w1, w2)
        (where self=w1, other=w2)
                │
                ▼
        Your code runs: Wallet(self.cash + other.cash)
                │
                ▼
        Returns new Wallet(80)
```

This is called **operator dispatch** — the `+` symbol is just a shortcut that Python maps to `__add__`. Every built-in operator has a corresponding dunder method.

---

### Common Mistakes — Dunder Methods

#### Mistake 1: Printing instead of returning in `__str__`

**Wrong:**
```python
def __str__(self):
    print(f"Car: {self.manufacturer}")   # ← WRONG: print, not return
```
**Error:**
```
None
```
**Why it fails:** `__str__` must **return** a string. If you `print`, the method returns `None`, so `str(obj)` gives you `None`.

**Correct:**
```python
def __str__(self):
    return f"Car: {self.manufacturer}"  # ← return, not print
```

#### Mistake 2: Returning the wrong type from `__len__`

**Wrong:**
```python
def __len__(self):
    return "five"   # ← must be an integer
```
**Error:**
```
TypeError: 'str' object cannot be interpreted as an integer
```
**Correct:**
```python
def __len__(self):
    return len(self.transaction_history)  # integer
```

#### Mistake 3: Forgetting `other` parameter in `__add__`

**Wrong:**
```python
def __add__(self):         # missing `other`
    return Wallet(self.cash)
```
**Error:**
```
TypeError: __add__() takes 1 positional argument but 2 were given
```
**Correct:**
```python
def __add__(self, other):  # `other` is the right-hand operand
    return Wallet(self.cash + other.cash)
```

---

### Best Practices — Dunder Methods

- Always implement `__repr__` if you implement nothing else — it aids debugging.
- `__str__` should be human-friendly; `__repr__` should be developer-friendly and ideally show enough info to reconstruct the object.
- `__add__` should return a **new instance** of the same class, not mutate `self`.
- Only implement dunder methods when you actually need that behaviour. Don't add them "just in case."

---

### When to Use / When NOT to Use Dunder Methods

| Use When... | Avoid When... |
|------------|--------------|
| Creating custom data structures (queues, stacks, matrices) | Your class has no meaningful concept of `+`, `len`, etc. |
| You want objects to work natively with `print()`, `len()`, `+`, `[]` | Adding them would confuse other developers |
| Building libraries for others to use | It's a quick script with one-time use objects |
| Need numeric-like or collection-like behaviour for a domain object | The behaviour is complex enough to need an explicit method name |

---

## Part 2 — Property Decorator

---

### Concept Overview

**Problem:** You have a private attribute `__balance` in a class. There are two bad choices:
1. Make it public → anyone can set `account.balance = -9999999` (security hole).
2. Write `get_balance()` / `set_balance()` methods → works, but callers must remember two method names.

**Property decorators** solve this by letting you access and modify an attribute **as if it were a plain variable**, while secretly routing through getter/setter/deleter methods where you can put validation logic.

**Real-world analogy:** A bank teller window. You hand money through a small opening (the property interface). The teller checks your ID, validates the amount, and then performs the transaction internally. You don't get direct access to the vault (`__balance`).

---

### Syntax & Definition

```python
class ClassName:
    def __init__(self, value):
        self.__attribute = value   # private storage

    @property                      # Step 1: Define the GETTER
    def attribute(self):           # function name MUST match the attribute you want to expose
        return self.__attribute

    @attribute.setter              # Step 2: Define the SETTER
    def attribute(self, value):    # same function name
        self.__attribute = value

    @attribute.deleter             # Step 3: Define the DELETER (optional)
    def attribute(self):           # same function name
        del self.__attribute
```

- The **name of all three functions must be identical** and should match the logical attribute name.
- The `@property` decorator creates a *getter*. Once the getter exists, `@attribute.setter` and `@attribute.deleter` become available.
- You **must** define the getter before the setter/deleter.

---

### Example 1: Old Method vs New Method (Getter)

```python
# ── OLD METHOD ────────────────────────────────────────────
class AccountOld:
    def __init__(self, balance):
        self.__balance = balance

    def get_balance(self):        # caller must remember 'get_balance'
        return self.__balance

    def set_balance(self, value): # caller must remember 'set_balance'
        self.__balance = value

acc_old = AccountOld(1000)
print(acc_old.get_balance())     # 1000
acc_old.set_balance(2000)
print(acc_old.get_balance())     # 2000


# ── NEW METHOD (Property Decorator) ───────────────────────
class AccountNew:
    def __init__(self, balance):
        self.__balance = balance

    @property
    def balance(self):
        return self.__balance

    @balance.setter
    def balance(self, value):
        self.__balance = value

acc_new = AccountNew(1000)
print(acc_new.balance)           # 1000  ← looks like variable access
acc_new.balance = 2000           # ← looks like variable assignment
print(acc_new.balance)           # 2000
```

**Output:**
```
1000
2000
1000
2000
```

**Key difference:** With properties, the caller only needs to remember ONE name — `balance` — to get, set, and delete. It reads naturally, like accessing a plain attribute.

---

### Example 2: Setter with Validation (Real-World Banking)

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance

    @property
    def balance(self):
        print("Checking balance...")
        return self.__balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            print("Security Alert: Balance cannot be negative.")
            return                         # reject the change
        if value > 1_000_000:
            print("Large transaction requires approval.")
            return                         # reject the change
        self.__balance = value             # only set if valid

    @balance.deleter
    def balance(self):
        raise AttributeError("Cannot delete bank balance.")

# Usage
acc = BankAccount(1000)

print(acc.balance)          # 1000
acc.balance = -999          # Security Alert: Balance cannot be negative.
print(acc.balance)          # 1000  ← unchanged

acc.balance = 5_000_000     # Large transaction requires approval.
print(acc.balance)          # 1000  ← unchanged

acc.balance = 2000          # valid
print(acc.balance)          # 2000

del acc.balance             # AttributeError: Cannot delete bank balance.
```

**Output:**
```
Checking balance...
1000
Security Alert: Balance cannot be negative.
Checking balance...
1000
Large transaction requires approval.
Checking balance...
1000
Checking balance...
2000
AttributeError: Cannot delete bank balance.
```

**Why this is powerful:** The validation logic lives in ONE place. Any code that tries `acc.balance = <bad_value>` is automatically protected, whether it comes from inside the program, an API, or user input.

---

### How Property Decorator Works Internally

```
acc.balance          →  Python sees attribute access
                     →  Finds `balance` is a property object (not plain attr)
                     →  Calls balance.fget(acc)    i.e., the @property function

acc.balance = 2000   →  Python sees attribute assignment
                     →  Finds `balance` is a property object
                     →  Calls balance.fset(acc, 2000)   i.e., the @balance.setter function

del acc.balance      →  Python sees attribute deletion
                     →  Calls balance.fdel(acc)    i.e., the @balance.deleter function
```

The `property` built-in creates a **descriptor object** that intercepts attribute access. This is why the function names must all be the same — they are all registered under the same property descriptor named `balance`.

---

### Comparison: Old Method vs Property

| Feature | Old Method | Property Decorator |
|---------|-----------|-------------------|
| Reading a value | `acc.get_balance()` | `acc.balance` |
| Writing a value | `acc.set_balance(2000)` | `acc.balance = 2000` |
| Code to memorize | Two method names | One attribute name |
| Validation | Inside each method | Inside setter |
| Readability | Verbose | Pythonic, clean |
| Required? | No | No — both are valid |

---

### Common Mistakes — Property Decorators

#### Mistake 1: Defining setter without getter first

**Wrong:**
```python
@balance.setter   # NameError: name 'balance' is not defined
def balance(self, value):
    self.__balance = value
```
**Why it fails:** You must create the property (getter) first. `balance.setter` only exists after `@property` creates the `balance` property object.

**Correct:** Always define `@property` (getter) before `@balance.setter`.

#### Mistake 2: Naming the function differently from the property

**Wrong:**
```python
@property
def balance(self):
    return self.__balance

@balance.setter
def set_balance(self, value):   # ← different name: AttributeError
    self.__balance = value
```
**Correct:** All three must share the same name: `balance`.

#### Mistake 3: Using `return` in setter

**Wrong:**
```python
@balance.setter
def balance(self, value):
    return self.__balance = value   # SyntaxError
```
**Correct:** Setters do not return values. Just assign: `self.__balance = value`.

---

### Best Practices — Property Decorators

- Use `__private` (double underscore) for the actual storage variable.
- Name the property the same as the logical attribute (without underscores).
- Always implement at least a getter if you implement a setter.
- Use setters for validation — they are the right place for business rules.
- Getters/setters/deleters map to CRUD: **R**ead → getter, **U**pdate → setter, **D**elete → deleter.

---

## Part 3 — The `__name__ == "__main__"` Guard

---

### Concept Overview

When Python imports a file, it **executes every line** in that file — including any code at the top level (not inside functions or classes). This can cause unexpected behaviour: test code inside a module file runs even when you only wanted to import a function.

The `__name__` guard solves this: code inside `if __name__ == "__main__":` runs **only when the file is executed directly**, not when it is imported.

**Real-world analogy:** A worker (Python file) has instructions. When the worker is *the main driver*, they follow all instructions. When they are *called as a helper by another driver*, they only follow the parts relevant to helping — not their own personal errands.

---

### How `__name__` Works

Python automatically sets `__name__` for every file:

| Situation | Value of `__name__` in that file |
|-----------|----------------------------------|
| File is run directly (`python calculator.py`) | `"__main__"` |
| File is imported by another file | Module path (e.g., `"my_module.calculator"`) |

```
my_module/
    calculator.py      ← __name__ = "my_module.calculator"  (when imported)
                          __name__ = "__main__"              (when run directly)
main.py                ← __name__ = "__main__"              (always, when run directly)
```

---

### Syntax & Definition

```python
# calculator.py

class Calculator:
    def __init__(self):
        self.history = []

    def add(self, a, b):
        result = a + b
        self.history.append(f"{a} + {b} = {result}")
        return result

    def get_history(self):
        for record in self.history:
            print(record)

# ── Guard: test code runs ONLY when this file is executed directly ──
if __name__ == "__main__":
    calc = Calculator()
    print(calc.add(10, 20))    # 30
    print(calc.add(5, 3))      # 8
    calc.get_history()
```

---

### Example: The Problem Without the Guard

```python
# calculator.py (WITHOUT the guard)
class Calculator:
    pass

hello_world()      # ← This is a top-level statement. It executes on import!
```

```python
# main.py
from my_module import calculator    # This triggers hello_world() — UNEXPECTED!
```

**Output of `python main.py`:**
```
Hello World    ← from calculator.py, unwanted!
Hello World    ← from main.py
```

---

### Example: The Solution With the Guard

```python
# calculator.py (WITH the guard)
def hello_world():
    print("Hello World")

if __name__ == "__main__":
    hello_world()    # Only runs when YOU run calculator.py directly
```

```python
# main.py
from my_module import calculator
calculator.hello_world()   # Only ONE "Hello World" from main.py
```

**Output of `python calculator.py` (direct run):**
```
Hello World   ← runs, because __name__ == "__main__"
```

**Output of `python main.py` (indirect run):**
```
Hello World   ← only from main.py, calculator.py's guard blocks its test code
```

---

### Best Practice Pattern

```python
# any_module.py

# ── All definitions first ──
class MyClass:
    def method(self):
        pass

def my_function():
    pass

# ── Test / run code at the bottom, inside the guard ──
if __name__ == "__main__":
    obj = MyClass()
    obj.method()
    my_function()
```

> **Pro tip:** You will see `if __name__ == "__main__":` in **virtually every professional Python file**. When you see it on GitHub, it means: "this is the entry point code — the code that runs when you execute this file directly."

---

## Part 4 — Special Functions

---

### 4.1 Lambda Functions

#### Concept Overview

A **lambda** is an anonymous, one-line function. It's useful when a full `def` would be overkill — typically when passing a simple function as an argument to another function.

**Syntax:**
```python
variable_name = lambda parameters: expression
```

The expression is automatically returned — no `return` keyword needed.

#### Comparison: `def` vs `lambda`

```python
# Traditional def
def add(a, b):
    return a + b

# Lambda equivalent
add_lambda = lambda a, b: a + b

# Both work identically
print(add(3, 4))        # 7
print(add_lambda(3, 4)) # 7
```

#### Example 1: Single Parameter

```python
square = lambda x: x * x

print(square(5))    # 25
print(square(10))   # 100
```

**Output:** `25`, `100`

#### Example 2: Multiple Parameters

```python
multiply = lambda a, b, c: a * b * c

print(multiply(2, 3, 4))   # 24
```

#### Example 3: With Conditional Expression

```python
classify = lambda x: "Even" if x % 2 == 0 else "Odd"

print(classify(4))   # Even
print(classify(7))   # Odd
```

**Line-by-line breakdown:**
- `lambda x:` — declares one parameter `x`
- `"Even" if x % 2 == 0 else "Odd"` — a Python ternary expression (inline if-else), the return value

#### When to Use Lambda

| Use Lambda | Use `def` |
|-----------|-----------|
| One-line calculations | Logic spans multiple lines |
| Passing as argument to `map`, `filter`, `sorted` | Function is reused in many places |
| The function name is irrelevant | Function needs documentation |
| Quick throwaway functions | Needs error handling or complex logic |

---

### 4.2 `map()` — Apply a Function to Every Item

#### Concept Overview

`map(function, iterable)` applies a function to **every element** of an iterable and returns a map object (convert to `list` to see results).

**Real-world analogy:** A production line — every item goes through the same machine (function) and comes out transformed.

```python
# Syntax
result = list(map(function, iterable))
```

#### Example 1: Square every number in a list

```python
numbers = [1, 2, 3, 4, 5]

# Using a named function
def square(x):
    return x * x

squared = list(map(square, numbers))
print(squared)   # [1, 4, 9, 16, 25]

# Using lambda (more common)
squared_v2 = list(map(lambda x: x * x, numbers))
print(squared_v2)   # [1, 4, 9, 16, 25]
```

**Output:** `[1, 4, 9, 16, 25]`

**Line-by-line breakdown:**
- `map(square, numbers)` — for each element `n` in `numbers`, calls `square(n)`, collects results
- `list(...)` — converts the lazy map object into an actual list
- The original list is **not modified**

#### Example 2: Convert names to uppercase

```python
names = ["Alice", "Bob", "Charlie", "Diana"]

upper_names = list(map(str.upper, names))
print(upper_names)   # ['ALICE', 'BOB', 'CHARLIE', 'DIANA']
```

> `str.upper` is the unbound method — `map` will call `str.upper(name)` for each name. You provide the method **without parentheses** because you're passing it as a reference, not calling it.

#### Example 3: Map with lambda on numbers

```python
numbers = [1, 2, 3, 4, 5]

cubed = list(map(lambda x: x ** 3, numbers))
print(cubed)   # [1, 8, 27, 64, 125]
```

---

### 4.3 `filter()` — Keep Only Items Where Function Returns `True`

#### Concept Overview

`filter(function, iterable)` passes each element to `function`. If `function` returns `True`, the element is **kept**; if `False`, it is **discarded**.

**Real-world analogy:** A coffee filter — only the liquid passes through, the grounds stay behind.

```python
# Syntax
result = list(filter(function_returning_bool, iterable))
```

#### Example 1: Keep only even numbers

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

def is_even(n):
    return n % 2 == 0   # returns True or False

even_numbers = list(filter(is_even, numbers))
print(even_numbers)   # [2, 4, 6, 8, 10]

# With lambda
even_v2 = list(filter(lambda n: n % 2 == 0, numbers))
print(even_v2)   # [2, 4, 6, 8, 10]
```

**Output:** `[2, 4, 6, 8, 10]`

**How it works:**
- `filter` passes `1` to `is_even` → `False` → skip
- Passes `2` → `True` → keep
- Passes `3` → `False` → skip
- … and so on

#### Example 2: Filter students by batch (dict + zip)

```python
names = ["Alice", "Bob", "Charlie", "Diana", "Eve"]
batches = ["Batch1", "Batch2", "Batch1", "Batch2", "Batch1"]

# zip combines two lists into pairs: [("Alice","Batch1"), ("Bob","Batch2"), ...]
zipped = list(zip(names, batches))
print(zipped)
# [('Alice', 'Batch1'), ('Bob', 'Batch2'), ('Charlie', 'Batch1'),
#  ('Diana', 'Batch2'), ('Eve', 'Batch1')]

def is_batch1(student_tuple):
    name, batch = student_tuple
    return batch == "Batch1"

batch1_students = list(filter(is_batch1, zipped))
print(batch1_students)
# [('Alice', 'Batch1'), ('Charlie', 'Batch1'), ('Eve', 'Batch1')]
```

---

### 4.4 `zip()` — Combine Multiple Iterables

#### Concept Overview

`zip(iter1, iter2, ...)` pairs up elements from multiple iterables at the same index, returning tuples.

```python
names = ["Alice", "Bob", "Charlie"]
scores = [90, 85, 92]

paired = list(zip(names, scores))
print(paired)   # [('Alice', 90), ('Bob', 85), ('Charlie', 92)]

# Unpack in a loop
for name, score in zip(names, scores):
    print(f"{name}: {score}")
```

**Output:**
```
[('Alice', 90), ('Bob', 85), ('Charlie', 92)]
Alice: 90
Bob: 85
Charlie: 92
```

> `zip` stops at the **shortest** iterable. If lists have different lengths, extra elements are ignored.

---

### 4.5 `enumerate()` — Get Index AND Value Together

#### Concept Overview

When iterating over a list, you sometimes need both the **index** and the **value**. `enumerate(iterable, start=0)` provides both.

```python
# Without enumerate — manual indexing
fruits = ["apple", "banana", "cherry"]
idx = 0
for fruit in fruits:
    print(idx, fruit)
    idx += 1

# With enumerate — Pythonic
for idx, fruit in enumerate(fruits):
    print(idx, fruit)

# Start index from 1
for idx, fruit in enumerate(fruits, start=1):
    print(idx, fruit)
```

**Output (enumerate from 0):**
```
0 apple
1 banana
2 cherry
```

**Output (enumerate from 1):**
```
1 apple
2 banana
3 cherry
```

**Use case:** Process only items at even indices:

```python
data = [10, 20, 30, 40, 50]

for idx, value in enumerate(data):
    if idx % 2 == 0:
        print(f"Index {idx}: {value}")
```

**Output:**
```
Index 0: 10
Index 2: 30
Index 4: 50
```

---

## Part 5 — List Comprehension

---

### Concept Overview

**List comprehension** is a compact, single-line way to create a new list by iterating over an iterable and optionally filtering or transforming elements.

**Without comprehension (5 lines):**
```python
evens = []
for n in range(1, 11):
    if n % 2 == 0:
        evens.append(n)
```

**With comprehension (1 line):**
```python
evens = [n for n in range(1, 11) if n % 2 == 0]
```

**Real-world analogy:** An assembly line order form — "Give me all items from batch A, but only those that pass quality check."

---

### Syntax & Structure

```
[expression   for item in iterable   if condition]
 ──────────   ─────────────────────   ────────────
 What to      Loop (mandatory)        Filter (optional)
 return
```

The three parts execute in this logical order:
1. **Loop** — `for item in iterable`
2. **Filter** — `if condition` (only items where condition is `True` proceed)
3. **Expression** — what to include in the final list

> **Critical rule:** When you have only `if` (no `else`), put it **after the `for`**. When you have `if-else`, put it **before the `for`** (ternary expression style).

---

### Example 1: Simple Iteration

```python
numbers = list(range(1, 21))   # [1, 2, ..., 20]

# All numbers as-is
all_nums = [n for n in numbers]
print(all_nums)   # [1, 2, 3, ..., 20]
```

---

### Example 2: Filtering (if only)

```python
numbers = list(range(1, 21))

# Only even numbers
evens = [n for n in numbers if n % 2 == 0]
print(evens)   # [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

**Line-by-line:**
- `for n in numbers` — iterate through 1 to 20
- `if n % 2 == 0` — keep only even ones
- `n` — add the number itself to the result list

---

### Example 3: Transformation + Filter

```python
numbers = list(range(1, 21))

# Square of only even numbers
squared_evens = [n ** 2 for n in numbers if n % 2 == 0]
print(squared_evens)   # [4, 16, 36, 64, 100, 144, 196, 256, 324, 400]
```

---

### Example 4: if-else (Transformation, no filtering)

```python
numbers = list(range(1, 11))

# For every number: True if even, False if odd
# ── if-else goes BEFORE the for loop ──
result = [True if n % 2 == 0 else False for n in numbers]
print(result)
# [False, True, False, True, False, True, False, True, False, True]

# Shorter: the expression itself evaluates to bool
result_v2 = [n % 2 == 0 for n in numbers]
print(result_v2)
# [False, True, False, True, False, True, False, True, False, True]
```

**Important:** When you need **both** if and else (ternary), write it as:
```python
[value_if_true if condition else value_if_false   for item in iterable]
```
When you only need to **filter** (no else), write it as:
```python
[expression   for item in iterable   if condition]
```

---

### Example 5: Advanced — Interview-Level Challenge

**Problem:** From numbers 1 to 100, return `True` if even, `False` if odd.

```python
result = [True if n % 2 == 0 else False for n in range(1, 101)]
# or more Pythonically:
result = [n % 2 == 0 for n in range(1, 101)]

print(result[:10])   # [False, True, False, True, False, True, False, True, False, True]
```

---

### Comparison: Traditional Function vs List Comprehension

```python
# Traditional
def get_evens(numbers):
    result = []
    for n in numbers:
        if n % 2 == 0:
            result.append(n)
    return result

numbers = list(range(1, 21))
print(get_evens(numbers))   # [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# List Comprehension
print([n for n in range(1, 21) if n % 2 == 0])   # same output
```

| Feature | Traditional | List Comprehension |
|---------|------------|-------------------|
| Lines of code | 5+ | 1 |
| Readability | Verbose but clear | Concise |
| Performance | Slightly slower | Slightly faster (C-level loop) |
| Use for complex logic | ✓ | ✗ (use traditional) |
| Use for simple transforms/filters | ✗ (overkill) | ✓ |

---

## Part 6 — Dictionary Comprehension

---

### Concept Overview

**Dictionary comprehension** works exactly like list comprehension, but outputs a `dict` instead of a `list`. Use `{}` instead of `[]`, and write `key: value` as the expression.

```python
{key_expression: value_expression   for item in iterable   if condition}
```

---

### Syntax & Definition

```python
# List comprehension → [...]
squares_list = [n ** 2 for n in range(1, 6)]
print(squares_list)   # [1, 4, 9, 16, 25]

# Dictionary comprehension → {...}
squares_dict = {n: n ** 2 for n in range(1, 6)}
print(squares_dict)   # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

---

### Example 1: Number → Even/Odd

```python
result = {n: (n % 2 == 0) for n in range(1, 11)}
print(result)
# {1: False, 2: True, 3: False, 4: True, 5: False,
#   6: True, 7: False, 8: True, 9: False, 10: True}
```

**Line-by-line:**
- `n` → the key (the number itself)
- `n % 2 == 0` → the value (True if even, False if odd)
- `for n in range(1, 11)` → iterate 1 to 10

---

### Example 2: Filter a Dictionary (Batch 1 students)

```python
students = {
    "Alice": "Batch1",
    "Bob": "Batch2",
    "Charlie": "Batch1",
    "Diana": "Batch2",
    "Eve": "Batch1"
}

# Only Batch1 students — filter goes AFTER the for loop
batch1 = {name: batch for name, batch in students.items() if batch == "Batch1"}
print(batch1)
# {'Alice': 'Batch1', 'Charlie': 'Batch1', 'Eve': 'Batch1'}
```

**Line-by-line:**
- `for name, batch in students.items()` — unpack key-value pairs
- `if batch == "Batch1"` — filter condition
- `name: batch` — what to put in the output dict

---

### Example 3: Transform Values

```python
prices = {"apple": 10, "banana": 5, "cherry": 20}

# Apply 10% discount
discounted = {item: price * 0.9 for item, price in prices.items()}
print(discounted)
# {'apple': 9.0, 'banana': 4.5, 'cherry': 18.0}
```

---

### if-else in Dictionary Comprehension

```python
numbers = range(1, 11)

# Value is "even" or "odd" — ternary goes BEFORE the for
classified = {n: ("even" if n % 2 == 0 else "odd") for n in numbers}
print(classified)
# {1: 'odd', 2: 'even', 3: 'odd', 4: 'even', ...}
```

---

### Comparison Table: List vs Dictionary Comprehension

| Feature | List Comprehension | Dict Comprehension |
|---------|-------------------|-------------------|
| Syntax | `[expr for x in iter]` | `{k: v for x in iter}` |
| Output type | `list` | `dict` |
| Requires unique keys | N/A | Yes (duplicate keys overwrite) |
| Use when | Building a sequence | Building a key-value mapping |

---

## Common Mistakes — Comprehensions

### Mistake 1: Putting `if` in wrong position

**Wrong (if-only, placed before for):**
```python
result = [n if n % 2 == 0 for n in range(10)]  # SyntaxError
```
**Error:** `SyntaxError: expected 'else' after 'if' expression`

**Why:** An `if` before the `for` is a ternary expression — Python expects `else`.

**Correct (if-only, filter — put AFTER for):**
```python
result = [n for n in range(10) if n % 2 == 0]
```

**Correct (if-else, ternary — put BEFORE for):**
```python
result = [n if n % 2 == 0 else -1 for n in range(10)]
```

### Mistake 2: Nested comprehension confusion

Start simple. Nest only when you're confident with the basic syntax.

---

## Best Practices — Comprehensions

- Keep comprehensions to **one line** if possible. If it exceeds 80 characters, use a traditional loop.
- Prefer `n % 2 == 0` over `True if n % 2 == 0 else False` — the boolean expression is itself true/false.
- Use comprehensions for **transforming and filtering**. For side effects (printing, appending to external lists), use a regular loop.

---

## Interview & Exam Corner

### Frequently Asked Questions

**1. Q: What are dunder methods?**  
A: Double-underscore methods (e.g., `__init__`, `__str__`, `__add__`) that Python calls automatically in response to operators or built-in functions. Also called "magic methods" or "special methods."

**2. Q: What is the difference between `__str__` and `__repr__`?**  
A: `__str__` is for end-users (readable, friendly). `__repr__` is for developers (detailed, unambiguous). `print(obj)` calls `__str__`; typing `obj` in the REPL calls `__repr__`. If only `__repr__` is defined, `print(obj)` uses it as a fallback.

**3. Q: How does `w1 + w2` work internally?**  
A: Python calls `w1.__add__(w2)`. `self` = `w1`, `other` = `w2`. If `__add__` is not defined, Python raises `TypeError: unsupported operand type(s) for +`.

**4. Q: What is the purpose of `if __name__ == "__main__":`?**  
A: It ensures that the enclosed code only runs when the file is executed directly, not when it is imported as a module. `__name__` equals `"__main__"` only in the directly-run file.

**5. Q: What is a lambda function?**  
A: An anonymous, one-line function defined with the `lambda` keyword. `lambda x: x * 2` is equivalent to `def f(x): return x * 2`. Used when a full `def` would be overkill.

**6. Q: What is the difference between `map()` and `filter()`?**  
A: `map()` transforms every element (output list has same length as input). `filter()` selects elements based on a boolean condition (output list has ≤ input length).

**7. Q: When does `if-else` go before the `for` in a list comprehension?**  
A: Always — when you have both `if` and `else`. The ternary expression `(val_if_true if cond else val_if_false)` replaces the simple `expression` position before `for`. When you only have `if` (filtering), it goes after the `for`.

**8. Q: What is CRUD?**  
A: Create, Read, Update, Delete — the four fundamental operations on data. In Python properties: getter = Read, setter = Update/Create, deleter = Delete.

---

### Tricky Output Questions

**Q1: What does this print?**
```python
class A:
    def __add__(self, other):
        return 42

a = A()
b = A()
print(a + b)
```
**Answer:** `42`. **Why:** `a + b` calls `a.__add__(b)`, which returns `42`.

---

**Q2: What does this print?**
```python
data = [1, 2, 3, 4, 5]
result = [x * 2 if x % 2 == 0 else x for x in data]
print(result)
```
**Answer:** `[1, 4, 3, 8, 5]`. **Why:** Odd numbers are returned as-is; even numbers are doubled. The ternary is before the `for`.

---

**Q3: What does this print?**
```python
result = [x for x in range(10) if x % 3 == 0]
print(result)
```
**Answer:** `[0, 3, 6, 9]`. **Why:** Only values divisible by 3 pass the filter.

---

**Q4: What happens here?**
```python
class Account:
    def __init__(self, balance):
        self.__balance = balance

    @property
    def balance(self):
        return self.__balance

acc = Account(1000)
acc.balance = 2000
print(acc.balance)
```
**Answer:** `AttributeError: can't set attribute`. **Why:** A setter has not been defined. `@property` alone creates a read-only property.

---

**Q5: What is the output?**
```python
names = ["alice", "bob", "charlie"]
result = list(map(str.upper, names))
print(result)
```
**Answer:** `['ALICE', 'BOB', 'CHARLIE']`. **Why:** `map` applies `str.upper` to each element.

---

### One-Liner Challenges

- **Challenge:** Square all odd numbers from 1 to 20 using list comprehension.  
  **Solution:** `[n**2 for n in range(1, 21) if n % 2 != 0]`

- **Challenge:** Create a dict of number → cube for 1 to 10.  
  **Solution:** `{n: n**3 for n in range(1, 11)}`

- **Challenge:** Filter names longer than 5 characters.  
  **Solution:** `[name for name in names if len(name) > 5]`

- **Challenge:** Double every number in a list using `map` and `lambda`.  
  **Solution:** `list(map(lambda x: x * 2, numbers))`

---

## Quick-Reference Cheat Sheet

```
DUNDER METHODS CHEAT SHEET
===============================================
__init__(self, ...)        → Constructor, runs at object creation
__str__(self)              → str(obj), print(obj), {obj!s} — user-friendly
__repr__(self)             → repr(obj), raw obj in REPL — developer-friendly
__add__(self, other)       → obj1 + obj2
__sub__(self, other)       → obj1 - obj2
__eq__(self, other)        → obj1 == obj2  (return bool)
__len__(self)              → len(obj)      (return int)
__getitem__(self, index)   → obj[index]

PROPERTY DECORATOR CHEAT SHEET
===============================================
@property                  → getter (read)
@attr.setter               → setter (write/validate)
@attr.deleter              → deleter (delete)
Rule: all three must have the SAME function name
Rule: getter must be defined before setter/deleter

__name__ GUARD CHEAT SHEET
===============================================
if __name__ == "__main__": → code runs ONLY in directly-executed file
                              NOT when imported as a module
__name__ value             → "__main__" when direct, module path when imported

LAMBDA CHEAT SHEET
===============================================
lambda x: x*2             → one param, doubles it
lambda x, y: x + y        → two params, adds them
lambda x: x if x > 0 else 0  → conditional
Rule: always returns the expression, no return keyword

MAP / FILTER / ZIP / ENUMERATE CHEAT SHEET
===============================================
map(fn, iter)              → apply fn to every item, return transformed
filter(fn, iter)           → keep items where fn(item) is True
zip(iter1, iter2)          → pair up elements by index → tuples
enumerate(iter, start=0)   → gives (index, value) pairs
All return lazy objects → wrap with list() to see results

LIST / DICT COMPREHENSION CHEAT SHEET
===============================================
[expr for x in iter]                    → simple list build
[expr for x in iter if cond]            → filter (if only → after for)
[v_true if cond else v_false for x in iter]  → transform all (if-else → before for)
{k: v for x in iter}                    → dict comprehension
{k: v for x in iter if cond}            → filtered dict
```

---

## Summary (TL;DR)

- **Dunder methods** let your custom classes respond to Python's built-in operators and functions. `__str__`/`__repr__` for string representation; `__add__` for `+`; `__len__` for `len()`; `__getitem__` for `[]`; `__eq__` for `==`.
- **Property decorators** (`@property`, `@attr.setter`, `@attr.deleter`) turn methods into attribute-like accessors, enabling validation while keeping clean syntax. Getter must be defined first; all three share the same function name.
- **`if __name__ == "__main__":`** prevents test code inside a module from running when the module is imported. Essential in any reusable Python file.
- **Lambda** is a one-line anonymous function: `lambda params: expression`. Use it when passing a function to `map`, `filter`, or `sorted`.
- **`map(fn, iter)`** transforms every element; **`filter(fn, iter)`** keeps only elements where `fn` returns `True`; **`zip`** pairs iterables; **`enumerate`** gives index + value together.
- **List comprehension** `[expr for x in iter if cond]` replaces 5 lines of loop code with 1 line. The `if`-only filter goes *after* the `for`; the `if-else` ternary goes *before* the `for`.
- **Dictionary comprehension** `{k: v for x in iter if cond}` does the same for dicts.

---

## Flashcard-Style Recall (Q&A pairs)

- **Q:** What is a dunder method?  
  **A:** A method with double underscores on both sides (e.g., `__add__`) that Python calls automatically in response to operators or built-in functions.

- **Q:** How do you make `print(obj)` show custom text?  
  **A:** Implement `__str__` returning a string. If only `__repr__` is defined, `print` uses that.

- **Q:** What does `w1 + w2` call internally?  
  **A:** `w1.__add__(w2)`, where `self = w1` and `other = w2`.

- **Q:** What is the difference between `__str__` and `__repr__`?  
  **A:** `__str__` is user-friendly; `__repr__` is developer-friendly and shows technical detail. `__repr__` is used as fallback.

- **Q:** How do you enable `len(obj)` on a custom class?  
  **A:** Implement `__len__(self)` returning an integer.

- **Q:** What is the property decorator?  
  **A:** `@property` turns a method into a getter. `@attr.setter` adds a setter. `@attr.deleter` adds a deleter. All three must share the same function name.

- **Q:** What error occurs when you try to set a `@property` without a setter?  
  **A:** `AttributeError: can't set attribute`.

- **Q:** What is `__name__ == "__main__"`?  
  **A:** A guard that ensures enclosed code runs only when the file is executed directly, not when imported.

- **Q:** What does `__name__` contain in an imported file?  
  **A:** The module's path (e.g., `"my_module.calculator"`), not `"__main__"`.

- **Q:** What is a lambda function?  
  **A:** An anonymous, one-line function: `lambda params: expression`.

- **Q:** What is the difference between `map` and `filter`?  
  **A:** `map` transforms every element; `filter` discards elements where the function returns `False`.

- **Q:** In list comprehension, where does a standalone `if` go?  
  **A:** After the `for` clause: `[expr for x in iter if cond]`.

- **Q:** In list comprehension, where does `if-else` go?  
  **A:** Before the `for` clause: `[a if cond else b for x in iter]`.

- **Q:** How is dict comprehension different from list comprehension?  
  **A:** Uses `{}` instead of `[]`, and the expression is `key: value` instead of just `value`.

- **Q:** What does `enumerate()` return?  
  **A:** Pairs of `(index, value)` for each element in the iterable.

---

## Related Topics & Connections

- **Connects to:** Encapsulation (private variables `__attr`), Inheritance (dunder methods are inherited), Decorators (property is a decorator)
- **Prerequisite knowledge:** Classes & objects, `__init__`, access modifiers (public/private/protected), decorators concept from previous classes
- **What to learn next:** NumPy (uses custom array types built on similar principles), Pandas DataFrames (operator overloading and `.apply()` which uses map-like logic), context managers (`__enter__`/`__exit__` — more dunder methods), generator expressions (like list comprehension but memory-efficient)
