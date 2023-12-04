- Most important notes to know are pointers and dynamic memory
- Linux and Bash, CMD Line, Debuggers(GDB and Valgrind), makefiles, source control

# 3 Perspectives to OOP

- Programmer's perspective: What does the code mean or do?
	- How do we use OOP?
- Compiler's perspective, how does our code get transformed to run on the machine?
- Designer's perspective: How do we use OOP to write longer, more scalable and robust programs?

# Hello World in C and C++

```c
// In C
#include <stdio.h>
void main(){
    printf("Hello world!\n");
}
```

```cpp
// In C++20
import <iostream>; // Using C++20 Modules
int main(){
    std::cout << "Hello World" << std::endl;
    return 0;
}
```

- `import <iostream>` gives us access to `cout` and `endl`
- In C++, main must return an int type, accessible in bash via $? Returns 0 by default
- `std::cout << data << data << data`
	- Print to standard output
- `std:: endl` ends line, print new line character, "flushes the output buffer"
	- Print stuff immediately, and see this stuff on the screen now
- If we don't want to write `std::` for `Cout` and `endl`, we can write: `using namespace std;`
  - Now we can write `cout << "hello world" << endl;`
- `stdio.h, printf` still exists in C++, but preferred to not be used
  - For memory safety in most cases
    
# Compilation

- `#include`, the pre-processor will convert those to code immediately  
	- A lot of includes will keep recompiling and can cause slowdowns
- Compile system headers: `g++20h iostream`
  - Creates a folder in the local directory and keeps reusing this
	  - Adds `gcm.cache`
  - Do this once when importing and go from there
- Compile program, `g++20m main << -o program`
  - Omitting `-o program` defaults it to `a.out`
## Not using C++ 20 Modules

- `#include <iostream>` , copy and pasting entire program and no need to do separate compilation
  - `g++20i main.cc -o program`

# Input/Output
- `stdout, stdin, stderr`- three streams
- To interact with these: Use `cout, cin, and cerr`
- To interact with these: Use `cout, cin, and cerr`
  - Read from `stdin: cin >> x;`
  - Print to `stderr`: `cerr << "Error, bad" << endl;`

### Example: Read the ints, add them, print back

```cpp
import <iostream>;
using namespace std;

int main(){
    int x, y;
    cin >> x >> y;
    cout<< +y<<endl;
}
```

- Characters:
  - `cin >> x`- "get from"
  - `cout << y`- "put to" operator
- Arrows point in direction of information flow
- `Cin` ignores all white space by default

### How can this go wrong?

1. User gives us a non integer input
2. User supplies EOF(using Ctrl-D)
3. What if they are too big?
- `cin` maintains: Fail bit and an eof bit
  - Fail bit is set to whenever a read fails
  - EOF bit is set to true whenever we run out of inputs
  - `cin.fail()`- returns a failbit status
  - `cin.eof()- returns eof bit`

- If user inputs non integer, `X` will be set to zero due to `cin.fail()`, y will not be anything due to failbit not resuming

### Example: Read integers from `stdin` until failure

- Failure due to EOF or non valid integer

```cpp
int main(){
    int i;
    while(true){
        cin >> i;
        if(cin.fail()) break;
        cout << i << endl;
    }
}
```

^969fb8
- One improvement: Implicit conversion from `cin` to `bool`
- Conversion returns inverse of fail bit
- if `(cin) => succeeds`, `cin` is in a good state
- if`(!cin) => succeeds`, `cin` is in a bad state

```cpp
int main(){
    int i;
    while (true){
        cin >> i; // Returns cin
        if(!cin) break;
        cout << i << endl;
    }
}


// Simplified version
int main(){
    int i;
    while (true){
        if(!(cin >> i)) break;
        cout << i << endl;
    }
}

// Even simplified further(Simplest Version)
int main(){
    int i;
    while(cin >> i){
        cout << i << endl;
    }
}   
```

- In C, >> and << are not operators to read input or print output, they are **bitshift operators**
  - Manipulate the binary of integers
```c
int x = 21;
printf("%d \n", x >> 3); // prints 2
```
- Will shift `x`s bits by 3
  - 21 = 10101
  - 21 >> 3 = 2
- In C++, dependent on context. LHS: int-bit shift, LHS: Stream- printing/reading input
- This is an example of overloading
  - `>>`, `<<` operators differ depending on context
- This applies to operators and functions
- With ints: 21 >> 3- returns an int
- What about `cin` >> x, `cout` << y?
- Example: cout << "hello" << "world" << endl;
  - First, hello, world, new line
  - Implicit brackets like so:
	  - `((cout << "hello") << "world") << endl;`
	  - `cout <<"hello"` returns cout and the rest simplify `cout << "world" << endl;`