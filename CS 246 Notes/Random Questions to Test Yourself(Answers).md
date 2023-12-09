- Answers may not be correct.

- `void printWidth( Box box );` is not defined as a function in Box. Since we can't augment main, we can not make `void printWidth` as `void Box::printWidth`, as main will be unable to call it. Instead, make `void printWidth(Box box)` into `friend void printWidth(Box box)` in the header to allow printWidth to access Box's private variables. 
- Steps of Object Creation
	1. Space is allocated
	2. Superclass constructor is run
	3. MIL is initialized for class constructors
	4. Constructor body runs
- Steps of Object Destruction
	1. Destructor body runs
	2. Object fields that have destructors are called in reverse declaration order
	3. Superclass destructor runs
	4. Memory is freed