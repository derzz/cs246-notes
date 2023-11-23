```cpp
int* arr = malloc(n*sizeof(int))
```

- In C++, we create arrays on the heap:

```cpp
int* arr = new int[10];
delete[] arr;
```

- Always pair new/delete and new[] with delete[]- mix and math leads to undefined behaviour
## Returning Values
- Differences between return-by-value, return-by-pointer, return-by-reference
```cpp
Node getMeANode(int x){
  Node n {x, nullptr};
  return n;
} // Copy done from getMeANode's frame into caller's frame

Node* getNodePtr(int x){
  Node n{x, nullptr};
  return &n; // Crashes at runtime if caller uses return value
}
  ```

- n is destroyed when function ends, returning a pointer to memory we no longer own
- Fix: Use heap allocation- memory live past end of function call
  ```cpp
Node* getNodePtr(int x){
  Node* p = new Node{x, nullptr};
  return p;
}
  ```
  - ![](https://cdn.discordapp.com/attachments/966496028469624875/1154444858610696204/image.png)
  
  - Fast, works, BUT user must remember to delete the pointer eventually, or else a memory leak occurs
### Return by Reference:
 ```cpp
Node& getNodeRef(int x){
	Node n {x, nullptr}; // Dangling refrence, n is destroyed on return
	return n;
}```
  
- Return by reference- rare, and only used when returning non local values
```cpp
  istream& operator >> (istream& in, int& x){
	  // Read from in into x
	  return in; // Not dangling, in is not local
  }
```
- Advice: Use return by value for most scenarios
	- Usually, compiler can optimize the copy away from the return(epsilon, move semantics)
### Operator Overloading

```cpp
struct vec{
  int x, y;
}

vec x{1, 2};
vec y{3, 4};
vec z = x + y; // We want to add this to be {4, 6}

vec operator+(const vec& v1, const vec& v2){
  vec r{
	  v1.x + v2.x, v1.y + v2.y
  }
  return r;
}
```
  - Type has to be one of your own structs
```cpp
// Scalar multiplication 
vec v = {1, 2};
vec w = 2 * v;

vec operator*(int k, const vec &v){
  return{k*v.x, k*v.y}
} // Could write 'vec' but is assumed in the return type
```
- Note `vec w = v*2;` , the arguments don't line up so we must write another operator like:
```cpp
vec w = v*2;
vec operator*(const vec& v, int k){
	reutrn k * v; // Calls the original function
}
    ```
## Overloading `>>` and `<<` operators

```cpp
struct Grade{
  int n;
};

Grade g{100};
cout << g;
Grade g2;
cin >> g2;

ostream& operator <<(ostream& out, const Grade& g){
  return out << g.n << '%';
}
  ```

```cpp
Grade g{25};
ofstream file{"grades.txt"};
file << g; // This works! ofstream is a type of oStream and would work!
  ```

```cpp
istream& operator >> istream(istream&in, Grade& g){
  in >> g.n;
  if(g.n < 0) g.n = 0;
  if(g.n > 100) g.n = 100;
  return in;
}
  ```

### Separate Compilation
- Large programs can take a long time to compile
  - Separate compilation avoids issues like these
- Speeds up compilation by only recompiling what is necessary/what is changed
### Interface Files
- Interface Files: `think.h`- Provide declarations for functions
- Implementation Files: (`:c files`)- Provide definitions of functions written

```cpp
// Interface file: Vec.cc
export module vec; // This is an interface file
export struct vec{ // Prefixed with export => Available to clients(importers)
	int x, y;
};
export vec operator+(const vec& v1, const vec&v2);

// Client Code(main.cc)
import vec;
int MediaDeviceInfo(){
	vec v{1, 2};
	vec w = v + v;
}
  ```

 ```cpp
// Implementation File(vec- impl.cc)
module vec; // Implementation file
vec operator+(const vec& v1, const vec& v2){
	return{v1.x + v2.x, v1.y + v2.y}
}
```
