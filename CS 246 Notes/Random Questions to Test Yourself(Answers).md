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
- Using the spaceship operator, make a program that calculates whether a Linked List is longer than the other.
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