# Big 5
```cpp
struct Node{
	int val;
	Node* nextNode;
}
```
- Create the Constructor for a linked list using the above nodes as a template.
- Create the destructor for a linked list
- Create the copy and move constructor for a linked list
- Create the copy assignment and move assignment operator for a linked list
- Using the spaceship operator, make a program that calculates whether a Linked List is longer than the other.

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

    // set box width without member function
    box.setWidth(10.0);

    // Use friend function to print the wdith.
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

	public:
		int getX(){return x;}
};
```
- What are the steps of object creation?
- Explain the differences between composition, aggregation, and inheritance with a UML diagram
- What are virtual functions and use an example of object slicing to explain their usage.
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
- Given the following code:
```cpp
class Student{
	public:
		virtual int fees() const = 0;
};

class Regular: public Student{
	public:
		int fees() const override;
};

class Coop: public Student{
	public:
		int fees() const override;
};

Student* s = new Student();
Regular r = Regular();
Coop* c = new Coop();
```
- The below code produces t1 in the end to now contains `"Shakespeare", "Mr. English", 200, "CS"` Why is that and how can we fix it?
```cpp
Text& Text::operator=(Text&& other){
	Book::operator = (std::move(other));
	topic = std::move(other.topic);
	return *this;
}

Text t1{"Algos", "CLRS", 1000, "CS"};
Text t2{"Shakespeare", "Mr. English", 200, "English"};

Book& r1 = t1;
Book& r2 = t2;
r1 = r2;
```
- Write a template for a linked list, where the programmer can use any type in the node.
- Assuming `v` is a vector, why won't the below work as expected?
```cpp
for(auto it = v.begin(); it != v.end(); ++it){
	if(*it == 5) v.erase(it);
}
```
- Create a decorator and implement the missing portions for Pizza given the following information and UML Diagram:
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