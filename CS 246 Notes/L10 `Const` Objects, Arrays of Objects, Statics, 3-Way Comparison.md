# Arrays of Objects
```cpp
Struct Vec{
	int x,y;
	vec(int x, int y): x{x}, y{y}, {}
	// Creates on the stack
	Vec* vp = new Vec[15]; // Object has already been initalized by constructor, run 15 constructors
	Vec sArray[10]; // Run 10 constructors
}
```
- Neither array creation compiles
- Whenever an array of objects is created, default constructor runs for each object in the array
	- Since `vec` has no constructor, compiler will try to call default constructor but no default constructor exists
## Solutions
1. Add a default constructor to the class.
	- What if a default constructor doesn't make sense- Eg. In a student structure, we can't make all IDs zero
2. Stack allocated arrays: Provide constructor arguments during initialization
```cpp
Vec sArray[3] = {{0, 1}, {2, 3}, {4, 5}}
```
3. Use an array of pointers to objects:
```cpp
Vec** vp = newVec*[15];
vp[0] = newVec{0, 1};
vp[1] = new Vec{2, 3}; // RHS: Vec* vp[i] = *(vp+i)
for(int i = 0; i < 15; ++i) delete vp[i];
delete[] vp;
```
# Constant Objects
- Objects can be constant:
```cpp
const Student s{60, 70, 80};
s.assns = 100; // Will not compile
```
- Fields of the objects cannot be changed!
```cpp
const Student s{60, 70, 80};
cout << s.grade() << endl; // Compilation error
```
- `s.grade()` may change a number since it might modify the object fields
	- Call is prevented as it does not know if it will respect the constants of `s`
- If we know grade won't modify fields, we must declare it to use grade with a `const` object:
```cpp
Struct Student{
	int assns, mt, final;
	float grade() const;
};

// Impl File
float Student:grade() const{ // Part of signature! Must appear in interface AND implementation.
	return assns*0.4+mt*0.2 + final*0.4;
}
```


```cpp
const Student s{60, 70, 80};
cout << s.grade() << endl; // New, this compiles, because grade is a const method
```
- Constant objects may only have constant methods called on them
- Non-constant objects can use either constant or nonconstant methods
- Consider collecting stats on our objects:
```cpp
Stuct Student{
	int assns, mt, final;
	int calls = 0;
}
float grade() const{
	++calls; // Won't compile! Method is not const, we're modifying calls!
	return assns*0.4 + mt *0.2 + final*9.4;
}
```
- Issue: Difference of physical vs logical constants
	- Physical: Did the bits in memory that make up the object change?
	- Logical: Did the "essence" of the objects change?
- We can make an exception to what the compiler checks by declaring a field <u>mutable</u>
```cpp
Struct Student{
	int assns, mt, final;
	mutable int calls;
};
```
- Can be changed in constant methods and for constant objects
- Use mutable sparingly- decreases usefulness of `const`
# Static Fields and Methods
- What if I want to record the number of calls for ALL students(instead of just one)?
- Or keep track of # student created?
```cpp
Struct Student{
	inline static int numInstances = 0; // Static means shared among all students
	Student(int assns, int mt, int final): assns{assns}, mt{mt},
	final {final} {
		++numInstances;
	}
};

Student s{60, 70, 80};
Student r{100, 100, 100};
cout << Student::numInstances << endl; // Syntax works with static variables
```
## Static Methods
- Defined for the class, rather than any one particular object
```cpp
Struct Student{
	Static void howMany(){
		cout << numInstances << endl;	
	}
}

Student::howMany();
```
- Static methods may only access static fields
# Three Way Comparison
- String Comparison in C:
```c
Strcmp(s1, s2); // < 0 if s1 < s2, == 0 if s1=s2, >0 if s1 > s2
```
- String comparison in C++:
```cpp
if(s1 < s2) {};
else if(s1 == s2){};
else;
```
- In C, we compare S1 and S2 once
- In C++, we compare S1 and S2 twice, but in C++20, we have a more efficient way of doing this!
## 3 Way Comparison Operator(Spaceship Operator)
- `s1 <=> s2`
- To use: Import `<compare>`
```cpp
std:: string_ordering n = (s1 <=> s2);
if(n < 0) cout << "less"
else if(n==0) cout << "equals"
else cout << "s1 greater than s2"
```
- Since `std::strong_ordering` is a lot to type
	- Use automatic type deduction: `auto n = (s1<=>s2);`
		- `auto` is the return type of expression
- We may also overload `<=>` for our own types
- Eg.  Vectors `v1` and `v2`, if we do `v1 <=> v2`, we get `v1 == v2`, `v1 != v2`, `v1 <= v2`, `v1 >= v2`, `v1 < v2`, `v1 > v2`
```cpp
Struct vec{
	int x, y;
	auto operator <=>(const vec&other) const {
		auto n = x <=> other.x;
		return(n == 0) ?  y <=>other.y : n;	
	}
}
```
- Here, we are comparing fields in declaration order, if that is the case, we can use a defaulted version of the spaceship operator
```cpp
Struct vec{
	int x,y;
	auto operator<=>(const vec&other) const = default;
	// Does field by field comparison in declaration order!
}
```