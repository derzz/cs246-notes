# Member Initialization List
```cpp
struct vec{
    int x, y;
    vec(int x, int y){
        this->x = x;
        this->y = y;
    }
}

struct Basis{
    vec v1, v2;
};

Basis b; // won't compile!

// Will compile, set MIL
struct Basis{
    vec v1, v2;
    Basis(): v1{0, 1}, v2{1, 0} {}
}

Basis b; // Will compile by setting default parameters to v1 and v2
```
- More generally, we can create a constructor like so
```cpp
struct Basis{
    vec v1, v2;
    Basis(): v1{0, 1}, v2{1, 0} {}
    Basis(const vec& v1, const vec&v2): v1{v1}, v2{v2}{} // Takes in two vectors and initializes them
}
```

- Initializing `v1` and `v2` is running the constructor for `vec`
- Alternate syntax to initialize:
```cpp
struct Basis{
    vec v1{1, 0}, v2{0, 1};
    Basis(){}
    Basis(const vec&v1, const vec& v2): v1{v1}, v2{v2}{}
}
```

- `v1{1, 0}` and `v2{0, 1}` are defaults for fields used if or 2 is not mentioned in the MIL
- Note that for the MIL; Fields are initialized based on the order of declaration inside the class
    - Write the MIL in declaration order and everything will run left to right
- MIL is often more efficient than setting values in the constructor body

```cpp
struct Student{
    int assns, mt, final;
    string name;
    Student(int assns, int mt, int final, const string& name){ // MIL allows to initialize something once
        this->assns = assns;
        ...
        this->name = name;
    }
}
```

- MIL is implicit here and constructor is moving everything
- Objects are default constructed and inside the constructor, we are overriding the values

```cpp
Student(int assns, int final, const string& name): 
    assns{assns}, mt{mt}, final{final}, name{name}{}
```
## When to use MIL
1. Objects fields are not default constructible
2. `Const` fields and reference fields
- Preferred for any type of initialization you do, use MIL whenever possible!
### Copy Constructors

```cpp
Student s{60, 70, 80};
Student r{s};
Student t = s;
```
- All of these involve the copy constructor, creating one object from another of the same type
#### Compiler Provided Copy Constructor

- Default constructor(goes away when any other constructor is written)
    - Note default constructor takes in 0 arguments
- Destructor, copy constructor, copy assignment operator, move constructor, move assignment operator
### Copy Constructor Example

```cpp
Student s{60, 70, 80};
Student r{s};
Student t = s;

struct Student{
    int assns, mt, final;
    Student(const Student& other): assns{other.assns}, mt{other.mt}, final{other.final}{ // other is referencing s
    } 
}
```

```cpp
struct Node{
    int data;
    Node* next;
}

Node* n = new Node{1, new Node{2, newNode{3, nullptr}}};
Node p = *n; // Types do not match up, dereference n with *, invokes copy constructor
Node* q = new Node{*n};
```

- Memory diagram of these stacks:
    - ![](https://cdn.discordapp.com/attachments/966496028469624875/1156991105955541083/image.png?ex=6516fb6f&is=6515a9ef&hm=df2c22f163354a5259bb8357eb60af6229b244841d712311dd21da34b12cf7f7&)
- Issue: There is data shared among our linked lists!
    - Compiler provided copy constructor is shallow: ==Pointers are pointing to the exact same location and directly copied!==
    - We want a deep copy: 3 identical but independent lists!
## Refresher on Ternary Operators

- `boolean value ? trueValue: falseValue;`
- Eg. `int x = (string{argv[1]} == "a") ? 10 : 20;`
## Node Copy Constructor

```cpp
struct Node{
    int data;
    Node* next;
    Node(const Node& other): data{other.data}, // Moving node down for copying
    next{other.next ? new Node{*(other.next)} : nullptr}{}
};
```

^a4c543

- Here, we're making a copy of the other node so we don't have the issue of linking to the previous nodes!
    - Running constructor of the node based on another node reference
    - Recursion, and will stop recursing when there's no nullptr
- Can have stack overflows if keep calling the stack
    - ![](https://cdn.discordapp.com/attachments/966496028469624875/1156994321589993592/image.png?ex=6516fe6e&is=6515acee&hm=0bb4657c13e3da22bbf282dfbbad5382878a9bbd7ffe3c67f1f5ec1113449100&)

### When is Copy Constructor Invoked?
- Constructing one object from the another of the same type
- Pass by value: There are subtleties- to be discussed regarding move