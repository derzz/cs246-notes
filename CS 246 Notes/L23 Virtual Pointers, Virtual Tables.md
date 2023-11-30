```cpp
class Vec{
	int x,y;
	void f();
};

class Vec2{
	int x,y;
	virtual void f();
};

Vec v;
Vec2 w;
cout << sizeof(int) // 4 
<< sizeof(v) // 8
<< sizeof(w) // 16
```
- `v`'s size is of 2 integers
- classes with no virtual functions, their object size are just the size of the fields
	- No space is taken up by f
- Vec2 takes up more space:
# Review of Virtual Methods
```cpp
Book *bp = new Text/Comic/Book;
bp->isHeavy(); //the call is done depending on the dynamic type, i.e. we call the method implementation based on the type `bp` is pointing to
```
- Any object that corresponds to a class with virtual methods also contains a `vpointer`, a vector pointer points to the vector table, which contains functions pointers to the methods of the class
# Virtual Pointers
```cpp
class C{
	int x,y;
	virtual void f();
	virtual void g();
	void h();
	virtual ~c();
}
```

```cpp
C cobject{1,2};
```
![[Pasted image 20231130114858.png]]
 - (Below: First one is the Book vtable and the second one is the Text vtable)
 ![[Pasted image 20231130115303.png]]
 ```cpp
 Book *bp = new Text/Comic/Book;
 bp->isHeavy();
```
## Steps of `vTables`
1. Follow the `vptr` to `vtable`
2. Find corresponding entry in `vtable`
3. Jump to that function pointer's location
- The above happens at run time
- Virtual functions are slower than non virtual functions because they require a `vtable` look up!
- Additionally, objects corresponding to classes with virtual functions are larger due to the `vpointer`, which implies that as the program is running, it consumes more memory!
## Why put the `vptr` at the top of the object?
```cpp
class A{
	int x;
};

class B: public A{
	int y;
};

B* bp = new B;
A* ap = bp;
ap->x;
```
![[Pasted image 20231130120802.png]]
- To create an `A*` pointing at a `B` object, just point to the same location, and ignore the fields below!
- B objects look like A objects if you ignore the B fields!
# Multiple Inheritance
```cpp
struct A{
	int a;
};

struct B{
	int b;
};

struct C : public A, public B{
	int c;
};
C cObject;
cout << cObject.a << " " << cObject.b << endl;
```
- `C` inherits both `A` and `B`
## Shared Ancestor
```cpp
struct A{int a;};
struct B: public A {int b;};
struct C: public A{int c;};

struct D: public B, public C{
	int d;
};

D dobject;
cout << dobject.a << endl; // Won't compile!
```

```plantuml
A <|-- B
B <|-- D
A <|-- C
C <|-- D
```
- `D` has two `A` fields! One from B and C!
	- Ambiguous!
- Just use `dObject.B::a` and `dObject.C:aa` to differentiate!
- What if I want just one copy of my a fields?
	- Use `virtual inheritance`!
# Virtual Inheritance
- The deadly diamond of death!
```plantuml
A <|-- B : Virtual
B <|-- D 
A <|-- C : Virtual
C <|-- D
```
```cpp
struct B: public Virtual A{...};
struct C: public Virtual A{...};
```
- In `D`, the A fields are shared rather than duplicated- `dObject.a` works!
## `ios_base`
![[Pasted image 20231130123009.png]]
## Object Layout
- What happens if we create an `A*`, `B*`, or `C*` to this object?
![[Pasted image 20231130123456.png]]
- It can easily look like an `A` object or `B` object but not a `C` object
	- What does the compiler do?
