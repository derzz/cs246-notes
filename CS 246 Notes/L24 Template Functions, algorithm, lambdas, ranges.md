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
	outIter copy(InIter, start, InIter finish, outIter result);
```
- Copies the values from `[start, finish)`, increments results each time
- Assume `result` has enough space
```cpp
vector v{1, 2, 3, 4, 5, 6, 7};
vector<int> w(4); // 4 0's
copy(v.begin()+ 1, v.begin()+5, w.begin()); // v.begin()+5 is not inclusive
// w = {2, 3, 4, 5}
```
## Transform
```cpp
template<typename InIter, typename outIter, typename fn>
	outIter transform(InIter start, InIter finish, OutIter result, Fn f){
	while(start != finish){
		*result = f(*start);
		++ start; ++result;
	}
	return result
	}
```
- Take values from start to finish and put them in result and make them return `f`
```cpp
int add1(int n){return n+1;}
vector<int> v{2, 3, 5, 7, 11};
vector<int> w (v.size());
transform (v.begin(), v.end(), w.begin(), add1);
// w = {3, 4, 6, 8, 12};
```
- We say `Iter` requires ++, !=, `*`, what about `Fn`?
	- The argument types must match, and `f(*start)` must be valid syntax!
- We can overload `operator()`, this makes a function object or `functor`
# Functor
```cpp
class Plus{
	int m;
	public:
		plus(int m): m{m}{}
		int operator()(int n){return n + m};
}

Plus p{5};
cout << p(10) << endl; // 15
transform(v.begin(), v.end(), w.begin(), p);
p = {7, 8, 12, 16};
```

```cpp
class IncreasingPlus{
	int m = 0;
	public:
		int operator()(int n){return n + (m++);}
}

IncreasingPlus ip;
cout << ip(5) << ip(5) << ip(5) << endl;
// 5 6 7

vector v{2, 3, 5, 7, 11};
vector<int>w(v.size());
transform(v.begin(), v.end(), w.begin(), IncreasingPlus{});
w = {2. 4. 7. 10. 15};
```
- Vector of integers, how many are even?
```cpp
bool even(int n){return n % 2 == 0;}
vector<int> v{...};
int x = count_if(v.begin(), v.end(), even);
// Address of operator is implicit
```
# Lambda Functions
- In CS135, we would use a lambda instead of a full function, we can do the same here:
```cpp
int x = count_if(v.begin(), v.end(), [](intn){return n%2 == 0;})
```
# Iterators
- Iterators are powerful concepts, can be applied beyond just containers
	- See `<iterator>`
```cpp
vector<int> v{1, 2, 3, 4, 5};
ostream_iterator<int> out{cout, ","};
copy(v.begin(), v.end(), out); //prints 1, 2, 3, 4, 5
```
- Another example, copy requires enough space to copy into!
	- What if I want to expand a container while copying?
```cpp
vector v{1, 2, 3};
vector w{4, 5, 6};
copy(v.begin(), v.end(), back_inserter(w));
```
- For `back_inserter` iterators, assignment calls `push_back` for you, `w = {4, 5, 6, 1, 2, 3}`
- Advice: Use `<algorithm>`, it can greatly simplify code and reduce bugs
# Ranges
- Consider `vector<T>::erase`- takes in an iterator, erases value at location, shifts down, returns iterator to now element in that location
	- `O(n)` time
- What if I want to erase k elements in a for loop: `o(nk)` time- instead use ranged version of erase: `erase(Iter start, Iter finish)`- erases from `[start, finish)`, shift once
- 