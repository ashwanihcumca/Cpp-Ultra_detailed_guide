# C++11 Ultra-Detailed Guide

A comprehensive, in-depth reference and tutorial for C++11. Master every feature, understand usage, migration, and best practices with real-world code.

---

## Table of Contents

1. Introduction
2. Language Features
    - Auto Type Deduction
    - Range-Based For Loop
    - nullptr
    - Uniform Initialization
    - Smart Pointers
    - Move Semantics & Rvalue References
    - Lambda Expressions
    - constexpr
    - Static Assertions
    - Strongly Typed Enums
    - Delegating Constructors
    - Inheriting Constructors
    - override & final
    - default & delete
    - Extended Template Features
    - Variadic Templates
    - Thread Support & Concurrency
    - Other Features
3. Standard Library
    - Containers
    - Utilities
    - Algorithms
    - Chrono & Regex
4. Best Practices & Patterns
5. Migration Notes & Pitfalls
6. Reference Table: New Syntax
7. Further Reading

---

## 1. Introduction

C++11 (“C++0x”) marks a major leap in power, safety, and expressiveness. It modernizes C++ for today’s software world, introducing features found in Java, Python, and functional languages.

- **Why learn C++11?**  
  - Write safer, more efficient code
  - Reduce boilerplate, improve maintainability
  - Use advanced libraries (most require C++11+)
  - Unlock concurrency, smart pointers, lambdas, and more

---

## 2. Language Features

### 2.1 Auto Type Deduction

`auto` lets the compiler infer the type from the initializer.  
**Use for iterators, STL, and when type is obvious.**

```cpp
auto i = 42;          // int
auto d = 3.14;        // double
auto *p = &i;         // int*
const auto& ref = someFunc(); // reference to return type
```

**With STL:**
```cpp
std::vector<std::string> v;
for (auto it = v.begin(); it != v.end(); ++it) { /* ... */ }
```

**Pitfalls:**
- Don’t use `auto` if type ambiguity makes code hard to read.
- `auto x = {1, 2, 3};` // x is std::initializer_list<int>

---

### 2.2 Range-Based For Loop

Easy iteration over containers and arrays.

```cpp
std::vector<int> v = {1, 2, 3};
for (auto n : v) std::cout << n << " ";
```
- With reference:
```cpp
for (auto& n : v) n *= 2;
```
- With const reference:
```cpp
for (const auto& n : v) std::cout << n << " ";
```

**Best practices:**  
Use reference for large or non-trivial types.

---

### 2.3 nullptr

Type-safe null pointer constant.  
**Always use `nullptr` instead of `NULL` or `0`.**

```cpp
int* p = nullptr;
void foo(int* p) { /* ... */ }
foo(nullptr);
```

**Pitfall:**  
Using `NULL` (which is 0) can lead to overload ambiguity.

---

### 2.4 Uniform Initialization

Consistent, brace-based initialization for all types.

```cpp
int a{5};
double d{3.14};
std::vector<int> v{1,2,3};
struct S { int x; double y; };
S s{1, 2.5};
```
**No narrowing:**
```cpp
int x{2.5}; // Error: narrowing
int x(2.5); // OK: x==2
```

**Initializer lists:**
```cpp
std::vector<int> v = {1, 2, 3};
```

---

### 2.5 Smart Pointers

**Automatic, exception-safe memory management.**  
Use instead of raw pointers for ownership.

#### unique_ptr (exclusive ownership)

```cpp
std::unique_ptr<int> p = std::make_unique<int>(42);
// Move only, cannot copy
std::unique_ptr<int> p2 = std::move(p);
```

#### shared_ptr (shared ownership)

```cpp
auto p = std::make_shared<int>(99);
auto q = p; // reference count increases
```

#### weak_ptr (non-owning)

```cpp
std::weak_ptr<int> wp = p;
if (auto sp = wp.lock()) {
    // sp is a shared_ptr
}
```

**Best practices:**  
- Prefer `unique_ptr` unless you need shared ownership.
- Use `weak_ptr` to break cycles (e.g., graphs, trees).

---

### 2.6 Move Semantics & Rvalue References

**Efficient resource transfer, no deep copies.**

#### std::move

