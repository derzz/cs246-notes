# How to Create an Object File(.o)
- `g++20m file.cc -c`- produces `file.o`
	- Compile in dependency order- interface files first, implementation/client code second
- Order:
	- `vec.cc`(`g++20m vec.o vec-impl.o main.o -o program`)
	- `vec-impl.cc`
	- `main.cc`
- `vec.o vec-impl.o` is a linking step
- Tooling such as `make`- doesn't exist yet in cpp
- `g++20m *.cc -c`
	- Will fail for things out of dependency order
- Run this program again to create the rest of files if the first run failed
- `g++20m *.o -o program`
	- Links together the object files
# Classes
- "Simplest form- a class is a struct with functions inside it"
```cpp
// Student.cc

export struct Student{
    int assns, mt, final;
    float grade();
};

// student- impl.cc
float Student:: grade(){
    return 0.4* assns, + 0.2*mt + 0.4*final;
}

Student s{60, 70, 80};
cout << s.grade();
```

- Note that `Student` is a class(there is a class keyword, to be discussed later)
- `s` is an **object**
    - A particular instance of a class
    - `::`- "Scope resolution operator"
        - One use, define a function inside a class
    - `Student:: grade` is a "member function" or a "method"
- When we call `s.grade()`, `assns`, `mt`, `final` are bound to `s.assns`, `s.mt`, `s.final`
## How does the Compiler Access the Correct Fields?
- The use of an implicit parameter: `this`!
    - It's a pointer to the object the method is calling
    - `s.grade()`- in `Student: grade`, `this == &S`
```cpp
struct student {
	int assns, mt, final;
	float grade() {
		return this->assns * 0.4 + this->mt * 0.2 + this->final * 0.4;
	}
};
```
- No need to write `this->` in most cases
- `grade` is defined inside the `struct`(which is allowed)
- Generally separate into interface and implementation
### Initializing Objects
- To control how objects are created, we can write a _constructor_ (`ctor`)
```cpp
// student.cc
struct Student {
	int assns, mt, final;
	float grade();
	Student(int assns, int mt, int final); // Constructor!
};
```

```cpp
//student-impl.cc
Student:: Student(int assns, int mt, int final){
	this->assns = assns; // Disambiguate between assgns field and assns args
	this->mt = mt;
	this-> final = final;
}

// implementation
Student s{60, 70, 70}; // Calls the constructor, ctor call 
    ```

```cpp
Student *p = new Student{60, 70 80}; // creates a pointer
Student s = Student{60, 70, 80}; // equivalent to Student s{60, 70, 80};
```
- If you do not write a `ctor`- compiler gives you field-by-field initialization(like in C)
- If you write your own constructor, compiler will use it instead of field-by-field
### Benefit of Constructors
- They are functions, and allow customization!
    - Bounds checking on arguments
    - Overloading
    - Default parameters
```cpp
struct Student{
	int assns, mt, final;
	Student(int assns=0, int mt=0, int final=0){
		this->assns = assns;
		this->mt = mt;
		this->final = final;
	}
};

Student S{60, 70, 80};
Student newKid{}; //Student newkid; Both invoke the constructor with 0 arguments
```
    

```cpp
struct Student{
    int assns, mt, final;
    Student(int assns=0, int mt=0, int final=0){
        this->assns = assns;
        this->mt = mt;
        this->final = final;
    }
};

Student S{60, 70, 80};
Student newKid{}; //Student newkid; Both invoke the constructor with 0 arguments
Student toby{30, 60}; // Leaves final as 0
```
- Any `ctor` that may be called with 0 arguments is a "default `ctor`"
- If you do not write any `ctor`s, compiler provides a default `ctor`
- Compiler provided Default `ctor`:
	- Primitive fields(`bool`, `int`, `char`, `pointers`)- left uninitalized
	- Object Fields- Calls the object field default `ctor`
- As soon as you write a `ctor`, compiler provided default `ctor` goes away
```cpp
struct vec {
    int x, y;
    vec(int x, int y) { // Not a default constructor
        this->x = x;
        this->y = y;
    }
};

vec v; // Won't compile since there are no arguments, compiler expects two
vec w{1, 2}; // Works
```

```cpp
struct Basis{
    vec v1, v2;
};

Basis b; // Attempts to invoke default constructor
```

- `Basis` did not provide a constructor so use compiler provided default constructor
	- No primitive fields, will not compile as `v1` and `v2` are not default fields
- Writing a default constructor for Basis such that `v1={1,0}` , `v2={0,1}`
```cpp
struct Basis{
	vec v1, v2;
	Basis(){
		v1 = vec{1,0};
		v2 = vec{0,1};
	}
};

Basis b; // Attempts to invoke default constructor
```
- Will compile with errors; the issue is that V1 and V2 will already have been constructed by the time we enter the method          
- Still do not have a default constructor, cannot be constructed like this              
- Fields are being constructed but the default doesn't know what to default them to  
## Steps of Object Creation

1. Space is allocated to store the object in.
2. _Later_ 🤔
3. Fields are initialized
	- Call `vec` default constructor, which doesn't exist
4. `ctor` body runs 
### How to Initialize Fields in Step 3- MIL(Member Initialization List)

```cpp
struct Basis{
	vec v1, v2;
	Basis(): v1{0, 1}, v2{1, 0}{ // MIL(Step 3)
		//ctor body
	}
};
```