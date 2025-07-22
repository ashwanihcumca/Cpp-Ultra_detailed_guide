# C++20 Ultra-Detailed Guide

A comprehensive, in-depth reference and tutorial for C++20. Master every feature, understand usage, migration, and best practices with real-world code.

---

## Table of Contents

1. Introduction
2. Language Features
    - Concepts
    - Ranges Library
    - Coroutines
    - Modules
    - Three-Way Comparison (Spaceship Operator)
    - Expanded constexpr
    - Calendar and Time Zones
    - Designated Initializers
    - Class Types in Non-type Template Parameters
    - Immediate Functions (consteval)
    - Lambdas in Unevaluated Contexts
    - Improved Default Initializations
    - std::span
    - std::format
    - Other Features
3. Standard Library
    - Containers
    - Utilities
    - Algorithms
    - Ranges
    - Concurrency & Coroutines
    - Calendar & Time Zones
4. Best Practices & Patterns
5. Migration Notes & Pitfalls
6. Reference Table: New Syntax
7. Further Reading

---

## 1. Introduction

C++20 is a massive update, bringing generic programming, modern libraries, and advanced language features.
**Why learn C++20?**
- Concepts for robust templates
- Ranges and views for composable data handling
- Coroutines for async programming
- Modules for scalable, fast builds
- Safer, more expressive syntax

---

## 2. Language Features

### 2.1 Concepts

Type constraints for templates, enabling safer and clearer generic code.

```cpp
template <typename T>
concept Incrementable = requires(T x) { x++; };

template <Incrementable T>
T increment(T x) { return x + 1; }
```

**Standard Concepts**: `std::integral`, `std::floating_point`, `std::same_as<T, U>`, `std::ranges::range`, etc.

**Best practices:**  
- Use concepts for template APIs, error messages, and intent.

---

### 2.2 Ranges Library

Compositional, lazy, functional-style data pipelines.

```cpp
#include <ranges>
std::vector<int> v = {1, 2, 3, 4, 5};
auto even = v | std::ranges::views::filter([](int n){ return n%2==0; });
for (int n : even) std::cout << n << " "; // 2 4
```

**Views:** `filter`, `transform`, `take`, `drop`, `reverse`, etc.

**Best practices:**  
- Use views to avoid data copies and for pipeline composition.

---

### 2.3 Coroutines

Efficient async/await code.

```cpp
#include <coroutine>
#include <iostream>

struct Generator {
    struct promise_type {
        int value;
        Generator get_return_object() { return Generator{this}; }
        std::suspend_always initial_suspend() { return {}; }
        std::suspend_always final_suspend() noexcept { return {}; }
        void return_void() {}
        std::suspend_always yield_value(int v) { value = v; return {}; }
        void unhandled_exception() { std::terminate(); }
    };
    promise_type* promise;
    explicit Generator(promise_type* p): promise(p) {}
    bool next() { /* simplified for demo */ return false; }
    int value() { return promise->value; }
};

Generator gen() {
    co_yield 1;
    co_yield 2;
    co_yield 3;
}
```

**Real-world:**  
- Async I/O, lazy generators, event-driven code

---

### 2.4 Modules

Replace header files, speed up builds, and improve encapsulation.

```cpp
// math.ixx
export module math;
export int add(int a, int b) { return a + b; }

// main.cpp
import math;
std::cout << add(1, 2);
```

- Separate compilation units, cleaner APIs

---

### 2.5 Three-Way Comparison (Spaceship Operator `<=>`)

Automatic generation of all comparison operators.

```cpp
#include <compare>
struct Point {
    int x, y;
    auto operator<=>(const Point&) const = default;
};
Point a{1,2}, b{2,2};
bool less = (a < b); // uses operator<=>
```

**Best practices:**  
- Use for sortable types

---

### 2.6 Expanded constexpr

Most STL algorithms and containers now usable in constexpr.

```cpp
constexpr std::array<int, 4> arr = {1,2,3,4};
constexpr int sum = std::accumulate(arr.begin(), arr.end(), 0);
```

---

### 2.7 Calendar and Time Zones

Precise date/time handling.

```cpp
#include <chrono>
using namespace std::chrono;
auto today = year_month_day{floor<days>(system_clock::now())};
```

---

### 2.8 Designated Initializers

Explicit field initialization.

```cpp
struct Data { int a; double b; };
Data d = {.a = 1, .b = 2.5};
```

---

### 2.9 Class Types in Non-type Template Parameters

Class types with constexpr constructors allowed as template parameters.

```cpp
struct Foo { int x; constexpr Foo(int y) : x(y) {} };
template <Foo f>
void func() { std::cout << f.x; }
func<Foo{5}>();
```

---

### 2.10 Immediate Functions (`consteval`)

