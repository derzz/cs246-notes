# Streams
```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;
int main(int argc, char** argv){
	ifstream file{argv[1]};
	string temp;
	while(getLine(file, temp)){
		int counter = temp.length();
		char c = counter;
		cout << counter;
	}
}
```
#