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