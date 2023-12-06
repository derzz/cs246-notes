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

# Static and Spaceship Operator
- Using the spaceship operator, make a program that calculates whether a Linked List is longer than the other.

- Define encapsulation and using structs and classes, make a problem with them.
- Create an iterator to traverse a given linked list. Include the operations `++`, `!=`, and `*`(dereference
	- After, create a main function, given a linked list `a`, using the iterator, output the values of `a`.
- Why doesn't the below code work?
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

// Member function definition
void Box::setWidth( double wid )
{
    width = wid;
}

void printWidth( Box box )
{
    /* Because printWidth() is a friend of Box, it can
    directly access any member of this class */
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