Guaranteed compile-time evaluation.

```cpp
consteval int get_five() { return 5; }
int arr[get_five()]; // Must be evaluated at compile-time
```

---

### 2.11 Lambdas in Unevaluated Contexts

Lambdas usable in `decltype`, templates, and SFINAE.

---

### 2.12 Improved Default Initializations

Aggregates and classes are more reliably initialized to zero/empty.

---

### 2.13 std::span

Views over contiguous memory regions.

```cpp
#include <span>
int arr[] = {1,2,3,4};
std::span<int> s(arr);
for (int n : s) std::cout << n << ' ';
```

---

### 2.14 std::format

Safe, fast, printf-style string formatting.

```cpp
#include <format>
std::string s = std::format("Hello {}!", "world");
std::cout << s;
```

---

### 2.15 Other Features

- `std::bit_cast` for type-punning
- `std::atomic_ref` for atomic access to existing objects
- `constinit` for compile-time initialization
- Expanded attributes
- Improved lambdas and template syntax

---

## 3. Standard Library

### Containers

- Expanded `constexpr` support
- Deduction guides

### Utilities

- `std::span`, `std::format`, `std::bit_cast`, `std::atomic_ref`
- Improved smart pointers, tuples, and function objects

### Algorithms

- Ranges-enabled algorithms
- More algorithms are constexpr

### Ranges

- Composable data pipelines with lazy evaluation

### Concurrency & Coroutines

- Improved threading, async, and coroutine support

### Calendar & Time Zones

- Precise, portable date/time APIs

---

## 4. Best Practices & Patterns

### Concepts for API Design

```cpp
template<typename T>
concept Sortable = requires(T a, T b) { a < b; };

template<Sortable T>
void sort(std::vector<T>& v) { /* ... */ }
```

### Ranges for Data Processing

```cpp
std::vector<int> nums = {1,2,3,4,5,6};
auto odds = nums | std::ranges::views::filter([](int n){return n%2;});
for (int n : odds) std::cout << n << " "; // 1 3 5
```

### Coroutines for Lazy Generation

```cpp
#include <generator> // experimental
auto gen = []() -> std::generator<int> {
    for (int i = 0; i < 5; ++i) co_yield i;
};
for (int n : gen()) std::cout << n << ' ';
```

### Modules for Clean APIs

```cpp
// math.ixx
export module math;
export int add(int a, int b) { return a + b; }

// main.cpp
import math;
std::cout << add(1, 2);
```

### std::span for Buffer Handling

```cpp
void process(std::span<const char> buf) { /* ... */ }
```

### std::format for Logging

```cpp
std::string s = std::format("[{}] {}", code, message);
```

---

## 5. Migration Notes & Pitfalls

- **Concepts:** Use for safe template APIs; error messages are easier to understand.
- **Ranges:** Avoid unintentional data copies; views are lazy.
- **Coroutines:** Syntax is new; toolchain support may vary.
- **Modules:** Not all compilers fully support modules yet.
- **std::span:** Beware of underlying buffer lifetimes.
- **Immediate functions:** Use for compile-time invariants.

---

## 6. Reference Table: New Syntax

| Feature         | Syntax Example                                  | Notes                    |
|-----------------|-------------------------------------------------|--------------------------|
| Concepts        | `template<Sortable T> void f(T);`               | Constraints on templates |
| Ranges          | `v | views::filter(f)`                          | Lazy, composable         |
| Coroutines      | `co_yield value;`                               | Async/generator code     |
| Modules         | `export module math; import math;`              | Fast, clean APIs         |
| Spaceship Op    | `auto operator<=>(const T&) const = default;`   | All comparisons auto     |
| Expanded constexpr | `constexpr int f(int);`                      | More STL constexpr       |
| Calendar        | `year_month_day{...}`                           | Date/time support        |
| Designated Init | `Data d{.a=1, .b=2.5};`                         | Explicit fields          |
| Non-type TPP    | `template<Foo f> ...`                           | Class types as params    |
| consteval       | `consteval int f();`                            | Compile-time only        |
| std::span       | `std::span<int> s(arr);`                        | View over memory         |
| std::format     | `std::format("{}", val);`                       | Safe formatting          |

---

## 7. Further Reading

- [cppreference.com - C++20](https://en.cppreference.com/w/cpp/20)
- [ISO C++ Official Site](https://isocpp.org/)
- [C++20 Features - GeeksforGeeks](https://www.geeksforgeeks.org/c-20-new-features/)
- [C++20 Ranges and Coroutines (Fluent C++)](https://www.fluentcpp.com/2020/01/10/cpp20-ranges/)
- [Breaking Down C++20 (JetBrains)](https://blog.jetbrains.com/clion/2020/09/cpp20-breaking-down-the-new-standard/)

---