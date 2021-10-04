# Chapter 1 Deducting Types
2021-10-04

- [Chapter 1 Deducting Types](#chapter-1-deducting-types)
- [Introduction](#introduction)
- [Item 1 : Understand template type deduction.](#item-1-understand-template-type-deduction)
  - [Case 1: `ParamType` is a Reference or pointer, but not a Universal Reference.](#case-1-paramtype-is-a-reference-or-pointer-but-not-a-universal-reference)
  - [Case 2: `ParamType` is a Universal Reference.](#case-2-paramtype-is-a-universal-reference)
  - [Case 3: `ParamType` is Neither a Pointer not a Reference.](#case-3-paramtype-is-neither-a-pointer-not-a-reference)
  - [Array Arguments](#array-arguments)
  - [Function Arguments](#function-arguments)


# Introduction
`auto` and `decltype` in C++14. The increasingly widespread application of type deduction frees you from the tyranny of spelling out types that are obvious or redundant. It makes C++ software more adaptable, because changing a type at one point in the source code automatically propagates through type deduction to other locations. However, it can render code more difficult to reason about, because the types deduced by compliers may not be as apparent as you'd like. 

This chapter provides the information about type deduction that every C++ developer requires. It explains how template type deduction works, how `auto` builds on that, and how `decltype` goes its own way. 
It even explains how you can force compilers to make the results of their type deductions visible, thus enabling you to ensure that compilers are deducting the types your want them to. 

# Item 1 : Understand template type deduction. 

When user of a complex system are ignorant of how it works, yet happy with what it does, that says a lot about the design of the system. By this measure, template type deduction in C++ is a tremendous success. By this measure, template type deduction in c++ is a tremendous success. Millions of programmers have passed arguments to template functions with completely satisfactory results, even though many of these programmers would be hard-passed to given more than the haziest description of how the types used by those functions were deduced. 
If that group includes you, I have good news and bad news. 
**Good news** : The type deduction from templates is the basis of onf the modern C++'s most compelling features: `auto`. If you were happy with how C++98 deduced types for templates, you’re set up to be happy with how C++11 deduces types for `auto`.
**Bad news** : when the template deduction rules are applied in the context of `auto`, they sometimes less intuitive than when they're applied to templates. For that reason, it is important to truly understand the aspects of template type deduction that `auto` builds on. This **Item** covers what you need to know. 

If you're willing to overlook a pinch of pseudocode, we can think of a function template as looking like this:
```cpp
template<typename T>
void f(ParamType param);
```
A call can look like this: 
```cpp
f(expr);   // Call f with some expression. 
```
During compilation, compilers use `expr` to deduce to types:
1. One for `T`
2. one for `ParamType`. 
These types are frequently different, because `ParamType` often contains adornments, e.g., `const` or reference qualifiers.
For example, if the template is declared like this: 

```cpp
template<typename T>
void f(const T& param); // ParamType is const T&
```
and we have this call: 

```cpp
int x = 0; 
f(x);  // Call f with an int
```
In this example: 
`T` is deducted to be `int`, but `ParamType` is deduced to be `const int&`. 

It's natural to expect that the type deducted from `T` is the same as the type of the argument passed to the function, i.e., that `T` is the type of `expr`. In the above example, that's the case, `x` is an `int`, and `T` is deduced to be `int`. But it doesn't always work that way. The type deduced for `T` is dependent not just on the type of `expr`, but also on the form of `ParamType`. There are three cases: 
+ `ParamType` : is a pointer or reference type, but not a universal reference. 
+ `ParamType` : is a universal reference. 
+ `ParamType` : is a neither a pointer nor reference. 

Universal references are described in **Item 24**. At this point, all you need to know is that they exist and that they're not the same as lvalue references or the rvalue reference. 

We therefore have three type deduction scenarios to examine. Each will be based on our general form for the templates and calls to it: 
```cpp
template<typename T>
void f(ParamType param);
f(expr); // deduce T and ParamType from expr
```

## Case 1: `ParamType` is a Reference or pointer, but not a Universal Reference. 

The simplest situation is when `ParamType` is a reference type of a pointer type, but not a universal reference. In that case, type deduction works like this: 

1. if `expr`'s type is a reference, ignore the reference part. 
2. The pattern-math `expr`'s type against `ParamType` to determine `T`. 

For example, if there is our template,
```cpp
template<typename T>
void f(T& param); // param is a reference
```

We have these variable declarations
```cpp
int x = 27; // x is an int
const int cx = x; // cx is a const int
const int& rx = x; // rx is a reference to x as a const int
```

The deduced types for `param` and `T` in various calls are as follows: 

```cpp
f(x) ; // T is int, param's type is int&
f(cx) ; // T is const int
        // param's type is const int& 
f(rx) ; // T is const int. 
        // pram's type os const int&
```

In the second and third calls, notice that because `cx` and `rx` designate `const` values, `T` is deducted to be `const int`, thus yielding a parameter type of `const int&`. That is important to callers. When they pass a `const` object to a reference parameter, they expect that object to remain unmodifiable. That is way passing a `const` object to a template taking a `T&` parameter is safe: the `const`ness of the object becomes part of te type deduced for `T`. 


## Case 2: `ParamType` is a Universal Reference. 

## Case 3: `ParamType` is Neither a Pointer not a Reference. 

## Array Arguments

## Function Arguments