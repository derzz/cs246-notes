
```cpp
struct Node{
	int data;
	Node* next,
	Node(const Node & other): data{other.data}, next{other.next? new Node{*other.next*}: nullptr}{}
};
```
# Common Mistake in Copy Constructor
- Why not `Node(Node other)`, they try to do pass by value
	- Invokes the copy constructor and will perform infinite series of recursion and will keep trying to copy the arguments instead
	- Infinite recursion
- You must be careful when writing single argument constructors!
	- They allow implicit conversions!
```cpp
struct Node{
	Node(int data, Node* next=nullptr): data{data}, next{next}{}
};

Node n{4};
node m = 4;

void f(Node n);
f(4); // Implicity calls the Node Constructor
```

```cpp
String s = "Hello"
```
- Left side is `std::string`
- Right side is a `const char *`
	- This is allowed, a single argument constructor for strings take in a `const char *`
- Implicit Conversions can be dangerous
	- `int -> Node implicitly is unintuitive`
	- Conversion is silent, no compiler warning
	- Potential for errors
# Explicit Constructor
- We can avoid implicit conversions by stating `explicit`
```cpp
Struct Node{
	explicit Node(int data, Node* next=nullptr): data{data}, next{next}{}
}

Node n{4}
Node m = 4 // Won't compile since implicit conversion has been disabled

void f(Node n);
f(4) // Won't compile, what you would expect
f(Node{4}) // Need to be explicit and would work :)
```
# Destructor(`dtors`)
- Destructor is a special method that runs when an object is destroyed!
	- Stack allocation: Goes out of scope
	- Heap allocation: Delete is called on a pointer to the object
- Compiler provided destructor:
	- Destructor is called on all object fields
## Steps of Object Destruction
1. Destructor body runs
2. Destructor runs for all object fields. In reverse declaration order
3. *later*
4. Space is deallocated
## Reasons for Writing a Destructor
```cpp
Node* p = new Node{1, newNode{2, new Node{}, nullptr}};
```
- If we do not call `delete p`, only p's memory is reclaimed on the stack, and three nodes will be leaked!
- ![[Pasted image 20231003115810.png]]
	- Nodes 2 and 3 are still leaked with compiler provided destructor
## Destructor Example
```cpp
struct Node{
// writing destructor
	~Node(){delete next;} // delete on a null pointer is safe
	// Once it reaches the last node, it goes back to the previous node and deletes that, and recurse
};
```
- `delete` will recursively call destructor
```cpp
Nodes s{4, new Node{5, nullptr}}; // cpp allows us to clean everything when it goes out of scope
```
# Copy Assignment Operator
```cpp
Student s{60, 70, 80};
Student t = s; // Copy Constructor
Student r{100, 100, 100};

t = r;
```
- `t` already exists, so we don't invoke the copy operator, but the **copy assignment operator**
- Copy assignment operator runs when we assign a value that already exists to an object of the same type
```cpp
Node n {1, new Node{2, new Node{3, nullptr}}};
Node p{4, new Node{5, nullptr}};
p = n;
```
![[Pasted image 20231003122151.png]]

## Creating the Copy Assignment Operator
```cpp
struct Node{
	Node& operator = (const Node& other){
		delete next; // removes current memory and delete every single node
		data = other.data;
		next = other.next? new Node{*other.next}: nullptr; // Copying values
		return *this;
	}
};
```
- Same code for copy assignment operator and copy operator is a mistake!
- Wrong copy assignment operator!
	- In the case of `n = n;`, it should do nothing but starts doing self assignment
	- It will delete itself, and doesn't have anything to copy to!
		- Crash! It will try to copy nodes that you just deleted!

## Self Assignments
```cpp
Node& operator = (const Node& other){
	if(this == &other) return *this;
	delete next;
	data = other.data;
	next = other.next? new Node{*other next}: nullptr;
	return*this;
}
```

^0f2b83
## Better Copy Assignment Operator
- One more improvement- request memory first before destroying the old data
```cpp
Node& operator = (const Node& other){
	if(this == &other) return *this;
	Node* tmp = other.next?new Node{*other.next}: nullptr;
	delete next;
	data = other.data;
	next = tmp;
	return *this;
}
```

^f3514b
