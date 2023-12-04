# References
- C++ has another pointer like type called a **reference**
```cpp
// Fixed version
void inc(int* x){
  ++*x;
}

int main(){
  int z = 5;
  inc(&z);
  cout << z << endl; // 6
}
  ```
- Z is an "l value reference to X"
	- `lValue`: Something we can take the address of
```cpp
int& z = 5; // Not allowed to compile
++z; // Are you changing the value of all 5s or the current 5?
```
- Z is an alias for x- any place we see Z, we can replace it with X in our minds
	- Any changes to Z are reflected in X
- References are like `const` pointers with automatic dereferencing
- How can we tell `&` means reference vs. address of?
	- If `&` appears in a type -> Reference
	- If `&` appears in an expression -> Address of operator
  
```cpp
void f(int& n) // int& is a type
g(&x); // expression, address of operator
    ```
### Limitations of References
- Cannot be left uninitialized!
	- `int& z; // Won't compile! Not initalized to a variable!`
- Cannot assign temporary to an `lvalue` reference
	- `lvalue` references may only be initialized to `lvalues`
```cpp
int& z = x + y;
int& z = f(); // Also illegal, there are "rvalues, temporary, do not have long term memory location"
```
- Cannot create a pointer to a reference
```cpp
// Read right to left!
int*& z; // A reference to a pointer to an integer, allowed!
int&* x; // A pointer to a reference to an integer, not allowed!
```
- Cannot create a reference to a reference
	- It has no value and the syntax is reserved for something else
```cpp
int&& z = x; // Not allowed, means something different - "rvalue reference"
```
- Cannot create an array of references
```cpp
int& arr[3] = {x, y, z}; // Won't compile, not allowed
```
### What are References Useful For?
- Most commonly used in function arguments(`args`)
```cpp
void inc(int* p){ ++*p; }

int main(){
	x = 5;
	inc(&x);
}

void inc(int& x){++x;}

int main(){
	x = 5; // Changes are reflected here
	inc(x);
}
```
- `getline(cin, 5)` works because 5 is passed by reference
-  `cin >> x` works like so:
	- `cin` is a function call
```cpp
istream* operator >> (istream& in, int& x){
	//Read from in int X
	return in;
}
```
  - `istream&` is the return type
  - `operator` is the name of the function
  - `istream& in, int& x` are the arguments
	- `x` needs to be taken by reference
### Costs of Parameter Passing
- Why do we take in and return `istream&` instead of just `istream`?
- Costs of various parameter passing:
```cpp
struct ReallyBig{...};

void f(ReallyBig rb);
void g(ReallyBig& rb);
void h(const ReallyBig& rb); // Can't manipulate rb
```
- `f` Pass by value: Copy of `rb` is made from caller's frame into `f`'s frame(slow) 
- `g`: No copy is made and is fast!
- `h`: No copy- fast, easy to reason about as `rb` will not change
# Dynamic Memory

- In C:
```c
int* arr = malloc(n * sizeof(int));
...
free(arr); // Returns memory back to the OS
```
- In C++: Use `new/delete`
```cpp
Node* p = new Node{5, nullptr};
...
delete p; // Frees memory given to the OS
```
![](https://cdn.discordapp.com/attachments/966496028469624875/1153732733940551710/image.png)
  
- All local variables are stored on the stack  
	- Stack allocated variables are deallocated when they go out of scope
	- When the above function ends, `p` is free from the stack
	- Memory allocated with `new` is on the heap
	- Heap allocated memory persists until `delete` is called
	  - Failure to delete leads to a memory leak and the program will eventually cras