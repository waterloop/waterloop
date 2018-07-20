[wio]: https://github.com/wio/wio

# Git Style Guide.

# Compilers

All reasonable warning levels should be enabled. Some warning levels, such as GCC's `-Weffc++` warning mode can be too noisy and will not be recommended for normal compilation.

### GCC / Clang

A good combination of settings is `-Wall -Wextra -Wshadow -Wnon-virtual-dtor -pedantic`

 * `-Wall -Wextra`: reasonable and standard
 * `-Wshadow`: warn the user if a variable declaration shadows another with the same name in the same scope
 * `-Wnon-virtual-dtor`: warn the user if a class with virtual functions has a non-virtual destructor. This can lead to hard to track down memory errors
 * `-pedantic`: warn about non-portable code, C++ that uses language extensions.

### MSVC

MSVC has fewer warning options, so all warnings should be enabled: `/W4`. `/Wall` could be considered, but does not seem to be recommended even by microsoft.

## Ignoring Warnings.

If it is determined that the compiler or analyzer is warning on something that is either incorrect or unavoidable, then only if the team agrees it is okay to disable the specific error to as localized part of the code as possible.

## Static Analyzers.

Static analyzers look for errors that compilers do not look for, such as potential performance and memory issues. Maybe try clang tidy.

### Cppcheck.

