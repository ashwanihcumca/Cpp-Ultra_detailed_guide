# C++23 Ultra-Detailed Guide

A comprehensive, in-depth reference and tutorial for C++23. Master every feature, understand usage, migration, and best practices with real-world code.

---

## Table of Contents

1. Introduction
2. Language Features
    - Deduction Guides for "this"
    - Explicit Object Parameters
    - Multidimensional Subscript Operators
    - Standard Library Improvements to Ranges, Views, and Algorithms
    - New Standard Attributes
    - Literal Suffix Templates
    - Monadic Operations for std::optional
    - std::expected
    - constexpr std::vector
    - Other Language Changes
3. Standard Library
    - Ranges & Views
    - Containers
    - Algorithms
    - Formatting
    - Utility and Utility Classes
    - Chrono & Calendar
    - Networking (TS)
    - Other Additions
4. Best Practices & Patterns
5. Migration Notes & Pitfalls
6. Reference Table: New Syntax
7. Further Reading

---

## 1. Introduction

C++23 (ISO/IEC 14882:2023) continues the modernization of C++, bringing quality-of-life improvements, new features for functional and generic programming, and expanded standard library capabilities.

**Why learn C++23?**
- More expressive syntax and language features
- Safer and more powerful standard library
- Modern programming techniques, including monadic error handling and ranges
- Smoother migration from legacy C++ to modern idioms

---

## 2. Language Features

### 2.1 Deduction Guides for "this"

Allows more flexible deduction of template parameters for member functions.

```cpp
struct X {
    template <typename T>
    void f(this X&& self, T val) {
        // 'self' is deduced as rvalue reference
    }
};
```
- Enables better support for forwarding references and generic code.

---

### 2.2 Explicit Object Parameters

You can specify the object parameter in member functions, making extension methods and customization easier.

```cpp
struct S {
    void foo(this S& self, int x) {
        self.value += x;
    }
};
```
- Similar to Python's `self` or Rust's `self`.

---

### 2.3 Multidimensional Subscript Operators

Support for multiple arguments in `operator[]`.

```cpp
struct Matrix {
    int data[10][10];
    int& operator[](std::size_t i, std::size_t j) { return data[i][j]; }
};
Matrix m;
m[2, 3] = 42;
```
- Improves readability for custom containers.

---

### 2.4 Standard Library Improvements to Ranges, Views, and Algorithms

- **New range adaptors:** `chunk`, `stride`, `cartesian_product`, `zip`, `join_with`, etc.
- **Better interaction with existing STL algorithms:** Many now work seamlessly with ranges.
- **Range factory utilities and views:** Create, transform, filter, and compose ranges elegantly.

**Example:**
```cpp
#include <ranges>
std::vector<int> v = {1,2,3,4,5,6};
auto chunks = v | std::views::chunk(2); // [[1,2], [3,4], [5,6]]
for (auto& chunk : chunks) {
    for (int x : chunk) std::cout << x << ' ';
    std::cout << '\n';
}
```

---

### 2.5 New Standard Attributes

C++23 adds attributes for static analysis and intent:
- `[[assume(expr)]]` — Tells the compiler to assume `expr` is true (may help optimization).
- `[[nodiscard("reason")]]` — Custom message for unused return value.
- `[[deprecated("reason")]]` — Custom message for deprecated entities.

**Example:**
```cpp
[[nodiscard("Check return value!")]]
int safe_add(int a, int b) { return a + b; }
```

---

### 2.6 Literal Suffix Templates

Allows template literal suffixes for user-defined literals.

```cpp
template<char... Cs>
constexpr int operator""_bin() {
    // Converts binary string literal to int
    return /* ... */;
}
int val = 1101_bin; // e.g. 13
```
- More flexible compile-time parsing.

---

### 2.7 Monadic Operations for std::optional

C++23 adds monadic methods to `std::optional` for functional programming patterns:
- `and_then(fn)`
- `transform(fn)`
- `or_else(fn)`

**Example:**
```cpp
std::optional<int> opt = 42;
auto result = opt.and_then([](int x){ return std::optional<int>{x * 2}; }); // result = 84
auto transformed = opt.transform([](int x){ return x * 10; }); // 420
auto fallback = opt.or_else([](){ return std::optional<int>{-1}; }); // 42
```

---

### 2.8 std::expected

A new standard type for error handling and monadic programming.  
Represents either a value or an error.

```cpp
#include <expected>
std::expected<int, std::string> parse_number(const std::string& s) {
    if (s.empty()) return std::unexpected("Empty string!");
    // ...parse logic...
    return 42;
}
auto result = parse_number("123");
if (result) std::cout << *result;
else std::cout << result.error();
```
- Use instead of `std::optional` for functions that might fail but should return an error.

---

### 2.9 constexpr std::vector

`std::vector` can be used in constant expressions, allowing compile-time dynamic arrays.

