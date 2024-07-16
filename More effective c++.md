MORE EFFECTIVE C++

//POINTERS AND REFERENCES 

References must always refer to an object
If by any chance the object could be null, use a pointer instead.

Reference can never be null 

It is more effecient to use references than pointers since references dont need to be evaluated 

pointers may be reasigned to different objects 

A reference always refers to the same object in which it was iniitialized

Use a pointer when you need to take into account the possibility that there is nothing to refer to
in which case you can set it to null 

or if you need to refer to diferent things at different time 
in which case you can change what the pointer points

use a reference if you know that there will always be an object to refer to and once you are refering to that object, you will not refer to anything else. 

Use a reference when implementing certain operators such as operator[] 

using pointers instead will confuse the reader and make it seem as if the array is an array of pointers 

you almost always want operator[] to return a reference
however there is an interesting exception to the rule 
2D ARRAY

variables as dimension sizes is not allowed 

Proxy objects 
objects that stand for other objects

proxy clases
clases that give rise to proxy objects

Array 1D in the 2D array object would be a proxy class
Item 30 

Use references when you know you have something to refer to and you will never want to refer to anything else

They are appropriate when implementing operators whose syntactic requirements make the use of pointers undesirable


//CASTS

Avoid C-style casts and go-to 
c-style casts are dangerous since they allow you to cast one type to any other type 

C-style casts were designed for C and not for C++
casts are hard to find 

C++ has four new cast operators 
static_cast
const_cast
dynamic_cast
reinterpret_cast

static_cast cannot remove constness from an expression 
because const_cast is used for that 


const cast is used to cast away the constness or volatiles
of an expression 

const cast tells humans and the compiler that you want to change the constness or volatileness of an expression 

if you try to use const cast for anything other than modifying the constness of a variable, the cast will be rejected.

const_cast cannot be used to cast down the inheritance heierarchy 

dynamic_cast is used to perform safe casts down or across an inheritance hierarchy.

dynamic cast  casts pointers or references to base class objects into pointers or references to derived or sibling base class objects in such a way that you can determine whether the casts succeeded.

failed casts are indicated by a null pointer (when casting pointers) or an exception (when casting references)

dynamic casts are used to helping you navigate inheritance hierarchies. They cannot be applied to types lacking virtual functions nor can they cast away constness

static_cast for types where inheritance is not involved 
const_cast for when you want to cast away constness

reinterpret_cast is used to perform type conversions whose result in nearly always imlementation-defined.
reinterpret_casts are rarely portable.

common use of reinterpret_cast to to cast between function pointer types
reinterpret_cast forces the compiler to see things your way
example, if you have an array of function pointers that point to functions returning void and taking no arguments, and you want an index to point to a function that returns int, you would need to use a reinterpret cast.

casting function pointers is not portable (C++ offers no guarantee that all function pointers are represented the same way), in some cases they may yield incorrect results 

Avoid casting function pointers

There is no easy way to emulate the behaviour of dynamic cast 
however, there are libraries the provide functions to perform safe inheritance based casts for you 

//INHERITANCE
Derived classes usually have more members than their base class so derived class objects are usually larger than baseclass objects 

Polymorphism and pointer arithmetic dont mix 
Array operations almost always involve pointer arithmetic

You should design your software so that concrete classes never inherit from one another
there are many benefits in following this rule

//CONSTRUCTORS
constructors guarantee that objects are initialized 

classes that lack default constructors are ineligible for use with many template-based container classes 

template classes require the type to have a default constructor 

don't allow exceptions to propagate out of the constructor

if a member may throw on initialization, use smart pointers 
to manage the resource to avoid memory leaks 

//DESTRUCTORS
prevent exceptions from leaving destructors

destructors are called when an object is destroyed under normal conditions eg when it goes out of scope or when the object is explicity deleted
desctructors are called when an object is destroyed by the exception handling mechanism during exception propagation


you cant disinguish between these conditionas inside a destructor


if control leaves a destructor due to an exception while another exception is active c++ calls the terminate function and your program exits immediately not even local objects are destroyed
catch(...) will prevent exceptions from propagating out of the class to the caller


preventing exceptions from propagating out of destructors 
if an exception is thrown from a destructor and is not caught there, then the destructor will not do everything it is supposed to do since it will not run to completion

prevents terminate from being called and it helps ensure that destructors accomplish everything they are supposed to do


//OPERATORS
Single argument ocnstructors and implicit conversion operators are troublesome since there is no source coude to show the calls

Be wary of user defined conversion functions
C++ allows compilers to perfrom implicit conversion between types due to its C heritage


    single argument constructors 
    implicit type conversion operators 

implicit conversion operator syntax example 

operator double() const;   
two kinds of functions allow compilers to perform such conversions (implicit conversions)
convertes the type to the specified type

usually you don't want to provide type conversion functions 
functions often end up being called when you neither want nor expect them to be. 

implicit type conversion can lead to the wrong function being called 

instead replace the implicit conversion with a function 
that returns the type of the operator 


class Rational {public:  ...  double asDouble() const;     // converts Rational};                             // to double

one argument constructors may rsult in undefined behaviour 
in order to prevent the one argument ocnstructor from being called indiscriminately is to use the explicit keyword 

 the compiler will not permit two implicit ocnversions in a row only one is permited 

