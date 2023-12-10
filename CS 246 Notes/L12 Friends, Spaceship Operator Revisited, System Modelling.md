# Representation Exposure, Friendship
```cpp
auto it = List::Iterator{nullptr};
```
- This violates the idea that all iterators are created by calling begin or end
	- End iterator might not be as expected, since it depends on the data structure, linked lists and nodes are different
- Consider making `List::Iterator` private!
	- Being a private method, this couldn't be called by the user!
- Then List cannot make iterators in begin/end
	- Solution: Make an exception for `List`, they get privileged access, they can call the private Iterator constructor
	- Use the power of friendship! :D
```cpp
Class List{
	Struct Node;
	Node* head = nullptr;
	public:
		class Iterator{
			Node* cur;
			Iterator(Node* cur): cur{cur}{}
			public:
			// operator overloads
				friend class List; // Declares List as a friend of Iterator
		};
			Iterator begin(){
				return Iterator{head};
			}
			Iterator end(){
				return Iterator{nullptr};
			}
		};
};
```
- If class A declares class B as a friend => B may access private fields/methods of A
-  Now user cannot create Iterators, only List can, and it forces the use of `head`
	- We are sure all Iterators created are via begin/end
> Why have friends if they're not doing anything for you?
- Advice: Limit your friendships!
- More friendships means more difficult about private fields/invariants
- Consider using accessor/mutator methods instead
	- Getters/setters
```cpp
Class Vec{
	int x, y;
	public:
		int getX() const {return x;} // accessor
		void setX(int a){x = a;} // mutator
};
```
- What about implementing `operator<<`
	- Must be a standalone function, how to access fields?
- Use getters if defined
- Or: Declare `operator<<` as a friend function, access to private function and fields of that particular object
```cpp
Class Vec{
	int x,y;
	public:
		friend oStream& operator<<(oStream&, const Vec&);
		// This standalone function can access x and y, even though they are private
		oStream& operator<<(oStream& out, const vec& v){
			return out << v.x << " " << v.y;
		}
}
```
> If someone declares you as their friend, you get access to their privates.
# Equality Revisited
- We now have an encapsulated List class
- We already have `ith`, `addToFront`, what about a length method?
1. Loop through it, count # of Nodes, return value once we've reached end
	- $O(n)$ time
2. Keep a length field, update it when `addToFront` is called
	- Return field in the method, $O(1)$ time
- Option 2 generally preferred, allows us to optimize equality
	- Previously to check equality:
```cpp
l1 == l2 // (l1 <=> l2) == 0;
```
- Using space ship operator takes O(n) time
- Most lists will differ because they have different lengths
- Short cut for `==`, check length first. If lengths differ, so do the lists. $O(1)$ time
```cpp
bool operator==(const List& other) const{
	if(length != other.length) return false;
	return(*this<=>other) == 0;
}

auto operator<=>(const List& other) const{
	if(!head && !other.head) return std::strong_ordering::equal;
	if(!head && other.head) return std::strong_ordering::less;
	if(head&& !other.head) return std::string_ordering::greater;
	return *head<=>*other.head;*
};
```
- Spaceship operator provides `>=, <=, >, <, !=` for List, but for `==`, compiler will use our optimized version instead
# System Modelling
- Display graphically the classes of a program at a high level
- Using popular standard UML(Unified Modelling Language)
![[SystemModelling.png]]

## Relationship Between Classes
- Composition is one possible relationship
- One class is embedded within the other
```cpp
class Basis{
	Vec v1, v2;
};
```
- Basis is composed of two `vecs`, also called "owns-a"
	- Basis owns the vectors
- If a "owns -a" B:
	1. If A dies, so does B
	2. If A is copied, so is B(deep copy)
	3. B does not have independent existence outside A
- Typically owns-a is implemented using object fields
![[relationshipBetweenClasses.png]]