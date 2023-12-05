# Template Functions
- How do `make_unique<T>` and `d/r/s/c_cast` work? Via template functions!
```cpp
template<typename T> T min(Tx, Ty){
	return x < y? x:y;
}
```
- `min(1,2)` returns 1, min for ints `min<int>` is automatically deduced, we can still specify type if necessary
- `min('a', 'c')`- `min(char)` called
- `min(1.5, 2.3)`- `min(float)` called
- What types work with min- those with copy/move constructors and operator< defined
```cpp
void for_each(AbstractIterator& start, AbstractIterator& finish, int (*f)(int)){
	while(start != finish){
		f(*start);
		++start;
	}
}
```
- Above works only if the iterator supports the `AbstractIterator` type

```cpp
template<typename Iter, typename Fn>
	void for_each(Iter start, Iter end, Fn f){
		while(start != finish){
			f(*start);
			++start;
	}
}
```
- This works so long as `Iter` has ++, !=, and `*`, and `*`'s return type matches the function's argument type works for not just `AbstractIterator`, but also STL iterators, and even regular pointers
```cpp
void print(int n){cout << n << " ";}
int a[5] = {1, 2, 3, 4, 5};
for_each(a, a + 5, print);
// a starts at the front of the pointer, a + 5 at the end, print is the print function
```
- `for_each` exists in `<algorithm>`
- Functions also exist!
# `<algorithm>`
## Find
```cpp
template<typename Iter, typename T>
	Iter find(Iter start, Iter finish, const T& val);
	// Searches in [start, finish) for an iteratorthat when dereferenced == val
	// Returns iterator to first the matches or finish if not found
```
## Count
- Similarly but returns `int`, indicating number of values found in range `[start, finish)`
## Copy
```cpp
template<typename InIter, typename outIter>

```