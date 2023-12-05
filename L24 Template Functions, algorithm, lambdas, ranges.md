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

```
template<