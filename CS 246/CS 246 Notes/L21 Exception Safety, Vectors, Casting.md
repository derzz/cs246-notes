# Exception Safety
- It's not that a function never throws, or that it handles all exceptions, internally
- It's about that it guarantees we are given if we call a function and it throws our exception at us
## Levels of Exception Safety
1. Basic Guarantee- If an exception is thrown from a function `f`, then the program is in a valid but unspecified state
	- Class invariants maintained and there's no leaks, data corruption
2. Strong Guarantee
	- If an exception is thrown from a function `f`, then the program state is reverted to before `f`'s call
	- I.e. You never called `f` in the first place
3. No throw guarantee
	- If we call `f`, it will never throw and it will always do its job
```cpp
class C{
	A a;
	B b;
	public:
		void f(){
			a.g();
			b.h();
		}
};
```
 - What's the exception %% safety %% of C?
	 - Let's assume `A::g` and `B::h` both provide strong guarantee
	 - If `a.g()` throws, it undoes any side effects, so it's as if `f` was never called
	 - If `b.h()` throws, it undoes any of its own side effects, but it cannot undo `a.g()`'s work
	 - **Basic Guarantee**
 - Since `c::f` has basic guarantee, can we change it to provide strong guarantee?
	 - Idea: Uses temporary values, that was if we throw, a and b aren't changed, must assume `a.g()` and `b.h()` have only local side effects for this to work!
```cpp
class C{
	A a;
	B b;
	public:
		void f(){
			A atemp = a;
			B btemp = b;
			atemp.g();
			btemp.h();
			a = atemp;
			b = btemp;
		}
}
```
- This looks like it provides the strong guarantee, but if `b = bTemp` throws, we've modified a already- just basic guarantee
	- We need the ability to set a and b without throwing
## Pointer to Implementation Idiom
- Then swapping pointers will guaranteed to be **no throw**
```cpp
struct CImpl{
	A a;
	B b;	
}

class C{
	unique_ptr<CImpl> pImpl;
	void f(){
		auto temp = make_unique<CImpl>(*pImpl);
		temp->a.g();
		temp->b.h();
		std::swap(temp, pImpl); // Can't throw
	}
}
```
- **Strong guarantee!**
- In general, if `A::g` or `B::h` don't produce any exception safety, then neither can `C::f`
# Vectors- RAII, Exception Safety
- `Vector<C> v`, represents ownership of C objects, when destructor runs for `v` when it's out of scope
	- C objects are deleted, no polymorphism!
- Subclasses inserted into V will be sliced!
- `Vector<c*> w`- represents non ownership, when `w` does out of scope, destructor will not delete what is pointed to be the `C*`s
- `Vector<unique_ptr<c>> u`
	- When u goes out of scope, destructor runs, frees associated memory, ownership, and allow polymorphism
## Vectors and Exception Safety
- `vector<T>: emplace_back` supports strong guarantee
- If it throws, dynamically allocated array inside the vector is unchanged
- What happens when size == capacity?
	1. Allocate new array with two times capacity
	2. Copy each object from old array to new array, if one throws, delete the new array and rethrow
	3. Point our old array pointer at the new array
- This is a little inefficient- why not move instead of copy?
	1. Allocate new array
	2. Use move constructor to move each object to new array
	3. Reassign array pointers
- Doesn't provide strong guarantee- if a move constructor throws, our original array has been modified!(We stole data)
- If move constructor will not throw- the compiler will move elements in the vector rather than copy
- If the move constructor may throw, compiler will resize vectors via copying
## `noexcept`
- At a minimum, move and swaps should provide nothrow guarantee- tag them noexcept so the compiler can facilitate optimizations
```cpp
class C{
	C(C&& other) noexcept;
	C& operator =(C&& other) noexcept;
}
```
# Casting
```cpp
Node n;
int* ip = (int*) &n; // Forces Node* to be treated as an int*
```
- This is C-style casting and not recommended in C++!
	- Dangerous and not type safe!
- C++ instead provided 4 casting functions for different situations
## Static Cast
- `static_cast` for "sensible casts" with well defined semantics float-> int
```cpp
float f;
void g(int x);
void g(float f);
g(static_cast<int>(f)); // Calls int version of g
```
- `static_cast` allows us to downcast `superclass*` to `subclass*`
```cpp
Book* pb = ...;
Text* pt = static_cast<Text*>(pb);
cout << pt->topic << endl;
```
- Here, `pb` must point at a Text, otherwise undefined behaviour, trust me!