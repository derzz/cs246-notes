Note: Answers may not be correct.
- Create the Constructor for a linked list using the above nodes as a template.
```cpp
Node(int val, Node* nextNode): val{val}, nextNode{nextNode}{}
```
- Create the destructor for a linked list
```cpp
~Node(){
	delete nextNode;
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
	- Encapsulation is the act of "hiding" data or functions from other classes or the user. This can be done via `private` fields in structures and classes have this enabled by default. Structures' fields will always be public by default and is useful when we need to take data like with nodes. Classes automatically encapsulate non specified fields(invariants). This increases abstraction and can ensure that the user only uses public methods to manipulate these variables. Take below:
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

- Create an iterator to traverse a given linked list. Include the operations `++`, `!=`, and `*`(dereference). After, create a main function, given a linked list `a`, using the iterator, output the values of `a`.- 

```cpp
class LinkedList{
	Node* head;
	public:
		class Iterator{
			Node* head;

			Iterator(Node* cur), head{cur}{}

			Iterator& operator++(){
				head = head->next;
				return *this;
			}

			bool operator!=(const Iterator& other) const{
				return head != other->head;
			}

			int operator*() const{
				return head->val;
			}
		
		};

	Iterator begin() const{
		return Iterator{head};
	}

	Iterator end() const{
		return Iterator{nullptr};
	}
};

int main(){
	LinkedList l = new LinkedList{new Node(5, new Node(6, new Node, (7, nullptr)))};
	for(int n: l) cout << n << endl;
	// Another valid way
	for(LinkedList::Iterator it=l.begin(); it != l.end(); ++it){
		cout << *it << endl;	
	}
}
```
- Will the code below work? If so, explain the process and explain its output. If not, explain why not and a fix for it. Do not change the main function if a fix is needed.
	- This code will not work. `void printWidth( Box box );` is not defined as a function in Box. Since we can't augment main, we can not make `void printWidth` as `void Box::printWidth`, as main will be unable to call it. Instead, make `void printWidth(Box box)` into `friend void printWidth(Box box)` in the header to allow printWidth to access Box's private variables. 
- Create a UML for the following class:
```plantuml
abstract class Vec{
	-x: Int
	-y: Int
	- {abstract} fees(): Integer
	# {abstract} imProtected(): Integer
	-getY(): Int
	+getX(): Int
}
```
(Red boxes mean `-` and green circle means `+`, yellow diamond means `#`, remember that `Vec` needs to have `**` or be italicized!
- What are the steps of Object Creation?
	1. Space is allocated
	2. Superclass constructor is run
	3. MIL is initialized for class constructors
	4. Constructor body runs
- Explain the differences between composition, aggregation, and inheritance with a UML diagram
	- Composition is a 'own-a' relationship between the superclass and subclass. The superclass will take full control of any subclass objects and whenever the superclass gets deleted, the subclasses gets deleted as a result.
	- Aggregation is 'has-a' relationship and the superclass does not fully own a subclass object. Whenever the superclass gets deleted, the subclasses remain. A good example is when a superclass has access to a pointer from another class.
	- Inheritance 'is -a' relationship. It is able to inherit any fields from the superclass but cannot access any private fields/methods from the superclass. However, it can access `protected` fields/methods from the superclass.
	- The UMLs are left as an exercise for the reader(TLDR, In order: Black diamond, empty diamond, empty arrow)
- What's the difference between public, protected, and private inheritance?
	- The code below best explains it:
```cpp
class A 
{
    public:
       int x;
    protected:
       int y;
    private:
       int z;
};

class B : public A
{
    // x is public
    // y is protected
    // z is not accessible from B
};

class C : protected A
{
    // x is protected
    // y is protected
    // z is not accessible from C
};

class D : private A    // 'private' is default for classes
{
    // x is private
    // y is private
    // z is not accessible from D
};
```
- What are virtual functions and object slicing?
	- Virtual functions are functions that can be overwritten by any inherited class. It is useful in polymorphism by changing one type to another. However, object slicing may occur, which takes the subclass' fields and methods and ignores some fields. Take the following example and pay attention to `b.isHeavy()`
```cpp
class Book{
	protected:
		string title, author;
		int length;
	public:
		Book(string title, string author, int length): title{title}, author{author}, length{length}{} 
		bool isHeavy() const{
			return length > 200;
		}
};

class Text: public Book{
	string topic;
	public:
		Text(string title, string author, int length): title{title}, author{author}, length{length}, topic{topic}{} 
		bool isHeavy() const{ // Overwriting isHeavy()
			return length > 500;
		}
};

Book b {"", "", 300};
Text t{"", "", 300, "topic"};
b.isHeavy(); // true since length > 200
t.isHeavy(); // false since length is not greater than 500
Book b = Text{"", "", 300, "topic"}; // Allowed due to public inheritance between text and books
b.isHeavy() // Runs Book, not Text as isHeavy() in Book is not virtual.
```
By changing `isHeavy()` like so:, 
```cpp
class Book{
	...
	public:
		virtual bool isHeavy() const{return length > 200;}
};

class Text: public Book{
...
public:
	bool isHeavy() const override {return length > 500;}
};
```
`b.isHeavy()` will run Text's `isHeavy()`.
- What are the steps of Object Destruction?
	1. Destructor body runs
	2. Object fields that have destructors are called in reverse declaration order
	3. Superclass destructor runs
	4. Memory is freed
