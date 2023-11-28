# Casting(Continued)
1. `static_cast`- sensible, well defined costs
2. `const_cast`- Allows you to remove `const` from a type
```cpp
void f(int* p) // Assume f does not modify the int p points to
void g(const int* p){
	f(p) // Won't compile
	f(const_cast<int*>(p));// Will compile
}
```
- If `f` changes `p`- undefined behaviour
3. `reinterpret_cast`- Take memory and reinterpret the bits stored there as a different type
```cpp
Turtle t;
Student* s = reinterpret_cast<Student*>(&t);
```
- Behaviour depends on the compiler and object layouts
- Generally unsafe!
4. `dynamic_cast`- Let's me check which subclass I am pointing to
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
	if(dynamic_pointer_cast<Text>(b))
}
```