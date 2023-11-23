```cpp
TicTacToe: class Board{
	...
	cout << "Your Move" << endl;
};
```
- If board shouldn't perform communication, then who? `main`? Generally, no `main` is not reusable
# MVC
![[Pasted image 20231121114233.png]]
- Model- Keep track of data and logic surrounding data
	- Keeps track of data and communicates with the controller via parameters/results
	- Sometimes observer relationship between model and view(s)
- View- Output and communication with the user
- Controller- Manages the input and control flow between the model and the view
	- May encapsulate logic for rules/turn-taking
	- Sometimes input is taken from `view` if it's a window
- **MVC promotes the reuse of code!**
# Exception Safety
```cpp
void f(){
	// Assume C is a class
	C myC{};
	C* myCPtr = new C{};
	g(); // f() leaks depends if g() throws an exception
	delete myCPtr;
}
```
- If `g` doesn't throw, we're good
- If `g` throws, control is immediately transferred to catch
	- Stack unwinding runs the `myC` destructor, heap memory is leaked
```cpp
// Fixed version
void f(){
	C myC{};
	C* myCPtr = new C{};
	try{g();}
	catch(...){delete myCPtr; throw;}
	delete myCPtr; // No leaks, if g throws or not
}
```
- Works but we have duplicated code in deleting `myCPtr`
	- Have to repeat for any function that may throw and only get more complex with more pointers and functions that we have
- We wanted to make sure that `delete` runs if we leave `f` normally or via exception
	- Other languages have `finally` which always runs, `C++` doesn't
- Guarantee: Stack allocated object destructors will run during stack unwinding
- Solution: Wrap dynamically allocated memory in a stack allocated object
	- And in general, prefer to use stack allocated objects, since we don't have to worry about leaks
# RAII/Unique Pointers
- Resource acquisition is initialization
- Constructor acquires some resource
- Destructor releases the resource
```cpp
{
	ifstreamf{"file.txt"}; // file is acquired in constructor
} // file closed in destructor
```
- `std::unique_ptr<T>` found in `<memory>` is on RAII class that manages dynamic memory
	- Constructor takes in a `T*`
	- Destructor deletes it for you
```cpp
void f(){
	C myC{};
	unique_ptr<C> my CPtr {new C{}};
	g(); // No leaks whether we leak normally or via exception
}
```
- OR: Use make unique
```cpp
unique_ptr<C> myCPtr = std::make_unique<C>(...);
```
- `make_unique` creates a `C` object in the heap for you using `new`, and the arguments you give to `make_unique`
- What happens if we copy a unique pointer?
```cpp
auto p = make_unique<C>(...);
unique_ptr<C> q = p; // Copy constructor for unique pointers
```
- Copy constructor and copy assignment
	- Disabled for unique pointers, code won't compile if we invoke them
	- If we pointed them at the same location => double delete
## How to Implement Unique Pointers
```cpp
template<typename T> class unique_ptr{
	T* ptr;
	public:
		explicit unique_ptr(T* ptr): ptr{ptr}{}
		// destructor
		~unique_ptr(){delete ptr;}
		// Copy constructor
		unique_ptr(const unique_ptr<T> &other) = delete;
		// Copy assignment operator
		unique_ptr<T>& operator=(const unique ptr<T>& other) = delete;
		// Move constructor
		unique_ptr(uniqueptr<T>&& other): ptr{other.ptr}{
			other.ptr = nullptr;
		}
		// Move assignment operator
		unique_ptr<T>& operator = (unique_ptr<T>&& other){
			delete ptr;
			ptr = other.ptr;
			other.ptr = nullptr;
			return *this;
		}
		T& operator*(){return *ptr;}
}
```
- If I need to copy a pointer:
	- First, who is in charge of ownership? Whoever owns the memory gets the `unique_ptr`
	- Everyone who just uses memory, access via raw pointers, can get those via `piget()`
	- New understanding of ownership, can be signalled via type
- `unique_ptr`
	- Represents ownership, associated memory is deleted when it goes out of scope
- `Raw pointers`
	- Represent non-ownership, default is they do not free memory when they go out of scope
- Moving `unique_ptr`
	- Transfer of ownership
- Parameters- `void f(unique_ptr<c> p)` takes ownership of the `unique_ptr` deleted when `f` finishes
- `void g(C* p)` ownership is not transferred from caller to `g`
	- `g` should just use `p`, not delete it
	- Results: 
		- `unique_ptr<c> f()`
			- `return`s always move, pointer is now owned by the caller
	- `c* g()`
		- Caller shouldn't delete returned value- stack memory, or owned by someone else
- Rarely, we may need true shared ownership, consider a graph data structure
## Shared Pointer
- In such a case, we can use shared pointers
```cpp
{
	auto p = make_shared<c>{...};
	if(...){
		auto q = p;	
	} // q goes out of scope, doesn't delete, p exists
} // p goes out of scope, nobody points at the C object anymore and it's deleted
```
- Shared pointers work by maintaining a <u>reference count</u> on creation, incremented
	- On deletion, decremented
	- When it reaches 0, underlying object