```cpp
std::string s1 = "abc";
std::string s2 = std::move(s1); // s1 is now empty
```

#### Move Constructor/Assignment

```cpp
class Foo {
    std::vector<int> data;
public:
    Foo(std::vector<int>&& d) : data(std::move(d)) {}
    Foo& operator=(Foo&& other) {
        data = std::move(other.data);
        return *this;
    }
};
```

#### Rvalue Reference

```cpp
void process(std::vector<int>&& v) {
    // v is a temporary
}
process(std::vector<int>{1,2,3});
```

**Best practices:**  
- Always move in move constructors/assignments.
- Mark move operations `noexcept` if possible.

---

### 2.7 Lambda Expressions

**Anonymous inline functions.**  
Useful with STL algorithms, callbacks, and concise code.

```cpp
auto add = [](int a, int b) { return a + b; };
std::cout << add(2, 3); // 5
```

#### Capturing variables

```cpp
int x = 5;
auto f = [x](int y) { return x + y; }; // capture by value
auto g = [&x](int y) { x += y; return x; }; // capture by reference
```

#### Mutable lambdas

```cpp
int x = 1;
auto inc = [x]() mutable { return ++x; }; // local x, not outer
```

#### With STL

```cpp
std::vector<int> v = {1,2,3,4};
std::for_each(v.begin(), v.end(), [](int& n){ n *= 2; });
```

**Pitfalls:**  
Beware of reference captures—dangling references if lambda outlives scope.

---

### 2.8 constexpr

**Compile-time constant expressions.**

```cpp
constexpr int square(int x) { return x * x; }
int arr[square(4)]; // arr[16]
```

- Functions, variables, and constructors can be `constexpr`.
- Use for performance, configuration, or hardware-related code.

---

### 2.9 Static Assertions

**Compile-time checks for types, values, and invariants.**

```cpp
static_assert(sizeof(int) == 4, "int must be 4 bytes");
static_assert(std::is_same<T, double>::value, "T must be double");
```

---

### 2.10 Strongly Typed Enums

**Type-safe, scoped enums.**

```cpp
enum class Color { Red, Green, Blue };
Color c = Color::Green;
```
- No implicit conversion to int.
- Must use scope: `Color::Red`

---

### 2.11 Delegating Constructors

**One constructor calls another.**

```cpp
class MyClass {
public:
    MyClass() : MyClass(0) {}
    MyClass(int x) { /* ... */ }
};
```

---

### 2.12 Inheriting Constructors

**Subclass inherits all parent constructors.**

```cpp
struct Base { Base(int) {} };
struct Derived : Base { using Base::Base; };
```

---

### 2.13 override & final

**Safer virtual overrides.**

```cpp
class Base { virtual void foo(); };
class Derived : public Base {
    void foo() override; // must match Base
    void bar() final;    // cannot override bar further
};
```

---

### 2.14 default & delete

**Explicitly default/delete special functions.**

```cpp
class A {
    A() = default;            // default constructor
    A(const A&) = delete;     // cannot copy
};
```

---

### 2.15 Extended Template Features

#### Template Aliases

```cpp
template<typename T>
using Vec = std::vector<T>;
Vec<int> vi;
```

#### Variadic Templates

**Templates with any number of arguments.**

```cpp
template<typename... Args>
void printAll(Args... args) {
    (std::cout << ... << args) << std::endl;
}
printAll(1, " ", 2.5, '\n');
```

#### Template Improvements

- Default template arguments for functions
- `decltype`, `std::result_of`
- SFINAE enhancements

---

### 2.16 Thread Support & Concurrency

**Portable, standard multithreading.**

```cpp
#include <thread>
void func(int x) { /* ... */ }
std::thread t(func, 10);
t.join();
```

#### Mutex & Lock

```cpp
#include <mutex>
std::mutex mtx;
mtx.lock(); /* ... */ mtx.unlock();
std::lock_guard<std::mutex> lock(mtx); // RAII lock
```

#### Condition Variable

```cpp
#include <condition_variable>
std::condition_variable cv;
cv.notify_one();
cv.wait(lock);
```

#### Atomic Types

```cpp
#include <atomic>
std::atomic<int> counter{0};
counter++;
```