- The below code causes a memory leak, identify it and fix it.
	- Since the `X` destructor is non virtual, when doing polymorphism on `xpToY` and `delete xpToY`, `X`'s destructor runs and not `Y`'s. This causes a memory leak as the correct object destructor is not run. To fix this, make the destructors virtual like so to run `Y`'s destructor(which runs `X`'s destructor too!):
```cpp
virtual ~X(){delete[] n;}
~Y() override{delete[] m;}
```
- What does `final` do?
	- `final` stops a function from being overwritten. When used in a class, `final` doesn't allow anyone to inherit from it. Take below for some examples:
```cpp
class Base {
public:
    virtual void foo() final;
};

class derive: public Base{
	public:
		void foo() override; // Causes an error!
}

class Base2 final{
	int x;
}

class derive2: public Base2{ // Error!
	int y;
}
```
- Write a template for a linked list, where the programmer can use any type in the list. Do not implement any of the functions. Write out the structure for a node inside the template and provide a function for `ith` that takes an integer `i` and outputs the value at the `i` th node. Furthermore, add a function called `addToFront` that adds a node to the front of the linked list.
```cpp
template<typename T> class LinkedList{
	struct Node{
		T data;
		Node* next;
	};	
	Node* head;
	public:
		class Iterator{
			...
			T& operator*() const;
		};
		T ith(int i) const;
		void addToFront(const T& n);
};
```
- Assuming `v` is a vector, why won't the below delete all values 5 as expected? Augment it so it works.
	- Assume the vector has `[5, 5]`. When deleting the first 5, it successfully deletes it but it automatically moves `it` up by 1 so the vector will now have `[5]`. See below for a correct implementation:
```cpp
for(auto it = v.begin(); it != v.end();){
	v.erase(it);
	if(*it == 5) v.erase(it);
	else it++;
}
```
- What vector function will put a value back at the end of the vector? What function will remove the last element in the vector? What will be in `vector<int> v(4, 5); `?
	- `emplace_back()`, `pop_back()`, and `[5, 5, 5, 5]`
- Create a decorator and implement the missing portions for Pizza given the following UML Diagram:
	- Answers may vary.
```cpp
class Decorator: public Pizza{
	protected:
		Pizza* next;
	public:
		Decorator(Pizza* p): next{p}{};
		~Decorator(){delete next;}
};

class Topping: public Decorator{
	string name;
	public:
		Topping(string name, Pizza *p): Decorator{p}, name{name}{}

	float price() const override{
		return 0.99 + next->price();
	}
	
	string desc() const override{
		return next->desc() + " with " + name;
	}
};

class stuffedCrust: public Decorator{
	public:
		StuffedCrust(Pizza* p): Decorator{p}{}
		float price() const override{
			return 1.50 + next ->price();
		}
		string desc()const override{
			return next->desc() + " with stuffed crust ";
		}
};
```
- What is RAII and why is it important in C++?
	- It stands for Resource Allocation is Initalized(great name btw). It's a programming idiom used in C++ where resources are tied to object lifetimes: resource allocation (acquisition) is done during object creation (initialization), by the constructor, and resource deallocation is done during object destruction, by the destructor. RAII is important in C++ for the following reasons:
		1. **Memory Management**: It helps in avoiding memory leaks. When an object goes out of scope, its destructor is called which can be programmed to free the allocated memory, ensuring that there are no memory leaks.
		2. **Exception Safety**: It provides exception safety. If an exception is thrown, the stack unwinds, and all the objects in the stack will have their destructors called, releasing all resources owned by them.
		3. **Resource Management**: It's not just about memory, but any resource that needs deterministic cleanup: file handles, network sockets, database connections, mutex locks, etc.
		4. **Code Readability and Maintenance**: It makes the code easier to read and maintain. Resource management logic is tied to the object, which is more intuitive and less error-prone than manually controlling resource lifetimes.
- What are the basic, strong, and no throw guarantee in exception safety? Taking a look at below's code, what type of guarantee is this? If it's a `basic` guarantee, how can we augment it so that it is strong or no throw? Assume every line in `f` has a chance to throw:
	- Basic guarantee: If an exception is thrown from a function `f`, the program is in a valid but undefined state.
	- Strong guarantee: If an exception is thrown, then the program state is reverted to below `f`'s call. 
	- No throw guarantee: `f` will never throw and the program is reverted before `f`'s call.
