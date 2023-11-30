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