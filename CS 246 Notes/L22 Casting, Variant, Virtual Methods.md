# Casting(Continued)
## `static_cast`
- sensible, well defined costs
## `const_cast`
- Allows you to remove `const` from a type
```cpp
void f(int* p) // Assume f does not modify the int p points to
void g(const int* p){
	f(p) // Won't compile
	f(const_cast<int*>(p));// Will compile
}
```
- If `f` changes `p`- undefined behaviour
## `reinterpret_cast`
- Take memory and reinterpret the bits stored there as a different type
```cpp
Turtle t;
Student* s = reinterpret_cast<Student*>(&t);
```
- Behaviour depends on the compiler and object layouts
- Generally unsafe!
## `dynamic_cast`
- Lets me check which subclass I am pointing to
```cpp
Book* bp = ...
Text* tp = dynamic_cast<Text*>(bp);
```
- If `bp` points at a Text, `tp` points at that same Text object
	- Otherwise, `tp` is set to `nullptr`
```cpp
if(tp) cout << "Text" << endl;
else cout << "Not a Text" << endl;
```
- Dynamic cast only works if you have at least **one virtual method!**
- We can also cast from `shared_ptrs` to other `shared_ptrs`: In `<memory>`
	- `static_pointer_cast`, `dynamic_pointer_cast`, `const_pointer_cast`, `reinterpret_pointer_cast`
- We can also dynamic cast references:
```cpp
Book& br = ...;
Text& tr = dynamic_cast<Text&>(br);
```
- No such thing as a null reference- `std::bad_cast` exception is thrown!
### Polymorphic assignment problem
```cpp
Text t1{...}, t2{...};
Book& r1 = t1; Book& r2 = t2;
r1 = r2;
```
- If `operator=` is non virtual: partial assignment
- If it's virtual, mixed assignment
	- Ability to set classes to one another that are not the same
- Recall signature of virtual `operator=`
```cpp
Text& Text::operator=(const Book& other){
	if(this == &other) return *this;
	const Text& tother = dynamic_cast<const Text&>(other);
	Book::operator=(tother);
	topic = tother.topic;
	return *this;
}
```
### Is `dynamic_cast` good style?
- With `dynamic_cast`, we can make decisions based on the run-time type information(RTII) of an object
```cpp
void whatIsIt(shared_ptr<Book> b){
	if(dynamic_pointer_cast<Text>(b)) cout << "Text";
	else if(dynamic_pointer_cast<comic>(b)) cout << "Comic";
	else cout << "Regular Book";
}
```
- Tightly coupled to Book hierachy
	- If I add a subclass, must add another `else-if`!
	- This must be done everywhere I do this pattern otherwise there's a bug!
- Is `dynamic_cast` bad style?
	- It depends on use
	- Use in `Text::operator =` doesn't need changing if we add more subclass types!
- We can fix `whatisIt` by using a virtual method!
```cpp
class Book{
	public:
		virtual void identify()const {
		cout << "Book" << endl;
	};
}

class Text: public Book{
	public:
		void identify() const override{
			cout << "Text" << endl;
		}
};

void whatIsIt(Book* b){
	if(b) b->identify();
	else cout << "Nothing";
}
```
- We were able to use this solution of `identify`:
1. There are a high number of possible Book subtypes and we want to add new ones with ease
2. Books can have a uniform interface, subclass behaviour doesn't deviate too much

- What about the opposite scenario:
1. Consider we know subclass types in advance and we are ok that adding new subclasses will require extensive code changes
2. Subclasses may not conform to a uniform interface- each may have significantly differing behaviour
```cpp
class Turtle: public Enemy{
	void StealShell();
}

class Boss: public Enemy{
	void epicBossBattle();
};
```
- Adding new enemies will require large changes as each have unique behaviour- so maybe `dynamic_casting` isn't so bad
	- Use `std:variant`
# Variant
- Variant, found in `<variant>` acts as a `type_safe` union:
```cpp
using Enemy = variant<Turtle, Boss>;
// Type alias- Enemy means variant<Turtle,Boss>;

Enemy e{Turtle{...}} // or {Boss{}}
if(holds_alternative<Boss>(e)){
	// It's a boss
} else{...}

// Access Value
try{
	Turtle t = get<Turtle>(e);
} catch(std::bad_variant_access&){
	...
}
```
- If variant is left uninitialized- eg. Enemy e;
	- It will default construct the first type in variant list
- Won't compile if first type is not default constructable

- If first type doesn't have a default constructor:
1. Add a default constructor
2. Reorder types so first has a default constructor
3. Use `std::monostate`- represents empty
	- eg. `variant<monostate, Turtle, Boss>;
- `std:optional<T>` is the same as `variant<monostate, T>`
# How do Virtual Methods Actually Work?
```cpp
struct Vec{
	int x,y;
	void f();
};

struct vec2{
	int x,y;
	virtual void f();
};

vec v; // Are these different in memory?
vec2 w;
cout << sizeof(int) << sizeof(v) << sizeof(w);
```