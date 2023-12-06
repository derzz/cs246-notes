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