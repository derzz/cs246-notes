# Revisit Vectors
- `v[0]`- gets the `i`th element of the vector
	- If `i` is outside bounds, undefined behaviour occurs
- Also have `v.at(i)`, gets the `i`th element of the vector
	- If `i` is outside bounds, an error is thrown instead of crashing
- How does error handling work in C?
	1. Reserve a value like `-1`, `INT_MIN`, etc
		- Shrinks our return space, what should it be for other types, like `Student`
	2. Struct with an error field
		- Wastes space for most returns
		- Easy to ignore the error field
	3. Use a global variable, like `errno` integer
		- Easy to ignore and be overwritten if multiple errors
# Exceptions
- To handle errors, C++ introduced exceptions(`exn`) can be **raised/thrown**. Program then goes to a **handler or catch block** to deal with the exception
	- No handler found => program crashes(`std::terminate`)
- If `v.at(i)` has `i` out of bounds, a `std::out_of_range` exception is thrown
- Found in `<stdexcept>`
```cpp
Vector<int> v{1, 2, 3};
try{
	int n = v.at(100); // This throws, jump to the catch
	cout << n << endl;
} catch(out_of_range r){
	cout << "Error" << r.what() << endl;
}
```
- Control flow jumps from the error statement to the try catch immediately
- `out_of_range` is just an object, and `what` is just a method that returns a string, describing the error that occurred
```cpp
int main(){
	try{
		h();
	}catch(out_of_range r){
		cout << r.what() << endl;
	}
}

void h(){
	g();
}

void g(){
	f();
}

void f(){
	throw out_of_range{"f threw"};
	// Returned by what()- run constructor
}
```
- When `f()` throws an exception, the program searches for a catch block
- Control flow transfers from `f()` directly to the catch block
	- In doing so, we perform **stack unwinding**
		- Local variables in `f`, `g`, and `h` are destroyed
## Throwing an Exception within Catch
```cpp
try{...}
catch(out_of_range r){
	// Do work, change vector, etc.
	throw invalid_argument{"Reprompt input"};
}
```
## Reraising an Exception
```cpp
try{...}
catch(out_of_range r){
	// Do some work
	throw;
}
```
- Why `throw` instead of `throw()`?
	- Consider inheritance
```plantuml
ErrorSituation <|-- "Special Error"
```
```cpp
try{}
catch(ErrorSituation &e){
	throw e;
}
```
- If `e` is an `ErrorSituation`, this is fine
	- If it's a special error, then because `throw e;` performs a copy, object slicing occurs!
- Whereas `throw;` does not copy and no object slicing!
- Can we write a general handler? Yes, using catch-all syntax!
```cpp
try{...}
catch(...){...} // Three dots means catch all!
```
- Although we usually throw objects, you can throw **anything** including primitives like int,, and your own types!
## Personal Exceptions
- You may also create your exception types
```cpp
class BadInput{};
try{throw BadInputs{};}
catch(BadInput & b){...}
```
- Advice: Throw by value, catch by reference, prevents object slicing!
- If we call new and OS rejects request for more memory
	- `Std::bad_alloc` is thrown
- Advice: Do not let destructor throw an exception!
- Destructors have an implicit tag called `noexcept`
	- If you throw, the program immediately crashes!(`std::terminate`)
- If we tag our destructor as `noexcept`(false), it won't immediately crash!
- RULE: At any time, we may only have one active exception!
- During stack unwinding, destructors run for stack allocated objects
	- This can cause two exceptions:
		1. The original exception that started unwinding
		2. Exception thrown from the destructor
	- 2 active exceptions means that the program crashes(`std::terminate`)
# Factory Method Pattern
- Problem: I want to create different versions of an object based on policies that should be easily customizable!
## Example: Enemy and level generation
```plantuml
abstract class Level
Level <|-- Normal
Level <|-- Hard

abstract class Enemy
Enemy <|-- Turtle
Enemy <|-- Boss
```
- Normal levels generate mostly Turtles, a few Bosses
- Hard levels are mostly bosses and some turtles
```cpp
class Level{
	public:
		virtual Enemy* getEnemy() = 0;
		virtual ~Level(){}
};

class Normal: public Level{
	public:
		Enemy* getEnemy() override{
			// mostly turtles, some bosses	
		}
};

class Hard: public Level{
	public:
		Enemy* getEnemy() override{
			// mostly bosses, some turtles
		}
};

level *l = ...;
Enemy* e = l->getEnemy();
```
- Use e and l, use their public methods
- Easy to add newLevels, new Enemies, and new policies for making enemies
# Template Method Pattern
- Not related to C++ templates
- Problem: What if I want some behaviour customizable in subclasses, not all!
- Example: Drawing Turtles- red and green turtles
```plantuml
abstract class Turtle
Turtle <|-- RedTurtle
Turtle <|-- GreenTurtle
```
- Both have head, shell, legs, only customizing shells
```cpp
class Turtle{
	void drawHead(){...}
	void drawLegs(){...}
	virtual void drawShell() = 0;
}

public:
	void draw(){
		drawHead();
		drawShell();
		drawLegs();
	}
};
```