```cpp
// Below is not the best example as it seems strong guarantee, but:
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
			b = btemp; // Let's say b = btemp fails! Then, a is in an undefined state.
		}
}
```
A possible change can be:
```cpp
struct CImpl{
	A a;
	B b;	
}

class C{
	unique_ptr<CImpl> pImpl;
	void f(){
		auto temp = make_unique<CImpl>(*pImpl);
		temp->a.g(); // If throw, nothing changes.
		temp->b.h(); // Same above.
		std::swap(temp, pImpl); // Can't throw
	}
}
```
- What happens when you `throw` without a try catch?
	- It stops the program and throws whatever exception you input.
- What's wrong with the below code? What will happen with this code?
	- By deleting `cptr`, the unique pointer doesn't know that the object has been deleted. Thus `temp->a` provides undefined behaviour and can lead to a crash.
- Define coupling and cohesion and fill in the blanks and explain your reasoning:
	- As programmers, we want "Low coupling, High cohesion".
	- Coupling refers to the degree to which one class knows about another class. If one class uses the other class directly, they are said to be tightly coupled. Low coupling is desirable because it reduces the interdependency between classes, making the system easier to modify.
	- Cohesion refers to how closely the responsibilities of a module or class are related to each other. High cohesion means that each module or class is assigned a single well-defined task or responsibility. High cohesion is desirable because it increases the readability and maintainability of the code, and makes the system easier to understand and debug.
- What is `variant` and how do we use it? What does `holds_alternative` do? Provide an example of variant being used between an integer and double using `holds_alternative`.
	- Variant acts as a type safe union between the types provided in it. We first `#include <variant>` to use it.  `holds_alternative` checks if the variant holds the specified alternative type.
```cpp
variant<int, double> v;
v = 10; // v contains int

if (std::holds_alternative<int>(v)) {
	std::cout << "v holds int\n";
}

v = 5.5; // v contains double

if (std::holds_alternative<double>(v)) {
	std::cout << "v holds double\n";
}
```
- Why do virtual methods take up more memory than regular methods?
	- Virtual methods in C++ require more memory than non-virtual methods because of the additional "vtable" (virtual table) and "vptr" (virtual pointer) that are used to support dynamic polymorphism. 
	- Each class that has at least one virtual function has its own vtable. This table is a static array that the compiler creates for you, and it contains one entry for each virtual function that can be called by objects of the class. Each entry is simply a function pointer that points to the most-derived function accessible by the class.
	- In addition, each object instance of a class with virtual functions also has a vptr, which is a pointer to the vtable. This vptr is added to the object's memory footprint. 
- Will the following code compile and run? If not, why won't it?
	- It will not. `B` is inheriting A privately so `D` cannot access `B`'s  `a` as it's now private for `D`. This is similar to the diamond problem BUT the reason it doesn't compile is not because of multiple inheritance. 
- What is the deadly diamond of death? Draw a UML to showcase this concept. Provide a solution for this problem.
	- `D` will have two copies of `A`'s members, making it ambiguous if it uses B's `A` or C's `A`. To solve this problem, you want to use `virtual` inheritance to solve this issue like so:
```plantuml
A <|-- B
B <|-- D
A <|-- C
C <|-- D
```
```cpp
class A {
public:
    void foo();
};

class B : virtual public A {
};

class C : virtual public A {
};

class D : public B, public C {
};
```
Or just don't use it...
- Write a template function for a `max` function which takes the biggest of two objects.
```cpp
template<typename T> T max(x, y){
	return x > y? x:y;
}
```
- Assuming `Text` is a subclass of `Book`, what is wrong with this move constructor and how do we fix it?
	- This move constructor is not moving the values of `other` to `this`. We need to convert `other` to an `rvalue` by using `std::move` like so:
```cpp
Text:: Text(Text&& other): Book{std::move(other)}, topic{std::move(other.topic)}{}
```
- Answer the following questions as true or false. Provide reasoning for any response:
	- An abstract superclass can have an abstract subclass 
		- True, the subclasses will be abstract though and cannot be called by themselves.
	- In public inheritance, the subclass has access to the superclass’s private members 
		- False, private members will always be private in any type of inheritance.
	- It is OK to override non-virtual methods
		- False, you can but you shouldn't. When you override, the compiler will not read the function that has been overrided but instead just read the base class method like so:
```cpp
class Base {
public:
    void nonVirtualMethod() {
        std::cout << "Base::nonVirtualMethod()" << std::endl;
    }
};

class Derived : public Base {
public:
    void nonVirtualMethod() override {
        std::cout << "Derived::nonVirtualMethod()" << std::endl;
    }
};

// Example usage
Base* ptr = new Derived();
ptr->nonVirtualMethod();  // Calls Base::nonVirtualMethod()
```