**Best practices:**  
- Always join or detach threads.
- Use locks, atomics for shared data.

---

### 2.17 Other Features

- Right angle brackets: `std::vector<std::pair<int, int>> v;`
- Extended initializer lists
- `std::array` (fixed-size array)
- `std::tuple` (heterogeneous containers)
- `std::chrono` (timers, durations)
- `std::regex` (regular expressions)
- New algorithms (`std::all_of`, `std::any_of`, `std::none_of`)
- `alignas`, `alignof`

---

## 3. Standard Library

### Containers

- `std::array`
- `std::forward_list`
- Improved `std::vector`, `std::map`, etc.

### Utilities

- `std::tuple`
- `std::function`
- `std::bind`
- `std::chrono`
- `std::thread`, `std::mutex`, `std::atomic`
- `std::regex`

### Algorithms

- Range-based algorithms
- Move-aware algorithms
- New algorithms (`std::all_of`, `std::any_of`, etc.)

---

## 4. Best Practices & Patterns

### RAII with Smart Pointers

```cpp
class File {
    std::unique_ptr<std::FILE, decltype(&std::fclose)> fp;
public:
    File(const char* name)
        : fp(std::fopen(name, "r"), &std::fclose) {}
};
```

### Lambdas for STL Algorithms

```cpp
std::vector<std::string> names = {"Ann", "Bob", "Cat"};
std::sort(names.begin(), names.end(), [](const std::string& a, const std::string& b){
    return a.size() < b.size();
});
```

### Multithreaded Producer/Consumer

```cpp
#include <thread>
#include <queue>
#include <mutex>
#include <condition_variable>
std::queue<int> q;
std::mutex mtx;
std::condition_variable cv;
bool done = false;

void producer() {
    for (int i=0; i<10; ++i) {
        std::lock_guard<std::mutex> lock(mtx);
        q.push(i);
        cv.notify_one();
    }
    done = true;
    cv.notify_all();
}
void consumer() {
    while (!done || !q.empty()) {
        std::unique_lock<std::mutex> lock(mtx);
        cv.wait(lock, []{ return !q.empty() || done; });
        while (!q.empty()) {
            std::cout << q.front() << std::endl;
            q.pop();
        }
    }
}
```

---

## 5. Migration Notes & Pitfalls

- **Prefer smart pointers:** avoid raw ownership.
- **Use `auto` for iterators, STL, but not everywhere.**
- **Lambdas:** be careful with `[&]` (capture-all)—may cause bugs.
- **nullptr:** always use for pointers.
- **enum class**: prefer for type safety.
- **Threads:** always `join()` or `detach`.
- **Move semantics:** use `std::move` in resource transfer; mark move constructors/assignments as `noexcept` if possible.

---

## 6. Reference Table: New Syntax

| Feature           | Syntax Example                                 | Notes                 |
|-------------------|------------------------------------------------|-----------------------|
| auto              | `auto x = 3.14;`                               | Type deduced          |
| Range-for         | `for (auto n : v)`                             | Container iteration   |
| nullptr           | `int* p = nullptr;`                            | Null pointer          |
| enum class        | `enum class Color {Red,Green};`                | Scoped, type-safe     |
| unique_ptr        | `std::unique_ptr<T> p = std::make_unique<T>()` | Exclusive ownership   |
| Lambda            | `auto f = [](int x){return x*2;};`             | Inline function       |
| constexpr         | `constexpr int s(int x){return x*x;}`          | Compile-time const    |
| static_assert     | `static_assert(sizeof(int)==4,"Bad int");`     | Compile-time check    |
| Variadic Template | `template<typename... Ts> void f(Ts... ts)`    | Flexible arguments    |

---

## 7. Further Reading

- [cppreference.com - C++11](https://en.cppreference.com/w/cpp/11)
- [ISO C++ Official Site](https://isocpp.org/)
- [C++11 FAQ - Bjarne Stroustrup](https://www.stroustrup.com/C++11FAQ.html)
- [Effective Modern C++ - Scott Meyers (book)](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/)
- [C++11 at a glance (Herb Sutter)](https://herbsutter.com/gotw/)

---

**Want this depth for C++14, C++17, and C++20? Just say yes!**