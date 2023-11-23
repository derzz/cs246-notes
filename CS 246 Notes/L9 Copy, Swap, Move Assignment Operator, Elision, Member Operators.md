# Copy/Swap Idiom: Another Method to Implement Copy Assignment
```cpp
Node* n = new Node{1, new Node{2, new Node{3, nullptr}}};
Node p{4, new Node{5, nullptr}};
p = *n; // Writing copy assignment operator
```
- `std::swap`: `<utility>` library
	- Swap (a, b): a gets b's value, b gets a's value
```cpp
Struct Node{
	void Swap(Node& other){
		swap(data, other.data);
		swap(next, other.next);
	}
	Node& operator=(const Node& other){
		Node tmp{other};
		Swap(tmp);
		return *this;
	}
}
```

^968578
![[Pasted image 20231005114225.png]]
1. Temp is made as a copy of p
2. The first node of `p` is swapped with first node of `tmp`
3. `p`'s first node now swaps to `tmp`'s second node and `tmp`'s first node now swap to `p`'s second node, successfully causing the swap
4. Upon copy constructor's completion, `tmp` gets deleted and `p` has successfully been made as a copy of `n`
# Move Constructor and Move Assignment Operator
- Recall: References, as we have seen them are "`lvalue`" references
	- `lvalue` references do not bind to temporary values
```cpp
Node oddsOrEvens(){
	Node o{l, new Node{3, new Node{5, ..., newNode{99, nullptr}}}}
	Node e{2, new Node{4, new Node{6, ..., newNode{100, nullptr}}}}
	// Give odd or even linked list if user is inputting 'o'
	char c;
	cin >> c;
	return(c == 'o') ? o : e;
}

Node l = oddsOrEvens(); // Return by Value, copy values from functions into field l
// Copy from oddsOrEvens stack frame into l
```
## What the Copy Looks like(Inefficient)

- Aka, we just copied 50 nodes, then deleted originals! What if we instead reused them?
	- `o` gets deleted as the function goes out of scope
- This is only allowed if we are certain no one else will be using the originals!
```cpp
Node n;
Node p{n}; // We must make a copy of n
// We may want to use n and p simultaneously

Node l = oddsOrEvens(); // Temporary value, no one else can use this value! Can reuse the data instead of copying
```
- We must determine- are we working with a long lasting value(`lvalue`)
	- Must deep copy
- Or, are we using a temporary- then, we can reuse their data
- `Node &&` - `rvalue` reference, can bind to temporary values, compiler reserves a temporary memory address to put the value in
## Move Constructor
```cpp
Struct Node{
	Node(Node&& other): data{other.data}, next{other.next}{
		other.next = nullptr; // If this is not put, destructor will run and you can't steal data!
	}
}
```

^61082c
![[moveConstructor.png]]
- As soon as this function dies, `o` will die, so steal `o's` nodes :)
## Move Assignment Operator
```cpp
Node n{200, new Node{300, new Node{400, nullptr}}}
n = oddsOrEvens(); // Temporary, reuse the values instead than copying

// Move Assignment Operator
Struct Node{
	Node& operator = (Node&& other){
		swap(other); // Swap
		return *this // Delete the swapped and successfully moved the node
	}
}
```

^3ebeca
![[moveAssignmentOperator.png]]
- If `other` is a temporary `rvalue`, call move constructor/move assignment operator instead of copy versions
	- If you only write the copy constructor or the copy assignment operator, these will always be used
## Rule of Big 5(Suggestion)
- If you write one of the destructor, copy constructor, copy assignment operator, move constructor, move assignment operator, you probably should write them all
- Do not reinvent the wheel- use compiler provided version if possible
	- Big 5 is usually necessary for classes that manage a resource(like memory)
# Elision
```cpp
vec getVec(){return{0,0};}
vec v = getVec(); // Run one constructor
```
- The basic constructor was run! No move!
- This occurs due to move/copy elision!
- Rather than making a `vec` in `getVec` and moving it into main- compiler writes value directly into main itself
- Compiler can provide this feature, even if it changes program output
## Another Example
```cpp
void useVec(vec v){...}
useVec({1, 2});
```
- `vec{1, 2}` is written directly into v, not constructed and moved
- We do not expect you to know all the places it may occur, just that it's possible
# Member Operators
- `vec operator+` was previously written as a standalone function, but operator `=` was a method
	- Difference?
```cpp
Struct vec{
	int x, y;
	vec operator+(const vec& other){
		return {x + other.x, y+ other.y}
	}
}
```
- Example: If we do `v+w`, then `v` points to LHS and `w` points to `other`
```cpp
vec operator*(int k){ // Works for v*5 but not 5*v
	return {x*k, y*k};
}

5 * v // Write as a standalone like before
```
- Advice: For arithmetic assignment, reuse logic from arithmetic overloads
```cpp
vec& operator+= (vec& v1, const vec& v2){ // Copies, pass by value
	v1.x += v2.x;
	v1.y += v2.y;
	return v1; // Standalone function
}

vec operator+(vec v1, const vec& v2){
	v1 += v2;
	return v1;
}
```
## Mistake
```cpp
Struct vec{
	ostream& operator << (ostream& out){
		return out << x << "" << y;
	}
};
```
- LHS is expected to be a vector:
	- So `v << cout` needs to be written, not intuitive!
	- I/O operators should be standalone functions
## Operator Overloads Must be Methods
- `operator=`
- `operator[]`
- `operator->`
- `operator()`
- `operator T` where T is a type