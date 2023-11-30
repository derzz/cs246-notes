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