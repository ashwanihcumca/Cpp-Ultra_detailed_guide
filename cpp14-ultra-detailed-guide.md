# C++14 Ultra-Detailed Guide

A comprehensive, in-depth reference and tutorial for C++14. Master every feature, understand usage, migration, and best practices with real-world code.

---

## Table of Contents

1. Introduction
2. Language Features
    - Generic Lambdas
    - Lambda Capture Initializers (init-capture)
    - auto Return Type Deduction
    - Variable Templates
    - Relaxed constexpr
    - std::make_unique
    - Deprecated Features
    - Binary Literals & Digit Separators
    - Miscellaneous Improvements
3. Standard Library
    - Container Improvements
    - Utility Improvements
    - Algorithm Updates
4. Best Practices & Patterns
5. Migration Notes & Pitfalls
6. Reference Table: New Syntax
7. Further Reading

---

## 1. Introduction

C++14 is a "bugfix" and enhancement release to C++11, making modern C++ easier and less verbose.  
- **Why learn C++14?**
  - Cleaner syntax for lambdas and templates
  - More expressive metaprogramming
  - Improved constexpr and type deduction
  - Minor but important STL and language improvements

---

## 2. Language Features

### 2.1 Generic Lambdas

**Lambda expressions can deduce parameter types using `auto`.**

```cpp
auto add = [](auto a, auto b) { return a + b; };
std::cout << add(3, 4) << '\n';      // 7
std::cout << add(3.1, 4.2) << '\n';  // 7.3
```

- Enables functional-style programming and reusable code.
- Can use `auto&`, `const auto&` for references.

**Pitfalls:**  
- Type deduction may cause ambiguous behavior if types don't match.

---

### 2.2 Lambda Capture Initializers (Init-Capture)

**Capture by value with custom initializers.**

```cpp
int x = 10;
auto lam = [y = x + 5]() { std::cout << y << std::endl; };
lam(); // Outputs 15
```

- Useful for capturing moved objects.
```cpp
std::unique_ptr<int> p(new int(42));
auto lam = [q = std::move(p)]() { std::cout << *q; }; // p is moved
```

**Best practices:**  
- Use for resource transfer into lambdas.

---

### 2.3 auto Return Type Deduction

**Functions can deduce return type automatically.**

```cpp
auto make_int() { return 42; }
auto multiply(int a, int b) { return a * b; }
```

- No need for trailing return type unless needed for complex expressions.
- For multiple return types (e.g., if/else), types must match.

**Pitfall:**  
- Ambiguity if return types differ in branches.

---

### 2.4 Variable Templates

**Templates for variables, not just types or functions.**

```cpp
template<typename T>
constexpr T pi = T(3.1415926535897932385);

double d = pi<double>;
float f = pi<float>;
```

- Useful for mathematical and configuration constants.

---

### 2.5 Relaxed constexpr

**More expressive compile-time functions.**

- Loops, branches (`if`, `switch`), and local variables now allowed.

```cpp
constexpr int factorial(int n) {
    int result = 1;
    for (int i = 2; i <= n; ++i)
        result *= i;
    return result;
}
static_assert(factorial(5) == 120, "Factorial failed");
```

- Enables complex metaprogramming and compile-time computation.

---

### 2.6 std::make_unique

**Safe, exception-friendly unique_ptr creation.**

```cpp
auto ptr = std::make_unique<int>(10);
auto arr = std::make_unique<int[]>(5);
```

- Prefer `make_unique` over `new` for memory safety.

**Pitfall:**  
- No direct support for custom deleters; use explicit `unique_ptr` when needed.

---

### 2.7 Deprecated Features

- `std::random_shuffle` is deprecated; use `std::shuffle` with a random engine.
```cpp
#include <algorithm>
#include <random>
std::vector<int> v = {1,2,3,4};
std::shuffle(v.begin(), v.end(), std::mt19937{std::random_device{}()});
```

- Several other minor removals and changes to improve type safety.

---

### 2.8 Binary Literals & Digit Separators

**Binary literals**: Prefix with `0b`
```cpp
int bin = 0b101010; // 42
```

**Digit separators**: Use single quotes `'` to separate digits for readability
```cpp
int million = 1'000'000;
int credit = 0b1010'1111'0010;
```

**Best practices:**  
- Use for large values, bitfields, and clarity in code.

---

### 2.9 Miscellaneous Improvements

- `std::shared_timed_mutex` for shared/exclusive locking.
- Improved type deduction in `std::bind`.
- `std::quoted` for quoted strings in streams:
```cpp
#include <iomanip>
std::string s = "Hello world";
std::cout << std::quoted(s); // Outputs: "Hello world"
```

---

## 3. Standard Library

### Container Improvements

- Deduction guides for containers, e.g., `std::vector<int> v = {1,2,3};`
- More flexible construction and assignment.

### Utility Improvements

- `std::exchange` for value swapping:
```cpp
int a = 5, b = 10;
a = std::exchange(b, a); // b = 5, a = 10
```

- `std::make_unique` added.
- Improvements to `std::tuple`, `std::array`.

### Algorithm Updates

- Improved performance and type deduction.
- Support for user-defined literals.

---

## 4. Best Practices & Patterns

### Generic Lambda for STL

```cpp
std::vector<int> v = {1,2,3,4};
std::for_each(v.begin(), v.end(), [](auto x) { std::cout << x << " "; });
```

### Lambda Capture Initializer for Resource Transfer

```cpp
std::unique_ptr<int> uptr(new int(7));
auto lam = [p = std::move(uptr)]() { std::cout << *p; };
```

### Relaxed constexpr for Metaprogramming

```cpp
constexpr int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}
static_assert(gcd(48, 18) == 6, "GCD failed");
```

---

## 5. Migration Notes & Pitfalls

- **Generic Lambdas:** Type deduction may surprise you—ensure parameter types are compatible.
- **auto Return:** Use consistently, but avoid ambiguity.
- **std::make_unique:** Prefer over manual `new`; safer and exception-friendly.
- **Binary Literals:** Use for hardware, bitmask, and protocol code.
- **Relaxed constexpr:** Use for compile-time checks and calculations.

---

## 6. Reference Table: New Syntax

| Feature                | Syntax Example                                   | Notes                  |
|------------------------|--------------------------------------------------|------------------------|
| Generic Lambda         | `[](auto x){ return x*2; }`                      | Parameter deduced      |
| Lambda Init-Capture    | `[x = foo()](){ ... }`                           | Custom initializer     |
| auto Return            | `auto f(){ return 42; }`                         | No trailing type needed|
| Variable Template      | `template<typename T> T val = ...;`              | Compile-time vars      |
| Binary Literal         | `int b = 0b1010;`                                | Binary value           |
| Digit Separator        | `int n = 1'000'000;`                             | Readable values        |
| Relaxed constexpr      | `constexpr int f(int x){ ... }`                  | Loops, branches allowed|
| std::make_unique       | `auto p = std::make_unique<T>(...);`             | Memory safety          |

---

## 7. Further Reading

- [cppreference.com - C++14](https://en.cppreference.com/w/cpp/14)
- [ISO C++ Official Site](https://isocpp.org/)
- [C++14 Features - GeeksforGeeks](https://www.geeksforgeeks.org/whats-new-in-c-14/)
- [Effective Modern C++ - Scott Meyers](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/)
- [C++17 and C++14 New Features (Herb Sutter)](https://herbsutter.com/gotw/)

---

**Ready for ultra-detailed guides for C++17 and C++20? Just say yes!**