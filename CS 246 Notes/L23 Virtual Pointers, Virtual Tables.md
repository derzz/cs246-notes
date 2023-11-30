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
- Challenge