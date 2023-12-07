```cpp
class Student{
	public:
		virtual int fees() const;
};

class Regular: public Student{
	public:
		int fees() const override;
};

class Coop: public Student{
	public:
		int fees() const override;
};
```
- In my program, I will create Regular and Coop student objects
	- Student fees is declared, how should we implement it?
	- We want the student class to only create a template for a subclass
	- Ideally, no implementation! Best to have the compiler enforce that only `Regular` and `Coop` students are created
- Solution: Define `Student` fees as pure virtual
# Pure Virtual Method
```cpp
class Student{
	public:
		virtual int fees() const = 0; // Makes it pure virtual
};
```
- Methods you're allowed to not have an implementation
- Any class that defines a pure virtual method cannot be instantiated
- `Student s;` Won't compile
	- Student is now an "abstract class"
- Subclasses of an abstract class are also abstract unless they provide an implementation for all inherited for all virtual methods, then they are concrete
- The point of abstract classes is to organize subclasses
```cpp
class Student{//Now an abstract class
	protected:
		int numCourses;
	public:
		virtual int fees()const = 0;
};

class Regular: public Student{
	public:
		int fees() const override { return 1600*numCourses;}
};
```
## UML
- We represent abstract classes and pure virtual methods via *italics* or use stars
![[pureVirtualAbstractUML.png]]
```plantuml
allow_mixing
abstract class student{
	+ {abstract} fees(): Integer
}
label " " as l0

l0 -|> student
```
# Big Five with Inheritance
```cpp
class Book{
	String title, author;
	int length;
	public:
		//Assume big 5 to be defined here
};

class Text{
	string topic;
	public:
		//Use compiler provided big 5
};

Text t1{...};
Text t2{...};
t1 = t2; // Compiler provided works here, sets t1 fields to the values of t2
```
- Let's see compiler provided big 5
## Copy Constructor
```cpp
Text::Text(const Text& other):Book{other}, topic{other.topic};
```
- `Book{other}` invokes Book's copy constructor
	- Not object splicing, simply copies `other`'s book fields into this Book fields
## Move Constructor
```cpp
Text:: Text(Text&& other): Book{std::move(other)}, topic{std::move(other.topic)}{}
```
- If we were to call `Book{other}`, this would copy title, author, and length, rather than move them, why?
	- `other` is an `lvalue`!
		- `lvalue` lasts for entire scope and you can take the address
		- `lvalue` invokes copy semantics
	- We use `std::move(other)` to be treated as an `rvalue`
## Copy Assignment Operator
```cpp
Text& Text::operator=(const Text& other){
	Book::operator=(other); // use book's assignment operator for the title, length, and author fields
	topic = other.topic;
	return *this;
}
```
## Move Assignment Operator
```cpp
Text& Text::operator=(Text&& other){
	Book::operator = (std::move(other));
	topic = std::move(other.topic);
	return *this;
}
```
- Now consider:
```cpp
Text t1{"Algos", "CLRS", 1000, "CS"};
Text t2{"Shakespeare", "Mr. English", 200, "English"};

Book& r1 = t1;
Book& r2 = t2;
r1 = r2; // What happens?
// operator= is non virtual
// Use static type. Call Book::operator=
```
- t1 now contains `"Shakespeare", "Mr. English", 200, "CS"
- This is *partial assignment*
- What if we made `Book::operator=` virtual, then `r1=r2`, use dynamic type, call `Text::operator=`
```cpp
class Book{
	string title, author;
	int length;
	public:
		virtual Book& operator=(const Book& other);
};

class Text: public Book{
	string topic; // This is not a valid override!
	public:
		Text& operator=(const Text& other) override;
		// args must match exactly with the superclass!
};
```
- We are allowed to override and return a subclass pointer/reference
	- `args` must match exactly with superclass
- This allows:
```cpp
Text t1{...};
t1 = Book{...}; // This compiles, bad.
t1 = Comic{...}; // This is mixed assignment
```
- If `operator=` is non-virtual: partial assignment
- If it's virtual, we get mixed assignment
- Solution: Restructure class hierarchy
- Advice: Make superclass abstract
```plantuml
abstract class AbstractBook
class Regular
class Text
class Comic

AbstractBook <|-- Regular
AbstractBook <|-- Text
AbstractBook <|-- Comic
```
- Also use a protected assignment operator
```cpp
class AbstractBook{
	string title, author;
	int length;
	protected:
		AbstractBook& operator=(const AbstractBook& other) = default;
	public:
		AbstractBook(...){...}
		virtual ~AbstractBook() = 0; // Use destructor as pure virtual to make a class abstract if no other media makes sense
};

class Regular: public AbstractBook{
	public:
		Regular& operator = (const Regular& other){
			AbstractBook::operator=(other);
			return *this;
		}
};
```
- Mixed assignment non-issue
- Text can only be set to Text, comic only to Comic, etc.
- Partial Assignment:
```cpp
Text t1{...}, t2{...};
AbstractBook& r1 = t1;
AbstractBook& r2 = t2;
r1 = r2; //  No longer compiles because we call AbstractBook::operator= outside of the class, and it's a protected method
```