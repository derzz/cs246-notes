# Command Line Arguments
- `./program abc 123`
- Access these in C or C++ in the following way:

```cpp
int main(int argc, char*argv[]){

}
```
- `argc` is number of arguments given to the program, including the name of the program
  - In example, three arguments are given
- `argv[0]` is the name of the program in string form
- `argv[0]` for i = 1 to argc command line args
- `argv[argc]`- defined to be the null pointer
  - Stack:
    - ![](https://cdn.discordapp.com/attachments/966496028469624875/1151905675002257448/image.png)

```cpp
if(argv[1] == "abc"){ ... } // Doesn't Work
  // Comparing the memory addresses and they will always be stored in different locations
  ```
- If statement never succeeds, strings have different memory addresses
- **Advice: Convert `args` to C++ strings before use**

```cpp
int main(int argc, char* argv[]){
    int total = 0;
    for(int i = 1; i < argc; ++i){
        String arg = argv[i];
        int n;
        if(istringstream iss {arg}; iss >> n) total += n;
    }
    cout << total << endl;
}
```

# Default Function Parameters

```cpp
void printfile(string name = "file.txt"){
    ifstreamfile{name};
    string line;
    while(getline(file,line)) cout<<line<<endl;
}

printfile("abc.txt")//prints from abc.txt
printfile() // prints file.txt, uses default
// optional parameteres must come last in the functions args.;
```

- Question, whose responsibility is it to set up the default arguments in memory?
  - The caller of the function must set up the default arguments
```cpp
void f(int x, String s = "Hello"){
    int main(){
        f(5);
        f(5, "abc");
    }
}
```

- It's difficult(impossible) for f(or any function with defaults) to determine whether money is uninitialized or whether it has been initialized to a "strange" value
- Caller sets `args` for this reason
## Where should we store default parameters be specified if we separate into interface and implementation files?
- Default parameters are specified only in the interface/header file
# Overloading
- Example: Negate integers into bools via a function
- In C:
```c
int negInt(int x){
        return -x;
    }
    
bool negBool(bool b){
	return !b;
}
```
- In C++:
```cpp
int neg(int x){
	returns -x;
}

bool neg(boolb){
	return !b;
}
```

- Compiler will choose the overload based on # and types of arguments, will do so at compile time
- Cannot overload solely based on return type!
  - We've seen this with `==, <<, >>, +=`
# Structs

- Classic Example: Node of a linked list
```cpp
    struct Node{
        int data;
        Node* next;
    };
```
  - Don't forget the semicolon!
    - (In C: "`Struct Node*`", in C++ `struct` can be omitted)
# Constants

```cpp
const int MAX_GRADE = 100;
const Node n{5, nullptr};
  ```
- Any changes to `MAX_GRADE` will force the program to not compile
	- No changes allowed to next or data
- In C, the null pointer was written as `NULL` or 0
	- In C++, use `nullptr` instead
- In C standard libraries, most have the following line:
	- `#define NULL 0`, can confuse C++
# Parameter Passing

```cpp
void inc(int x){
  ++x;
}

int main(){
  int x = 5;
  inc(x);
  cout << x << endl; // 5
}
  ```
- Note that `inc(x)` made a copy of the arguments when we pass them to functions
  - "Pass by value" semantics
- If we want to mutate an argument, use a pointer or pass a reference!