```cpp
constexpr std::vector<int> make_vec() {
    std::vector<int> v;
    v.push_back(1);
    v.push_back(2);
    return v;
}
static_assert(make_vec().size() == 2);
```

---

### 2.10 Other Language Changes

- **Improved support for UTF-8 string literals:** `u8"Hello"`
- **Standardized `std::move_only_function`:** A move-only function wrapper for callbacks and task systems.
- **Expanded support for `constexpr` in the standard library.**
- **Better type deduction in templates and lambdas.**
- **Expanded default member initializers.**

---

## 3. Standard Library

### Ranges & Views

- **New views:** `chunk`, `stride`, `cartesian_product`, `zip`, `join_with`, etc.
- **Improved adaptors:** More functional composition, lazy evaluation.

### Containers

- **constexpr containers:** Most STL containers now work in constant expressions.
- **Improved deduction guides and value initialization.**

### Algorithms

- **Expanded algorithms:** Most algorithms now accept ranges.
- **`starts_with`, `ends_with`** for strings.
- **Monadic methods for optional and expected.**

### Formatting

- Expanded support for `std::format`.
- Improved interoperability with chrono and ranges.

### Utility and Utility Classes

- `std::expected`, `std::move_only_function`, improved `std::optional`.

### Chrono & Calendar

- **Better date/time handling**, more utilities for time zones, calendars, and formatting.

### Networking (TS)

- Networking library is progressing towards standardization.
- Provides async sockets, HTTP, and more (implementation-dependent).

### Other Additions

- **Expanded standard attributes.**
- **Improved support for coroutines and concepts.**
- **Better support for Unicode and text processing.**

---

## 4. Best Practices & Patterns

### Monadic Error Handling with std::expected

```cpp
std::expected<int, std::string> safe_divide(int a, int b) {
    if (b == 0) return std::unexpected("Divide by zero!");
    return a / b;
}
auto result = safe_divide(10, 0);
if (result) std::cout << *result;
else std::cout << result.error();
```

### Functional Pipelines with Ranges

```cpp
std::vector<int> v = {1,2,3,4,5,6};
auto squares = v | std::views::transform([](int x){ return x*x; })
                 | std::views::filter([](int x){ return x % 2 == 0; });
for (int x : squares) std::cout << x << ' ';
```

### Multidimensional Subscript

```cpp
struct Tensor {
    int data[4][4][4];
    int& operator[](size_t i, size_t j, size_t k) { return data[i][j][k]; }
};
Tensor t;
t[1,2,3] = 99;
```

### Literal Suffix Templates

```cpp
template<char... Cs>
constexpr int operator""_bin() { /* ... */ }
auto val = 1101_bin; // Compile-time binary literal
```

---

## 5. Migration Notes & Pitfalls

- **Ranges:** Prefer views and adaptors for performance and composability.
- **std::expected:** Use for functions that may fail, especially where error details are valuable.
- **Monadic optionals:** Chain operations functionally instead of manual if-checks.
- **Multidimensional subscript:** Adjust custom containers to support new syntax.
- **UTF-8 literals:** Use for cross-platform text, but check compiler support.
- **constexpr containers:** Enable compile-time validation and static data.

---

## 6. Reference Table: New Syntax

| Feature                   | Syntax Example                                 | Notes                       |
|---------------------------|------------------------------------------------|-----------------------------|
| Deduction for "this"      | `void f(this X&& self, T val)`                 | Explicit object param       |
| Explicit Object Param     | `void foo(this S& self, int x)`                | Python-like 'self'          |
| Multidimensional Subscript| `m[i, j]`                                      | Multiple indices            |
| Ranges/Views              | `v | views::chunk(2)`                          | Functional pipeline         |
| Literal Suffix Template   | `1101_bin`                                     | Compile-time literals       |
| Monadic Optionals         | `opt.and_then(fn)`                             | Functional chaining         |
| std::expected             | `std::expected<int, std::string>`              | Value or error              |
| constexpr vector          | `constexpr std::vector<int> v = ...`           | Compile-time container      |
| New attributes            | `[[assume(expr)]]`                             | Static analysis/optimization|
| UTF-8 literals            | `u8"string"`                                   | Unicode support             |
| move_only_function        | `std::move_only_function<void()>`              | Move-only callback          |

---

## 7. Further Reading

- [cppreference.com - C++23](https://en.cppreference.com/w/cpp/23)
- [ISO C++ Official Site](https://isocpp.org/)
- [C++23 Features - GeeksforGeeks](https://www.geeksforgeeks.org/whats-new-in-c-23/)
- [C++23 Range Adaptors (Fluent C++)](https://www.fluentcpp.com/2023/02/10/c23-range-adaptors/)
- [C++23 Standardization Reports (Reddit r/cpp)](https://www.reddit.com/r/cpp/)
- [Papers and Proposals for C++23 (WG21)](https://isocpp.org/std/status)

---