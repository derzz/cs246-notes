```cpp
class Turtle{
	void drawHead(){...}
	void drawLegs(){...}
	virtual void drawShell() = 0; // Private pure virtual
}

public:
	void draw(){
		drawHead();
		drawShell();
		drawLegs();
	}
};

class RedTurtle: public Turtle{
	void drawShell() override{
		
	}
};
```
- If we call draw on a `Turtle*`
	- Call `Turtle:drawHead`
	- `(Red/Green)Turtle::drawShell`
	- `Turtle::drawLegs()`
- What is the purpose of public methods?
	- Provide an interface to clients- with invariants, pre/post conditions, and a description of what the method does
- Purpose of virtual methods- "an interface" for subclasses to override and change behaviour
- What about public virtual methods: Contradictory- they promise behaviour to clients, while giving subclasses power to change behaviour
- No guarantee subclasses will respect invariants when overriding
	- Template method pattern may be generalized into non-virtual idiom(**NVI**)
# NVI
- NVI States:
	1. Public methods should be non-virtual
	2. Virtual methods should be private or protected
	3. Exception: Destructor should be public virtual
```cpp
// No NVI
class DigitalMedia{
	public:
		virtual void play() = 0;
};

// With NVI
class DigitalMedia{
	virtual void doPlay() = 0;
	public:
		void play(){doPlay();}
};
```
## Benefit: Flexibility
- We can add code that will always run by putting it before or after `doPlay()`(Eg. Copyright, play count, etc.)
- Can also add more hooks for customization by adding more private virtual methods
 like `showCoverArt()`
 - All without changing the public interface! Just as fast as before, compiler will optimize out extra function call
 - Easier to use NVI from the start than adding it later
# STL Maps
- Array that can be indexed with different types than integer
	- Found in `<map>`
```cpp
map<string, int> m;
m["abc"] = 2;
m["def"] = 3;

cout << m["abc"] << endl; // 2
cout << m["ghi"] << endl; // Look below
```
- If a key is not found, the value is default constructed(objects), zero initialized(primitives)
```cpp
if(m.count("abc")){...}
//returns 0 if key not found, 1 if found
m.erase("abc");
```
### Iteration over Keys
```cpp
for(auto& p:m){
	cout << "key" << p.first << "value" << p.second << endl;
}
```
- Type of p: `std::paid<string, int>&`
	- Pair is just a struct, first stores a string, second an int
	- Used a struct because it's just a collection of data, no invariants to preserve
- Or use a structured binding:
```cpp
for(auto&[key,value]: m){
	cout << key << " " << value;
}
```
- Structured bindings can be used for structs with all public fields
```cpp
vec v{1, 2};
auto [localX, localY] = v; // localX = 1 localY = 2
```
- May also use for stack arrays if size is known
```cpp
int a[3] = {1, 2, 3};
auto[x, y, z] = a;
```
# Coupling and Cohesion
- What should go in a Module?
	- How to tell if code is well structured?
	- Right now, we have one class per module
- Larger programs contain multiple classes/functions per module
## Coupling
- To what extent do modules depend on each other?
- Low Coupling: 
	- Simple communication via parameters and results
	- Communication via arrays or structures
	- Modules affect each other's control flow
	- Modules share global data
- High Coupling: 
	- Modules have access to each other's implementation(friends)
- We want low coupling
	- Makes our program easier to change
## Cohesion
- How much do the parts of a module relate to each other?
- Low Cohesion: Module parts are unrelated(Eg. `<utility>` library)
	- Module parts share a common theme- `<algorithm>`
	- Parts of a module cooperate to manage lifetime state(read/open/write files)
- High cohesion: All parts of a module cooperate to perform one task
- We want **low coupling, high cohesion**
- What if we have two classes that depend on one another
```cpp
class A{
	int x;
	B y;
};

class B{
	int x;
	A y;
};
```
- Cannot determine the size of `A` or `B`!
- Break the chain of dependencies via a pointer
```cpp
class B;
class A{
	int x;
	B* y;
};
class B{
	int x;
	A y;
};
```
- We cannot forward declare from another module, so A and B must be in the same module
- Forward declaration is not allowed for object fields or for inheritance
# Tic Tac Toe
- Coupling and cohesion to Tic Tac Toe
```cpp
class Board{
	public:
		void play(){... cout << "Your move;" << end; ...}
};
```
- Book is coupled with `cout`, cannot reuse Board without using the print statements!
- What if I want to print to a file? Or not print at all?
```cpp
class Board{
	istream& in;
	ostream& out;
	public:
		Board(istream& in, ostream& out): in{in}, out{out}{}
		void play(){... out << "Your Move" << endl...}
};
```
- Sill coupled with streams, what if I want a graphical display? Or a web API?
- Board should not do communication with user
	- Single Responsibility Principle: Each class should have exactly one reason to change
	- If changes to 2 different parts of the spec requires changes to the same class, SRP is violated
		- Board State + communication are different things
		- Where to do communication?
			- In main? Probably not
		- Main function is not reusable like other classes are
		- Split into MVC for handling data, output, and input(oh god)