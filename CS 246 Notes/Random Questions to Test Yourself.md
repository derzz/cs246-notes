```cpp
struct Node{
	int val;
	Node* nextNode;
}
```
- Create the Constructor for a node using the above nodes as a template.
- Create the destructor for a node
- Create the copy and move constructor for a node
- Create the copy assignment and move assignment operator for a node
- Using the spaceship operator, make a comparison that detects if a node is greater or less than the other. If both numbers are equal, check to see which list is longer or shorter than the latter.
- Define encapsulation and using structs and classes, define a pro and con of using one over the other.
- Create an iterator to traverse a given linked list. Include the operations `++`, `!=`, and `*`(dereference
	- After, create a main function, given a linked list `a`, using the iterator, output the values of `a`.
- Will the code below work? If so, explain the process and explain its output. If not, explain why not and a fix for it. Do not change the main function if a fix is needed.
```cpp
#include <iostream>

using namespace std;

class Box
{
    double width;

public:
    void printWidth( Box box );
    void setWidth( double wid );
};

void Box::setWidth( double wid )
{
    width = wid;
}

void printWidth( Box box )
{
    cout << "Width of box : " << box.width <<endl;
}

// Main function for the program
int main()
{
    Box box;

    box.setWidth(10.0);

    printWidth( box );

    return 0;
}
```
- Create a UML diagram for the following class:
```cpp
class Vec{
	int x;
	int y;

	int getY(){return y;}
	int yolo() = 0;

	public:
		int getX(){return x;}
};
```
- What are the steps of object creation?
- Explain the differences between composition, aggregation, and inheritance with a UML diagram
- What's the difference between public, protected, and private inheritance?
- What are virtual functions and object slicing?
- What are the steps of object destruction?
- The below code causes a memory leak, identify it and fix it.
```cpp
class X{
	int* n;
	public:
		X(int size): n{new int[size]} {}
		~X(){delete[] n;}
};

class Y: public X{
	int* m;
	public:
		Y(int size1, int size2): X{size1}, m{new int[size2]}{}
		~Y(){delete[] m;}
}

X xObj{5};
Y yObj{5, 10};
X* xp = new X{5};
Y* yp = new Y{5, 10};
X* xpToY = new Y{5, 10};
delete xp;
delete yp;
delete xpToY;
```
- What does `final` do?
- Write a template for a linked list, where the programmer can use any type in the list. Do not implement any of the functions. Write out the structure for a node inside the template and provide a function for `ith` that takes an integer `i` and outputs the value at the `i` th node. Furthermore, add a function called `addToFront` that adds a node to the front of the linked list.
- Assuming `v` is a vector, why won't the below delete the whole vector as expected? Augment it so it works.
```cpp
for(auto it = v.begin(); it != v.end(); ++it){
	if(*it == 5) v.erase(it);
}
```
- What vector function will put a value back at the end of the vector? What function will remove the last element in the vector? What will be in `vector<int> v(4, 5); `?
- Create a decorator and implement the missing portions for Pizza given the following UML Diagram:
```cpp
class Pizza{
public:
	virtual float price() const = 0;
	virtual string desc() const = 0;
	virtual ~Pizza(){
		
	}
};

class CrustAndSauce: public Pizza{
	public:
		float price() const override{return 7.99;}
		string desc() const override{return "pizza";}
};
```

```plantuml
abstract class Pizza
class CrustandSauce
abstract class decorator

Pizza <|-- CrustandSauce
Pizza <|-- decorator
Pizza --* decorator

class topping{
	+ price() float
	+ desc() string
}

class StuffedCrust
class DippingSauce

decorator <|--topping
decorator <|-- StuffedCrust
decorator <|-- DippingSauce

```
- What is RAII and why is it important in C++?
- What are the basic, strong, and no throw guarantee in exception safety? Taking a look at below's code, what type of guarantee is this? If it's a `basic` guarantee, how can we augment it so that it is strong or no throw? Assume every line in `f` has a chance to throw:
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
- What happens when you `throw` without a try catch?
- What's wrong with the below code? What will happen with this code?
```cpp
struct CImpl{
	int a;
	int b;	
}

class C{
	unique_ptr<CImpl> pImpl;
	void f(){
		auto temp = make_unique<CImpl>(*pImpl);
		CImpl* cPtr = temp.get();
		delete cPtr;
		cout << temp->a << endl;
	}
}
```
- Define coupling and cohesion and fill in the blanks and explain your reasoning:
	- As programmers, we want "__ coupling, __ cohesion".
- What is `variant` and how do we use it? What does `holds_alternative` do? Provide an example of variant being used between an integer and double.
- Why do virtual methods take up more memory than regular methods?
- Will the following code compile and run? If not, why won't it, if so, what will it output?
```cpp
struct A{int a;};
struct B: private A {int b;};
struct C: public A{int c;};

struct D: public B, public C{
	int d;
};

D dobject;
cout << dobject.a << endl;
```
- What is the deadly diamond of death? Draw a UML to showcase this concept.
- Write a template function for a `max` function which takes the biggest of two objects.