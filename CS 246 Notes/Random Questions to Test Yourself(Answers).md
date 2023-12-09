Note: Answers may not be correct.
- Create the Constructor for a linked list using the above nodes as a template.
```cpp
Node(int val, Node* nextNode): val{val}, nextNode{nextNode}{}
```
- Create the destructor for a linked list
```cpp
~Node(){
	delete *nextNode;
}
```
- Create the copy and move constructor for a linked list
```cpp
// Copy
Node(const Node& copy): val{copy.val}, next{copy.next ? new Node{*(copy.next)}: nullptr}{}

// Move
Node(Node&& move): val{move.val}, next{move.next}{
	move.next = nullptr;
}
```
- Create the copy assignment and move assignment operator for a linked list
```cpp
Node& operator=(const Node& copy){
	if(this == &copy) return *this;
	delete next;
	val = other.val;
	next = copy.next ? new Node{*other.next}: nullptr;
	return *this;
}

// With swap
Node& operator=(Node &&move){
	swap(move);
	return *this;
}

// Without swap
Node& operator=(Node &&move){
	val = move.val;
	Node* temp = move.next;
	move.next = next;
	next = temp;
	temp = nullptr;
	return *this;
}
```
- Using the spaceship operator, make a program that calculates whether a Linked List is longer than the other.
```cpp
auto Node::operator<=>(Node &other){
	auto n = data <=> other.data;
	if(n != 0 || !next && !other.next) return n;
	if(!next && other.next) return strong_ordering:less;
	if(next && !other.next) return strong_ordering:greater;
	return *next <=> *other.next;
}
```
- Define encapsulation and using structs and classes, define a pro and con of using one over the other.
	- Encapsulation is the act of "hiding" data or functions from other classes or the user. This can be done via `private` fields in structures and classes have this enabled by default. Structures' fields will always be public by default and is useful when we need to take data like with nodes. Classes automatically encapsulate non specified fields, usually as variables that the programmer doesn't want the user to touch. This increases abstraction and can ensure that the user only uses public methods to manipulate these variables. Take below:
```cpp
struct Node{
	int val;
	Node* nextNode;
}
```
The user and other classes can access these parameters freely. However:
```cpp
class Node{
	int val;
	Node* nextNode;
}
```
The user or other classes cannot access `val` or `nextNode` freely as the compiler will provide an error. 

Create an iterator to traverse a given linked list. Include the operations `++`, `!=`, and `*`(dereference). After, create a main function, given a linked list `a`, using the iterator, output the values of `a`.

```cpp
class LinkedList{
	Node* head;
	public:
		class Iterator{
			Node* head;
			Iterator& operator++(){
				head = head.next;
				return *this;
			}

			Iterator& operator!=(LinkedList& other){
				return head->val != other.head->val;
			}

			int& operator*(){
				return *val;
			}
		
		};

	Iterator
};
```

- `void printWidth( Box box );` is not defined as a function in Box. Since we can't augment main, we can not make `void printWidth` as `void Box::printWidth`, as main will be unable to call it. Instead, make `void printWidth(Box box)` into `friend void printWidth(Box box)` in the header to allow printWidth to access Box's private variables. 
- Steps of Object Creation
	1. Space is allocated
	2. Superclass constructor is run
	3. MIL is initialized for class constructors
	4. Constructor body runs
- Steps of Object Destruction
	1. Destructor body runs
	2. Object fields that have destructors are called in reverse declaration order
	3. Superclass destructor runs
	4. Memory is freed
- 