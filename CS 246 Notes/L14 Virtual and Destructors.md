- Recall, we want a book case, an array of books that may behave differently depending on if it's a book, text, or comic
- Book: Heavy if greater than 200 pages
- Text: Greater than 500 pages
- Comic: Greater than 30 pages
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
b.isHeavy() // Runs Book, not Text, look below for explanation
```
- What if we call `b.isHeavy()`???
	- It returns true as it is treated as a `Book`
	- After assigning `b` to a `Text`, we lost the information that `b` was a Text, b is from that point treated as a `Book`
- This is running compiler provided book move constructor, which only moves title, author, length(Same thing happens for copies)
# Object Slicing
> When you try to take a big object and shove it in a smaller hole
- Constructor runs for a superclass and chops off subclass fields
![[objectSlicing.png]]
- What if we use pointers instead?
```cpp
Text t{"...", "...", 300, "topic"};
Book* bp = &t;
bp->isHeavy(); // True
```
- What would `isHeavy()` run in this scenario?
	- `bp` uses the book definition and will act like a book
- To get `isHeavy` to use the Text definition even if pointed to by a Book, we must use a virtual method!
# `Virtual`
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
## Definitions
- **Static Type** of a pointer, give on the left hand side in the type declaration
	- `Book* pb = &t`, `Book*` is the static type
- **Dynamic type**: What is the type of the "underlying object" being pointed to?
	-  `Book* pb = &t`, `Text` is the static type
## Flowchart
1. If not using via a pointer or a reference, we always call the method definition based on the static type
2. Using via a pointer or reference?
	- Non-virtual method -> Use static type
	- Virtual method -> Use dynamic type
```cpp
Text t{"...", "...", 300, "..."};
Book b{"...", "...", 300};
Text* pt = &t;
Book* pb = &t;

// If a book is heavy > 300 pages
// If a text is heavy > 500 pages

t.isHeavy(); //False
b.isHeavy(); // True
pt->isHeavy(); // Since virtual method, use dynamic type of pt, False
pb->isHeavy(); // Use dynamic type, returns false
```
- Bookcase example:
```cpp
Book* myBooks[20];
myBooks[0] = new Text{...};
myBooks[1] = new Comic{...};

for(int i = 0, i < 20; ++i){
	cout << my Books[i] -> isHeavy() << endl; // Uses dynamic type
}
```
- What does override do in `Text.isHeavy`?
	- Nothing(in terms of program execution)
- Checks at compile time that superclass method is actually `virtual`
- This is an example of polymorphism- "many forms"- superclass being able to represent multiple subclass types
- We've seen this; `ifstream` is an `istream`
# Destructors Revisited
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
```

```cpp
X xObj{5};
Y yObj{5, 10};
X* xp = new X{5};
Y* yp = new Y{5, 10};
x* xpToY = new Y{5, 10};
delete xp;
delete yp;
delete xpToY;
```
## Object Destruction Sequence
1. Destructor body runs
2. Object fields have destructors called in reverse declaration order
3. Superclass destructor runs (*new*)
4. Space reclaimed
- Why does `delete xpToY` leak memory?
	- Calls destructor on underlying object- that's a method!
	- Destructor is non-virtual => Use static type
	- We call x's destructor instead of Y's for `xpToY`, leaking memory
- Solution: Make `x`'s destructor virtual!
```cpp
class X{
	int* n;
	public:
		virtual ~x(){delete[] n;}
		x(...){...}	
};
```
- If you renew a a Class may be subclassed, **Always** make its destructor virtual
- If you know a class will never be subclassed, make the compiler enforce it via `final`
```cpp
Class C final{
	... // Won't compile if c is subclassed
}
```