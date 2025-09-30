# Table of content

1. [Pointer & smart pointer](#pointer--smart-pointer)
1. [Pointer, Reference and copy](#Pointer,-Reference-and-copy)
1. [Container](#Container)
1. [Inheritance](#Inheritance)
1. [Polymorphic](#Polymorphic)
1. [Compilation process](#compilation-process)
1. [Template](#Template)
2. [Cast](#cast)
1. [Inheritance vs Composition](#Inheritance-vs-Composition)

**Stack vs heap**

- **Stack**
  * Faster access to this memory than accessing to the heap
  * Allocation and deallocation are automatic. There is no need to handle that part. A reserved space is reserved for each local variable. Those variables will be automatically destroyed at end the end of their scope.

- **Heap**
  * Permanent memory that must be handled manually by the user
  * Allocated a new variable, array, buffer... is processed using **new**, **malloc** or **calloc**
    * **malloc**: Simple allocation on the heap
    * **calloc**: Simple allocation on the heap and set all the allocated memory to a given byte (usually 0)
    * **new**: C++ version. Correspond to a **malloc** + a call to the constructor.
  * Deallocated a variable is proccess using **free** or **delete**
    * **free**: Deallocate an allocation made using **malloc** or **calloc**. Crash if we try to free a `nullptr` pointer.
    * **delete**: Call the destructor of the object and deallocate the mem;ory. Can only be used with memory allocated using **nez**. Can be use with **nullptr**. In that case, nothing will happen.
  * If the allocated memory is not deallocated, the memory will stay reserved which leads in less memory available which might lead to a crash. This is a **memory leak**.

## Pointer & smart pointer

- **pointer** : variable that store the adress of an object in memory. It can be nullptr (which means 0) or have a value. If it has a value, it is impossibe for a program to determine if this value is right or not. (it can point on outside the program's memory, point on already free memory...). Using a invalid pointer will crah.
- Because allocated memory on the **heap** is often not deallocated, wrapper around raw pointers have been created to release the memory once no one needs them : **smart_ptr**
  * **shared_ptr** : Pointer that can be shared between every one. If someone needs it, he can increment a ref count and keep a referece to this wrapper. Once he has done his job using this pointer, he can set it to nullptr, the ref count is decreased. If the ref count turns 0, that means no one use it anymore so the memory can be released.
  * **weak_ptr** : Works in pair with **shared_ptr**. Keeping a **weak_ptr** will not increase the ref count of the **shared_ptr**, but to retrieve the data you must 'upgrade' the **weak_ptr** to a **shared_ptr**. Because it doesn't increase the ref count, it is possible that the **shared_ptr** has already been destroyed. In that case, the 'upgrade' process will fail.

  ```cpp
  	// Create on the heap
	MyClass* mc = new MyClass();

	// Also created on the heap
	std::shared_ptr<MyClass> spMc = std::make_shared<MyClass>();

	// weak_ptr on spMc, did not increase ref
	std::weak_ptr<MyClass> wpMc = spMc;

	{
		// New shared ptr, no allocation but the ref count has been added
		std::shared_ptr<MyClass> spMc2 = spMc;

		// At the end of this scope, spMc2 will be destroyed so the ref count will decrease
	}

	{
		// Upgrade the weak_ptr to a shared_ptr
		std::shared_ptr<MyClass> spMc2 = wpMc.lock();

		// There is a new shared_ptr in this scope, so the ref count will increase (and decrease at the end of the scope)
	}

	// spMc is now nullptr, there is no more object that has a ref on the instance of MyClass, the object will be deallocated
	spMc = nullptr;

	// The shared_ptr linked to our weak_ptr is destroyed. WIll return true
	bool bExpired = wpMc.expired();

	// spMc2 is empty because expired is true
	std::shared_ptr<MyClass> spMc2 = wpMc.lock();

	//mc has not de deleted so there is a memory leak
    ```

- **unique_ptr** : Works the same a **shared_ptr** but only one person has the ownership of this object

## Pointer, Reference and copy


## Container

## Inheritance

## Polymorphic

## Compilation process

## Template

## Cast

Casting (the right full name is **typecasting**) is a solution to convert a type to another. There are several types of cast which provide various features.
- **static_cast** :
  * Can only cast types that are compatible such as related types, numeric types, class in the sane inheritance hierarchy 
  * Casting is executed at compile time. That means that the check will be performed during conpilation. If it fails, that will result in a compilation error.
  * Because it is processed at compile time, their is no cost during runtime.
  ```cpp
  // Works
  int n = 10;
  double nd = static_cast<double>(n);

  // Does not work - Compilation error
  MyClass c = static_cast<MyClass>(n);
  ```

- **dynamic_cast** : 
  * Mainly used to performed downcasting, cast a base class to a derived one.
  * Such as **static_cast**, it performs small checks at compile time to ensure the cast is theorically possible
  * The real casting is performed at runtime because we need the actual instance to try to cast it to another type. This cast has a cost during runtime execution.
  * Might return nullptr if the cast is not possible.
```cpp
class B {};
class D1 : public B {};
class D2 : public B {};

B* d1 = new D1();

// casted_d1 has a value
D1* casted_d1 = dynanic_cast<D1*>(d1);

// casted_d2 his nullptr
D2* casted_d2 = dynanic_cast<D2*>(d1);
```
- **reinterpret_cast/C Cast**:
  * Perform any conversion without any check even if it has no sense to do it
  * Most dangeurous among all casts, you must know why you are using them.
```cpp

int i = 19;

// That works but... it will boum :)
MyClass* mc = reinterpret_cast<MyClass*>(&i);

// C style version
MyClass* mc = (MyClass*)&i
```
- **const_cast**:
  * Use to add or remove the const attribute from a variable
  * Breaks the entire architecture of your project, so it is not a right solution but sometimes you will need it.
  * If you have to use it, think about it. Is there any other solution? Yes, it is safe to do it (it won't break your program) but the const was there for a reason.
```cpp
const int i = 19;
i = 123; // will not compile

int* pI = &i; // will not compile

int* pI2 = const_cast<int*>(pI);
*pI2 = 123; // Works
```

## Inheritance vs Composition