# C++ Learning Doc

- [C++ Learning Doc](#c-learning-doc)
  - [Data Structure](#data-structure)
    - [std::array](#stdarray)
    - [wise_enum](#wise_enum)
      - [Examples](#examples)
  - [Language rules](#language-rules)
    - [__function__](#function)
    - [std::experimental::optional](#stdexperimentaloptional)
    - [final](#final)
    - [std::underlying_type](#stdunderlying_type)
    - [virtual class](#virtual-class)
  - [Language Candy](#language-candy)
    - [const function](#const-function)
    - [Difference between `begin` and `cbegin`](#difference-between-begin-and-cbegin)


## Data Structure
THis section will talk about the data structure related topic. 

### std::array

since c++ 11 

```cpp
template<
    class T,
    std::size_t N
> struct array;
```

std::array is a container that encapsulates fixed size arrays. 
This container is an aggregate type with the same semantics as a structure holding c_style array `T[N]` as its only non-static data member. Unlike c-style array, it doesn't decay to `T*` automatically.
As an aggregate type, it can be initialized with aggregate-initialization given at most N initializers that are convertible to T: 
`std::array<int, 3> a = {1, 2, 3};`. 
The structure combines the performance and accessibility ofa C-Style array with the benefits of a standard container, such as knowing its owns size, supporting assignment, random access iterators, etc. 

### wise_enum
link to [wise_enum](https://github.com/quicknir/wise_enum)
`wise_enum` is a standalone smart enum library. It supports all of the standard functionality that you would expect from a smart enum class in C++. 
1. Tells you the number of enumerators. 
2. Lets you iterate over all enum values. 
3. Coverts string to enum, end enum to string.
4. Does everything in an idiomatic C++ way. 

#### Examples
```cpp
// Equivalent to enum Color {GREEN = 2, RED};
WISE_ENUM(Color, (GREEN, 2), RED)
```



## Language rules

### __function__
 
__func__ is an implicitly declared identifier that expands to a character array variable containing the function name when it is used inside of a function. 
__PRETTY_FUNCTION__ is a gcc extension that is mostly the same as __FUNCTION__, except that for C++ functions it contains the "pretty" name of the function including the signature of the function. 
```cpp
$ cat test.cpp 
#include <iostream>

int main(int argc, char **argv)
{
    std::cout << __func__ << std::endl
              << __FUNCTION__ << std::endl
              << __PRETTY_FUNCTION__ << std::endl;
}
$ g++ test.cpp 
$ ./a.out 
main
main
int main(int, char**)
```

### std::experimental::optional
The class template std::experimental::optional manages an optional contained value, i.e. a value that semantically may not be present.
A common use case for optional is the return value of a function that may fail. As opposed to other approaches, such as std::pair<T,bool>, optional handles expensive to construct objects well and is more readable, as the intent is expressed explicitly.
The value is guaranteed to be allocated within the optional object itself, i.e. no dynamic memory allocation ever takes place. Thus, an optional object models an object, not a pointer, even though the operator*() and operator->() are defined.

### final
Specifies that a virtual function cannot be overridden in a derived class or that a class cannot be inherited from.

### std::underlying_type
Defined in header <type_traits>
```cpp
template< class T >
struct underlying_type;
```
If T is a complete enumeration (enum) type, provides a member typedef type that names the underlying type of T. 
**Note**: Each enumeration type has an `underlying type`, which can be: 
1. Specified explicitly (both scoped and unscoped enumerations)
2. Omitted, in which case it is `int` for scoped enumerations or an implementation-defined integral type integral type capable of representing all values of the enum (for unscoped enumerations). 

**Examples**
```cpp
#include <iostream>
#include <type_traits>
 
enum e1 {};
enum class e2 {};
enum class e3: unsigned {};
enum class e4: int {};
 
int main() {
 
  constexpr bool e1_t = std::is_same_v< std::underlying_type_t<e1>, int >;
  constexpr bool e2_t = std::is_same_v< std::underlying_type_t<e2>, int >;
  constexpr bool e3_t = std::is_same_v< std::underlying_type_t<e3>, int >;
  constexpr bool e4_t = std::is_same_v< std::underlying_type_t<e4>, int >;
 
  std::cout
    << "underlying type for 'e1' is " << (e1_t ? "int" : "non-int") << '\n'
    << "underlying type for 'e2' is " << (e2_t ? "int" : "non-int") << '\n'
    << "underlying type for 'e3' is " << (e3_t ? "int" : "non-int") << '\n'
    << "underlying type for 'e4' is " << (e4_t ? "int" : "non-int") << '\n'
    ;
}
```

```bash
underlying type for 'e1' is non-int
underlying type for 'e2' is int
underlying type for 'e3' is non-int
underlying type for 'e4' is int
```


### virtual class
The = 0 syntax declares a pure virtual function, it has nothing to do with the return value.
If a class contains at least one pure virtual function, that makes the class "abstract", which means it cannot be instantiated.
In practice, such classes need to be concretized by subclassing and implementing the virtual function(s).


## Language Candy

### const function
A "const function", denoted with the keyword const after a function declaration, makes it a compiler error for this class function to change a member variable of the class. However, reading of a class variables is okay inside of the function, but writing inside of this function will generate a compiler error.


### Difference between `begin` and `cbegin`
First of all `cbegin` means constant begin. `begin` will return an `iterator` or `const_iterator` depending on the const-qualification of the object it is called on. 
`cbegin` will return a `const_iterator` unconditionally. 

```cpp
std::vector<int> vec;
const std::vector<int> const_vec;

vec.begin(); //iterator
vec.cbegin(); //const_iterator

const_vec.begin(); //const_iterator
const_vec.cbegin(); //const_iterator
```