 //PROXY OBJETS 
 proxy objects are objects that stand for something else 
 see the array template class example on page 61 
 the advantage of proxy objects is that they give us control over aspects of software behaviour for example, implicit type conversions 

 granting compilers license to perform implicit type conversions usually lead to more harm than good 

 only provide conversion functions if you are sure you want them

 //PREFIX POSTFIX INCREMENT AND DECREMENT OPERATORS 
 prefix and postfix return diferent types 
 prefix forms return a reference 
 postfix forms return a const object

 prefix = incrment and fetch 
 postfix fetch and increment 

 post fix incrment  must return an object 
 (returns the old value)
 must return a const object else this is valid
 i++++
 this conflicts with the built-int types and it is inconsistent 
 when in doubt do what the built in types do 

 prefer prefix operator overloads to postfix 
 since they are more efficient (no temporary object is created)

 only overload postfix if you really need the postfix behaviour 

//&&, ||, or ,
never overload 

c++ uses short circuit evaluation of boolean expressions
once the truth or falsehood of an expression has been determines, evaluation of the expression ceases, even if some parts of the expression havent been yet examined 

The purpose of operator overloading is to make programs easier to read, write, and understand

//NEW AND DELETE 
new only does two things 
allocates enough memory for the object
calls the constructor to initialize the object in the memory that was allocated 
 
operator new can raise an exceptions if it fails to allocate enough memory for the object.

c++ desstroys only fully constructed objects, and an object isint fully constructoed until its constructor has run to completion

to avoid resource leaks every dynamic allocation must be matched by an equal and opposit deallocation.

delete is a built in operator 

//OPERATOR NEW DECLARATION 
“void * operator new(size_t size);”

returns a pointer to raw uninitialized memory
never call new directly 

new only allocates memory 
operator new returns and transforms it into an object

the purpose of operator new is to find memory for an object and return a pointer to that memory

to use placement new 
use the new library 
#include <new>

if you want to create an object on the heap, use new operator
it both allocates memory and calls a constructor for the object 

//EXCEPTIONS
replicated code is annoying, difficult to maintain, and feels wrong

exceptions can be passed by value, reference, or by pointer

when you throw an exception control does not return to the throw site 

objects thrown as exceptions are copied 
even if they are static

even if the catch block catches the exception by reference,
only the copy of the object is mutable not the original

objects static stypes are thrown, not their dynamic type 
however there are techniques to bypass this 

the compiler only cares about the objects static type

use the throw syntax to rethrow the current exception
performance efficient since no copy is being made 
throws exception of the objects dynamic type 

exceptions are rare

no return is needed since the return is the type of the operator 
throw by pointer is similar to pass by pointer
dont ever throw a pointer to a local object 

there are no implicit conversions when catching exceptions an object of type int cant be caught by a catch block that accepts a double 

two conversions are applied when matching exceptions to catch clauses 

catching values and pointers is generally a bad idea 

1,  inheritance based conversions 

    A catch clause for base class exceptions is allowed to handle eceptions of publicly derived class types too

2, typed to untyped pointer conversion 
    eg, a catch clause taking a const void* pointer will catch an exception of any pointer type
never put a catch clause for a base class before a catch clause for a derived class 

SMART POINTERS
objects that act like pointers but do more are called smart pointers 

auto pointer takes a heap allocated object in its constructor 

and deletes the object in its destructor 
when going out of scope 

use smart pointers instead of raw pointer objects and you wont have to worry about heap objects not being deleted, not even when exceptions are thrown


if you want an auto_ptr template for arrays, use a vector instead

auto-ptr uses the single form of delete which is not suitable for use with pointers to arrays of objects 
smart pointers will fail to de-allocate their resource if an exception is thrown while allocating memory for the object

C++ does not call the destrutor on partially alllocated objects becuse it is nonsensical and it may be harmful 

the destructor would not even know what to do 
unlesss you trace you trce how much of the constructor had been excecuted. This will result in the object increasing in size and slow down the constructor.

c++ avoids this but partially allocated objects are not destroyed. 

replace pointer class members with smart pointers, then you fortify your constructors against resource leaks
in the prescence of exceptions 

you eliminate the need to manually deallocate resources in destructors, and you allow const member pointers to be handled in the same graceful fashion as non-const pointers 

smart pointers improve the readability of the code 
smart pointrs are robust in the face of exceptions

//INITIALOIZER LISTS
initializer lists only allow expressions
not statements 
you cant try catch in an initializeer list 
you cant if else chain in an initializer list you need to use arithmatic construct instead

//COPY CONSTRUCTORS
copies are always made on an objects static type 
however, there are techniques that lets you make copies on the basis of an objects dynamic type


//LAZY EVALUATION
Writing your classes in such a way that they defer 
computations until the results of those computations are 
required 

Eager evaluation: (Don't make coppies of objects 
if they're not used)

Saves cost of a call to new and the expense of copying anything

use someone else's copy as long as you can get away with it

* in some application areas, you can ofter get away with it forever

to mutate objects despite their constness within any member function, use the mutable qualifier

If your vendors don't support it use the fake {this}
approach 

* create a pointer to non const that points to the same 
* object as this does. When you want to modify a data member,
* you access it through the fake this pointer

*Avoids*
*Paltry copying of objects*
*Distinguish reads from writes using operator[]*
*To avoid paltry reads from databases*
*Avoid paltry numerical computations*

*Lazy evaluation is only useful when there's a reasonable chance your software will be asked to perform computations that can be avoided.*

