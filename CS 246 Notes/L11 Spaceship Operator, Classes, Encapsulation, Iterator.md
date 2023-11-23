# More on the Spaceship Operator
# Consider a case where `=default` is not appropriate for `<=>`
- Linked Lists
```cpp
Struct Node{
	auto operator<=>(const Node& other) const{
		auto n = data <=> other.data;	
		if(n != 0 || (!next && !other.next)) return n;
		if(!next && other.next) return std::strong_ordering:less;
		if(next && !other.next) return std::strong_ordering:greater;
		return *next <=>*other.next;
	}
};
```
# Encapsulation
```cpp
Struct Node{
	Node* next;
	int data;
	// Big 5
};

Node n1{1, new Node{2, nullptr}};
Node n2{3, nullptr};
Node n3{4, &n2}; // What's going on here
```
- What happens when the destructor runs:
	- `n1` destructor runs: frees node on heap via `delete next;`
	- `n3`'s destructor runs, `delete next`- tries to free `n2` which is stack-allocated
- In the big 5, we assumed:
	1. `next` is always `nullptr` or pointer to heap allocated memory
	2. No sharing of Nodes between lists
		- These are <u>invariants</u>- properties of a data structure that must always be true
		- Issue: Our invariants can be violated by the user
## Definition
- Solution to users violating invariants
- Users should treat objects like "capsules" or "black boxes", no access to underlying data
	- Interact objects via calling methods, which we write to respect invariants
- Introduce access specifiers: `public` and `private`
	- Public fields/methods can be accessed anywhere
	- Private fields/methods may only be called/accessed within class methods
```cpp
Struct Vec{
	private:
		int x, y; // User can't access them
	public:
		vec(int x, int y);
		vec operator+(const vec& other) const;
};
```
- Prefer to keep fields private by default
	- There exists the `class` keyword- default visibility is `private`
		- Vs. `struct` where the default visibility is public(only difference)
```cpp
class Vec{
	int x, y; // Private by default, cannot be accessed outside Vec methods
	public:
		vec(int x, int y);
		vec operator+(const vec& other) const;	
};
```
## Linked Lists with Encapsulation, Protect Variants
```cpp
//List.cc
export module List;
export class List{
	struct Node; // Private nested class
	Node* head = nullptr;
	Public:
		~List();
		void oddToFront(int n);
		int ith(int i);
};

// List-impl.cc
Struct List::Node{
	int data;
	Node* next;
	// Big 5
	~Node(){delete next;}
};

List::~List(){delete head;}
Void List::addToFront(int n){head = new Node{n, head};}
int List:; ith(int i){
	Node* cur = head;
	for(int j = 0; j < i; ++j) cur = cur->next;
	return cur->data;
}
```
- Issue: Looping a list takes $O(n^2)$ time!
# Iterator Pattern
- Iterator pattern is a **design pattern**
	- Effective solution to a common problem
- Solution: Create an **iterator class**- abstraction of a pointer
	- Keep track of how far we have iterated, let us access data, but not modify `next`
```cpp
Class List{
	Struct Node;
	Node* head = nullptr;
	public:
		Class Iterator{
			Node* cur;
			public:
				Iterator(Node* cur), cur{cur}{};
				Iterator& operator++(){
					cur = cur->next;
					return *this;
				}
				bool operator != (const Iterator& other) const{
					return cur != other.cur;
				}
				int& operator*() const{
					return cur->data;
				}; // Ends Iterator
		}
	// List class
	Iterator begin() const{
		return Iterator{head};
	}
	Iterator end() const{
		return Iterator{nullptr};
	}
};

int main(){
	List l;
	l.addToFront(1);
	for(List::Iterator it=l.begin(); it != l.end(); ++it){
		cout << *it << endl;	
	}
}
```
- If you have a class with:
	1. `begin/end` methods returning some Iterator type
	2. This Iterator type has ++, !=, and *
		- You can use range-based for loop
```cpp
for(int n:l){
	cout << n <<< endl; // n is a copy of the data stored in the node
}
```
- Pass by reference
```cpp
for(int &n: l){
	++n; // Increment each node's data by 1
}
```