[Cppcheck](http://cppcheck.sourceforge.net/) is free and opensource. It strives for 0 false positives and does a good job at it. Therefor all warning should be enabled: `-enable=all`

### Clang's Static Analyzer.

Clang's analyzer's default options are good for the respective platform. It can be used directly [from cmake](http://garykramlich.blogspot.com/2011/10/using-scan-build-from-clang-with-cmake.html).

## Code Coverage Analysis.

A coverage analysis tool shall be run when tests are executed to make sure the entire application is being tested. Unfortunately, coverage analysis requires that compiler optimizations be disabled. This can result in significantly longer test execution times.

The most likely candidate for a coverage visualization is the [lcov](http://ltp.sourceforge.net/coverage/lcov.php) project. A secondary option is [coveralls](https://coveralls.io/), which is free for open source projects.

Another good tool to use is k-cov, it can integrates with bazel quite well.

## Build Tool.

Use [wio]. If not available then use Bazel or Cmake.

## Unit Tests.

There should be a test enabled for every feature or bug fix that is committed. These should
be placed in a test folder according to [wio].


# Style.

Style guidelines are not overly strict. The important thing is that code is clear and readable with an appropriate amount of whitespace and reasonable length lines. A few best practices are also mentioned.

## Keep lines 90 characters length.
This is based on the fact that back in the day 80 characters used to be standard, but now we have bigger screens, hence we should use them.

## Descriptive and Consistent Naming.

C++ allows for arbitrary length identifier names, so there's no reason to be terse when naming variables. Use descriptive names, and be consistent in the style

 * `camelCase`
 * `PascalCase`
 * `CAPITAL_SNAKE_CASE`
 * `snake_case`

are common examples. snake_case has the advantage that it can also work with spell checkers, if desired.

### Common C++ Naming Conventions.

 * Types are PascalCase: `MyClass`
 * Functions and variables are camelCase: `myMethod`
 * Macros are CAPTIAL_SNAKE_CASE: `#define MY_PI 3.14159265358979323`

*Note that the C++ standard does not follow any of these guidelines. Everything in the standard is lowercase only.*

### Distinguish Private Member Variables.

Name private data with a `m_` prefix to distinguish it from public data.

### Distinguish Static Member Variables.

Name static member variables with an `s_` prefix.

### Well formed example.

```cpp
class MyClass {

public:

    MyClass(int data) : m_data(data) {

    }

    int getData() const {
        return m_data;
    }

private:

    int m_data;

};
```

## Distinguish C++ Files From C Files.

C++ source file should be named `.cpp` NOT `.c or .cc`
C++ header files should be named `.hpp` NOT `.h or .hh`

## Use `nullptr`.

C++11 introduces `nullptr` which is a special type denoting a null pointer value. This should be used instead of 0 or NULL to indicate a null pointer.

## Comments.

Comment blocks should use `//`, not `/* */`. Using `//` makes it much easier to comment out a block of code while debugging.

```cpp
// this function does something
int myFunc() {
}
```

To comment out this function block during debugging we might do:

```cpp
/*
// this function does something
int myFunc() {
}
*/
```

which would be impossible if the function comment header used `/* */`

## Never Use `using` In a Header File.

This causes the name space you are `using` to be pulled into the namespace of the header file.


## Include Guards.

Header files must contain an distinctly named include guard to avoid problems with including the same header multiple times or conflicting with other headers from other projects.

```cpp
#ifndef MYPROJECT_MYCLASS_HPP
#define MYPROEJCT_MYCLASS_HPP

namespace my_project {

class MyClass {
};

}

#endif
```

## A space between keyword and open brace and no open-brace on one line.
```cpp
// Bad Idea
for (int index = 0; index < 15; ++index)
{
  ++sum;
  std::cout << index << std::endl;
}

// Bad Idea
for (int index = 0; index < 15; ++index){
  ++sum;
  std::cout << index << std::endl;
}

// Bad Idea
class MyClass
{
public:
  MyClass(const std::string &t_value)
    : m_value(t_value)
  {
  }

  std::string get_value() const
  {
    return m_value;
  }

private:
  std::string m_value;
}

// Good Idea
class MyClass {

public:

  MyClass(const std::string & value)
    : m_value(value) {
  }

  std::string get_value() const {
    return m_value;
  }

private:

  std::string m_value;

}
```

## 4 spaces indent level.

Tabs are not allowed, and a mixture of tabs and spaces is strictly forbidden. Modern autoindenting IDEs and editors require a consistent standard to be set. We can run cpp check and clang format on older things to fix the spacing.

```cpp
// Good Idea
int myFunction(bool myInput) {

  if (myInput) {
    // do something
  }

}
```

## {} are required for blocks (i.e. `if, for, while`).
Leaving them off can lead to semantic errors in the code.

```cpp
// Bad Idea
// this compiles and does what you want, but can lead to confusing
// errors if close attention is not paid.
for (int index = 0; index < 15; ++index)
  std::cout << index << std::endl;

// Bad Idea
// the cout is not part of the loop in this case even though it appears to be
int sum = 0;
for (int index = 0; index < 15; ++index)
  ++sum;
  std::cout << index << std::endl;


// Good Idea
// It's clear which statements are part of the loop (or if block, or whatever)
int sum = 0;
for (int index = 0; index < 15; ++index) {
  ++sum;
  std::cout << index << std::endl;
}
```

## Keep conditions readable.

```cpp
// Bad Idea
// hard to follow
if (x == false && y == true && myFunctionThatReturnsBool() && caseNumber3 && (15 > 12 || 2 < 3)) {
}

// Good Idea
// Logical grouping, better to read
if (x == false && y == true && myFunctionThatReturnsBool() &&
    caseNumber3 &&
    (15 > 12 || 2 < 3)) {
}

// Great Idea
// Logical simplification and grouping everything on it's own line, way nicer to read.
if (x == false &&
    y &&
    myFunctionThatReturnsBool() &&
    caseNumber3 &&
    (15 > 12 || 2 < 3)) {
}
```


## Use "" For Including Local Files.
... `<>` is [reserved for system includes](http://blog2.emptycrate.com/content/when-use-include-verses-include).

```cpp
// Bad Idea. Requires extra -I directives to the compiler
// and goes against standards
#include <string>
#include <includes/MyHeader.hpp>

// Worse Idea
// requires potentially even more specific -I directives and
// makes code more difficult to package and distribute
#include <string>
#include <MyHeader.hpp>


// Good Idea
// requires no extra params and notifies the user that the file
// is a local file
#include <string>
#include "MyHeader.hpp"
```

## Initialize Member Variables.
...with the member initializer list

```cpp
// Bad Idea
class MyClass {

public:

    MyClass(int value) {
        m_value = value;
    }

private:

    int m_value;
};


// Good Idea
// C++'s member initializer list is unique to the language and leads to
// cleaner code and potential performance gains that other languages cannot
// match
class MyClass {

public:

    MyClass(int value) : m_value(value) {
    }

private:

    int m_value;

};
```

## Forward Declare when Possible.

This:
```cpp
// some header file
class MyClass;

void doSomething(const MyClass &);
```

instead of:

```cpp
// some header file
#include "MyClass.hpp"

void doSomething(const MyClass &);
```

This is a proactive approach to simplify compilation time and rebuilding dependencies.

## Always Use Namespaces.

There is almost never a reason to declare an identifier in the global namespaces. Instead, functions and classes should exist in an appropriately named namespaces or in a class inside of a namespace. Identifiers which are placed in the global namespace risk conflicting with identifiers from other (mostly C, which doesn't have namespaces) libraries.

## Avoid Compiler Macros.

Compiler definitions and macros are replaced by the pre-processor before the compiler is ever run. This can make debugging very difficult because the debugger doesn't know where the source came from.

```cpp
// Good Idea
namespace my_project {

    class Constants {

    public:
        static const double s_pi = 3.14159;
    }
}

// Bad Idea
#define PI 3.14159;
```
# Performance and Safety.

## Limit Variable Scope.

Variables should be declared as late as possible, and ideally, only when it's possible to initialize the object. Reduced variable scope results in less memory being used, more efficient code in general, and helps the compiler optimize the code further.

```c++
// Good idea
for (int index = 0; index < 15; ++index) {

    MyObject obj(index);
    // do something with obj
}

// Bad Idea
MyObject obj; // meaningless object initialization

for (int index = 0; index < 15; ++index) {
    obj = MyObject(i); // unnecessary assignment operation
    // do something with obj
}
// obj is still taking up memory for no reason
```

## Use Exceptions Instead of Return Values to Indicate Error.

Exceptions cannot be ignored. Return values, such as using `boost::optional`, can be ignored and if not checked can cause crashes or memory errors. An exception, on the other hand, can be caught and handled. Potentially all the way up the highest level of the application with a log and automatic restart of the application.

Stroustrup, the original designer of C++, [makes this point](http://www.stroustrup.com/bs_faq2.html#exceptions-why).

## Avoid raw memory access.

Raw memory access, allocation and deallocation, are difficult to get correct in C++ without [risking memory errors and leaks](http://blog2.emptycrate.com/content/nobody-understands-c-part-6-are-you-still-using-pointers). C++11,C++14,C++17 provides tools to avoid these problems.

```cpp
// Bad Idea
MyClass * myobj = new MyClass;

// ...
delete myobj;


// Good Idea
std::shared_ptr<MyClass> myobj = make_shared<MyClass>();
// ...
// myobj is automatically freed for you whenever it is no longer used.

// Great Idea (unique only if you can)
std::unique_ptr<MyClass> myobj = make_unique<MyClass>();
// ...
// myobj is automatically freed for you whenever it is no longer used.
```

## Avoid global data.
... this includes singleton objects

Global data leads to unintended sideeffects between functions and can make code difficult or impossible to parallelize. Even if the code is not intended today for parallelization, there is no reason to make it impossible for the future.


## Prefer pre-increment to post-increment.
... when it is semantically correct. Pre-increment is [faster](http://blog2.emptycrate.com/content/why-i-faster-i-c) then post-increment because it does not require a copy of the object to be made.

```cpp
// Bad Idea
for (int index = 0; index < 15; index++) {
    std::cout << index << std::endl;
}


// Good Idea
for (int index = 0; index < 15; ++index) {
    std::cout << index << std::endl;
}

```

## Const as much as possible.
`const` tells the compiler that a variable or method is immutable. This helps the compiler optimize the code and helps the developer know if a function side effects. Also, using `const &` prevents the compiler from copying data unnecessarily. [Here](http://kotaku.com/454293019) are some comments on const from John Carmack.

Use const getters.
```cpp
// Bad Idea
class MyClass {

public:

  MyClass(std::string value)
    : m_value(value) {
  }

  std::string get_value() {
    return m_value;
  }

private:

  std::string m_value;

}


// Good Idea
class MyClass {

public:

  MyClass(const std::string & value)
    : m_value(value) {
  }

  std::string get_value() const {
    return m_value;
  }

private:

  std::string m_value;

}
```


## Prefer Stack Operations to Heap Operations.

Heap operations have performance penalties in mulithreaded environments on most platforms and can possibly lead to memory errors if not used carefully.

Modern C++11,14,17 has special move operations which are designed to enhances the performance of stack based data by reducing or eliminating copies, which can bring even the single threaded case on par with heap based operations.
