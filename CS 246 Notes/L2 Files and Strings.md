# Read and Print all integers from `stdin`, skipping invalid inputs

```cpp
//Read and Print all ints from stdin, skipping invalid inputs

int main(){
    int i;
    while(true){
        if(cin >> i) cout << i << endl;
        else{
            if(cin.eof()) break; // eof would be ctrl-d for example
            cin.clear();
            cin.ignore();
        }
    }
}
```

- `cin.clear()` resets the fail bit, tell `cin` that you're good to read from it again
	- Allows us to read from `cin` again
- `cin.ignore()` discards one character from the character stream
- `Input abc 123` , skips `abc` and will read 123
# I/O Manipulator(Stream Modifiers)
- `cout << 95 << endl; // prints 95`
	- What if we want to print in hexadecimal?
- Using I/O manipulator(Stream modifier)
	- `cout << hex << 95 << endl; //print 5f`
- Belong to library `<iomanip>`
	- `cin/cout` are global variables- good practice to reset any modifiers when you're done
  - All numbers print in hexadecimal until reset: `cout << dec;`
	  - Just resetting, not printing anything

# Strings
- In C, strings are **sequences of characters**, terminated by `\0`
- Manage our own memory, save space for `\0`, don't overwrite it!
- In C++: There is a string type: `std::string`
	- Found in library `<string>`
- No longer need to manage your own memory! No need to worry about `\0`
- `String S = "Hello";`
	- `"Hello"` is a string literal from C(`char*` with a null terminator, stored in ROM[Read only memory])
	- The C++ String, conversion happens
## Operations:
- Comparison: `S1 == S2, S1 != S2`
- Sorting in lexicographical order: `S1< S2` or `S1 >= S2`
- Length: `S.length()`
	- Time Complexity: `O(1)`
- Concatenation: `S1 + S2`, `S3 += S2`
- Chars: `s[0], s[1], s[2]`- individual chars of string

## Examples

```cpp
int main(){
    string s;
    cin >> s; // Read one "word" from Stdin, word- sequence of non whitespace seperated characters
    cout << s << endl;
}
```

- If string must have white space, use `getline(Cin, S)` -> Reads Rest of a line into S;
- `cin` is of the type `std::istream`
- `cout` is of type `std::ostream`
  - These are examples of abstractions
    - Using these "interfaces" to perform different functionalities
    - These concepts can be used for different applications
- `istream`provides `>>` as an interface to "read from"
- `ostream` provides `<<` as an interface to "write to"

## Files
- For files: `ifstream`- read from a file
- `ofstream`- write/print to a file
- Anything that can be done with an `istream/ostream` can be done with `ifstream/ofstream`

```cpp
// File access in C
int main(){
    char S[256];
    FILE* f = fopen("file.txt", "r");
    while(true){
        fscanf(f, "%255S", S);
        if(feof(f)) break;
        printf("%s\n", s)
    }
    fclose(f);
}

// File access in C++
int main(){
    string s;
    ifstream f{"file.txt"}; // Creates ifstream f, opens file.txt
    while(f >> s) cout << s << endl; // Reads words from f, prints each on a new line
} // File is closed automatically when main ends
```

- Another application of `istream`  `ostream` abstraction, strings, library`<sstream>`;)
	- `std:iostringstream`: Allows us to print to a string(with formatting)
	- `std: istringstream`: Allows us to "read from" a string
- Example: Convert an int to a String
  
  ```cpp
  string intToString(int n){
      ostringstream oss;
      oss << n;
      return oss.str(); // Gives me a string containing everything printed to the ostringstream
  }
  ```

- Example: Printing all ints from stdin, ignore non-ints
  
  ```cpp
  int main(){
      String s;
      while (cin >> s){// Read words from cin
          int i;
          // iss is a variable declaration in if statement
          if(isstringstream iss {s}; iss >> i) cout<<i<endl;
      }
  }
  ```
  
- Note: Behavior slightly differs
- Example: ab12cd -> prints nothing in new version
	- Prints 12 in original
- Example, ab12cd, prints nothing in new version, prints 12 in original
- Don't need to clear `issis` fail bits, reinitialized each loop
