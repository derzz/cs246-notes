- Aggregation, Specialization (Inheritance)
- Composition: If `A` ”owns-a” `B`, then:
    - `A` dies → `B` dies
    - `A` copied → `B` copied (deep copy)
    - `B` has no independent existence apart from `A`
```cpp
struct Basis {
    Vec v1, v2;
};
```
# Aggregation
- If class `A` ”has-a” `B`, then:
    - `A` dies → `B` keeps living
    - `A` is copied → `B` is not copied (shallow copy)
    - `B` may have independent existence outside `A`
```cpp
class Student {
    int id;
    University* myUni;
    public:
        Student(...) {}
};
int main() {
    University uw{...};
    Student s{1, &uw};
    Student t{2, &uw};
}
```
![[compositionAndAggregation.png]]
- Typically, composition uses object fields, and aggregation uses references or pointers
### Linked List
- Consider linked list, which is implemented via pointers but is a composition relationship
- Implementing the Big 5 usually means ownership and composition
![[linkedList.png]]
- Both versions are valid, but it depends on the context in which you use it in
# Specialization (Inheritance)
- Imagine a program to manage and catalogue library books
- We will manage books, texts, and comics
```cpp
class Book {
    string title, author;
    int length;
    
    public:
        Book(...) {}
};
class Text {
    string title, author;
    int length;
    string topic;

    public:
        Text(...) {}
};
class Comic {
    string title, author;
    int length;
    string hero;
    public:
        Comic(...) {}
};
```
- What if I want an array of all types of Books? It should be able to store `Texts`, `Books`, and `Comics`
    1. Use `void *`s, which can point to anything (this is not type safe)
    2. Use a union type
```cpp
Union BookType {
	Book* b;
	Text* t;
	Comic* c;
};
```
- Stores one of the three types
```cpp
BookType u;
u.b = new Book{...};
cout << u.t->topic << endl;
```
- This is also not safe as it is undefined
- Issue is that the compiler is not aware of the relationship: A comic is a type of book, a text is a book specialization relationship or "is a" implemented in C++ via public inheritance
```cpp
class Book{ //Basic or superclass
	String title, author;
	int length;
	public:
		Book(String title, string author, int length): title{title}, author{author}, length{length}{}
};

class Text: public Book{
	String topic; // Derived subclasses
	public:
		Text(){}
}

class Comic: public Book{
	string hero;
	public:
		comic(){
			
		}
}
```
- Text and comic inherit from Book, this means they have title, author, and length fields
- A subclass inherits all fields + methods from its superclass(title, author, length are private in Book)
- title, author, and length are only accessible in `Book`, not subclasses of Book
- Below doesn't work!
```cpp
class Text: public Book{
	string topic;
	public:
		Text(string title, string author, int length, string topic):
			title{title}, author{author}, length{length}, topic{topic}{}
};
```
- Reasons for not working:
	1. title, author, length are private to Book, cannot be changed in Text AND MIL is only for your class's fields
	2. Object creation sequence updated:
		1. Space is allocated
		2. *New*, superclass constructor runs
		3. Fields are initialized via MIL
		4. Constructor body runs
	- In step 2, superclass constructor is called, if you do not specify the arguments for superclass constructor, default constructor will be used. If that doesn't exist, it won't compile
- Below is valid:
```cpp
class Text: public Book{
	string topic;
	public:
		Text(string title, string author, int length, string topic):
			Book{title, author, length}, topic{topic}{}
};
// step 2
```
- Generally, it is a good idea to keep superclass fields private for subclasses
	- If we do want to give just subclasses access, use `protected`, accessible in a class and subclasses, nowhere else
- Consider adding multiple authors to Texts:
```cpp
class Book{
	protected:
		string title, author;
		int length;
	public:
		Book(){}
}
```

```cpp
class Text: public Book{
	string topic;
	public:
		Text(){}
		void addAuthor(string a){author += a;} // protected, can be accessed
}
```
- Preferred: Use `protected` mutators
```cpp
class Book{
	string title, author;
	int length;
	protected:
		void setAuthor(string a){author=a;} // May only be called from subclasses
};
```
# Specialization/"is -a" in UML
```plantuml
card Book
card Text
card Comic
Book <|-- Text
Book <|-- Comic
```
- Public inheritance in C++