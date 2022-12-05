# 3 Pointers

Reference types store the memory addresses of objects in order to write highly efficient programs. Instead of copying information from a to b, it can often directly be written in the correct location and passed on through its address.

## Pointers

Pointers contain both pieces of information needed to interact with information:
- address
- type
  - size
  - way of interpreting it

### Definition

```cpp
int* myPointer;
```

### Addressing Variables

Address-Of Operator `&`

```cpp
#include <cstdio>

int main() {
	int number{};
	printf("number: %d\n", number);
	int* numberAddress = &number;
	printf("&number: %p\n", numberAddress);
}
```

### Address Space Layout Randomization

If you run the program multiple, times, you'll notice some inconsistencies:
```
number: 0
&number: 0x16f57334c
number: 0
&number: 0x16d37f34c
```

- the base memory address is randomized on start-up
- to avoid exploitation e.g. through `return-oriented programs`

### Dereferencing Pointers

Dereference Operator `*`

```cpp
#include <cstdio>

int main() {
	int number{};
	printf("number: %d\n", number);
	int* numberAddress = &number;
	printf("&number: %p\n", numberAddress);
	*numberAddress = 24;
	printf("number: %d\n", number);
	printf("&number: %p\n", numberAddress);
}
```

### Accessing Pointer Members

Member-Of-Pointer Operator `->`

To access a member of a class-pointer, you need to:
- dereference the pointer
- access a member of the pointed-to object

```cpp
#include <cstdio>

struct DateTime {
	int year{1990};
	void addYear(){
		year++;
	}
};

void modify(DateTime* timePtr){
	(*timePtr).year = 2022;
	(*timePtr).addYear();
}

int main() {
	DateTime time;
	DateTime* timePtr = &time;
	modify(timePtr);
	printf("Address of time: %p\n", timePtr);
	printf("Value of time: %d\n", (*timePtr).year);
}
```

This can be done much more elegant using the arrow-operator:
- Before: `(*variablePtr).member`
- After: `variablePtr->member`

```cpp
#include <cstdio>

struct DateTime {
	int year{1990};
	void addYear(){
		year++;
	}
};

void modify(DateTime* timePtr){
	timePtr->year = 2022;
	timePtr->addYear();
}

int main() {
	DateTime time;
	DateTime* timePtr = &time;
	modify(timePtr);
	printf("Address of time: %p\n", timePtr);
	printf("Value of time: %d\n", timePtr->year);
}
```

## Pointers and Arrays
Pointers are similar to Arrays:
- A pointer encodes object type + location
- An array encodes object type + location + number of objects

### Array Decay
Arrays can decay into pointers:

```cpp
int keyToTheUniverse[]{3,6,9};
int* keyPtr = keyToTheUniverse; // points to 3
```

This happens whenever you want to pass on an Array to another variable or function:

```cpp
#include <cstdio>

void printNumbers(int* numbers) {
	// how to access number of elements here?
	printf("Number: %d\n",numbers[0]);
}

int main() {
	int keyToTheUniverse[]{3,6,9};
	printNumbers(keyToTheUniverse);
}
```

### Handling Decay

This is usually solved by passing two arguments on:
- A pointer to the first array element
- The array length

```cpp
#include <cstdio>
#include <array>

void printNumbers(int* numbers, size_t count) {
	// how to access number of elements here?
	for(size_t i = 0; i < count; i++){
		printf("Number: %d\n",numbers[i]);
	}
}

int main() {
	int keyToTheUniverse[]{3,6,9};
	printNumbers(keyToTheUniverse, std::size(keyToTheUniverse));
}
```

This is ubiquitous in C-Style APIs, e.g. Windows and Linux systems.

## Pointer Arithmetic

The following four lines all do the same:

```cpp
int main() {
	int secondElement = keyToTheUniverse[1];
	secondElement = *(keyToTheUniverse+1);
	secondElement = *(1+keyToTheUniverse);
	secondElement = 1[keyToTheUniverse];
}
```

### Adding a scalar to Pointers

When you add a scalar to a pointer, it offsets the pointer address by the pointer type's size

```cpp
int numbers[]{1,2,3};
int* first = numbers; // address of first element
int* second = first+1; // address of second element (first + 4 bytes)
```

Same for Short:

```cpp
short numbers[]{1,2,3};
short* first = numbers; // address of first element
short* second = first+1; // address of second element (first + 2 bytes)
```

Addition is commutative, which means that you can also do:

```cpp
short* second = 1+first;
```

### Using the Bracket-Operator

`pointer[5]` is just short for: `*(pointer+5)`
- first, add 5 to the pointer
  - i.e. move 5 elements down in memory
- then, dereference the pointer to access the value

```cpp
short numbers[]{1,2,3};
short second = numbers[2];
```

This is the same as:

```cpp
short numbers[]{1,2,3};
short second = *(numbers+2);
```

Which is commutative, so the same as:

```cpp
short numbers[]{1,2,3};
short second = *(2+numbers);
```

Which can then be written as:

```cpp
short numbers[]{1,2,3};
short second = 2[numbers];
```

## Pointer Arithmetic is Dangerous

You can access arbitrary elements using the Bracket-Operator.
- this allows any access to any memory address
- unless you access an address without permission by the OS

```cpp
#include <cstdio>
#include <array>

int main() {
	char lower[] = "abcde";
	char upper[] = "ABCDE";

	lower[2] = 'g';
	upper[2] = 'G';

	lower[8] = 'h';
	upper[8] = 'H';

	printf("lower: %s (%p)\nupper: %s (%p)\n", lower, lower, upper, upper);
}
```

Output:

```
lower: Hbgde (0x16fdff3d8)
upper: ABGDE (0x16fdff3d0)
```

## Void-Pointer
If you want to pass on an address, but the type is relevant, you can use `void*`
- it can not be dereferenced (type `void` does not exist)
- pointer arithmetic is impossible (size is unknown)

## Byte-Pointer
If you want to pass on an address of arbitrary data, like reading, writing files, network i/o, encryption, compression, you can use `byte*`
- dereferencing only gives you information of one `byte`
- pointer arithmetic is possible (byte by byte)

## Nullptr
`nulltr` is the value for a pointer to 'nothing'. It is usually used to indicate that something does not exist (yet) or something went wrong.

```cpp
#include <cstdio>
#include <array>

char* names[]{"Alpha", "Beta", "Gamma", "Delta"};

char* chooseName(){
	for(char* name : names){
		printf("Do you like the name '%s'? [y/n]\n", name);
		char answer[2];
		scanf("%1s", answer);
		if(answer[0] == 'y'){
			return name;
		}
	}
	return nullptr;
}

int main() {
	char* chosenName = chooseName();
	if(chosenName == nullptr){
		printf("You're never satisfied. Are you??\n");
	}
	else{
		printf("%s is a wonderful name!", chosenName);
	}
}
```

## Pointers and Conditions

Pointers have an implicit conversion to type `bool`:
- `nullptr` converts to `false`
- any other pointer converts to `true`

Therefore:

```cpp
if(chosenName == nullptr)
// is the same as:
if(!chosenName)
```

And

```cpp
if(chosenName != nullptr)
// is the same as:
if(chosenName)
```

## References
Safer, more convenient version of pointers
- can pass on references/pointers to values (no copy)
- cannot be assigned `nullptr`
- cannot be reseated (reassigned)
- no member-of-pointer operator needed `->`

```cpp
#include <cstdio>

struct DateTime {
	int year{1990};
	void addYear(){
		year++;
	}
};

void modify(DateTime& time){
	time.year = 2022;
	time.addYear();
}

int main() {
	DateTime time;
	modify(time);
	printf("Value of time: %d\n", time.year);
}
```

Cannot be reseated:

```cpp
#include <cstdio>

struct DateTime {
	int year{1990};
	void addYear(){
		year++;
	}
};

int main() {
	DateTime time{2004};
	DateTime time2{2012};
	DateTime& timeRef = time;
	timeRef = time2; // the value of time2 gets copied to time
	time2.year = 12; // 12 gets only assigned to time2
	printf("Value of time: %d\n", time.year);
	printf("Value of time2: %d\n", time2.year);
	printf("Value of timeRef: %d\n", timeRef.year);
}
```

## Reference vs. Pointer
Use Pointers only, when you need to reseat them or because you need `nullptr`. In all other cases, References should be preferred.

## This-Pointer
Sometimes, you need to access the `current object` from a method. As in, the object on which the Method is currently being invoked. e.g. when attacking:

```cpp
#include <cstdio>
struct Unit{
	char* name;
	Unit(char* name){
		// Use case for `this`: name is the name both for the constructor arg as well as the field member
		this->name = name;
	}

	void attack(Unit* target){
		// Use case for `this`: pass on a pointer to ourselves to the attacked Unit
		target->attackedBy(this);
	}

	void attackedBy(Unit* attacker){
		printf("%s got attacked by %s.\n", name, attacker->name);
	}
};

int main(){
	Unit hero{"Hero"};
	Unit monster{"Monster"};
	hero.attack(&monster);
}
```

## Const
- short for "constant"
- i.e. "cannot be movied"
- gives compiler errors if violated

### Const Arguments

```cpp
#include <cstdio>
void printButDoNotModify(const char* name){
	printf("%s", name);
	// name[0] = 'K'; // Won't compile!
}

int main() {
	char* name = "Marc";
	printButDoNotModify(name);
}
```

### Const Methods

The following code won't compile:

```cpp
class DateTime {
	int year{1990};
public:
	DateTime(int year){
		this->year = year;
	}
	void addYear() {
		year++;
	}
	int getYear() {
		return year;
	}
};

void printButDoNotModify(const DateTime& dateTime){
	printf("Year: %d", dateTime.getYear());
}

int main() {
	DateTime time{1925};
	printButDoNotModify(time);
}
```

Error: `this` argument to member function `getYear` has type `const DateTime`, but function is not marked `const`

Reason is, that only `const` methods can be invoked on `const` variables.

#### Definition

```cpp
int getYear() const {
	return year;
}
```

#### Usage

```cpp
int getYear() const {
	return ++year; // Won't compile - error: cannot assign to non-static data member within const member function 'getYear'
}
```

### Const Members

```cpp
struct Unit {
	const int maxLevel = 100;
	DateTime birthDate;
}
```

#### Initializer List

```cpp
#include <cstdio>
class DateTime {
	int year{1990};
public:
	DateTime(int year){
		this->year = year;
	}
	void addYear() {
		year++;
	}
	int getYear() const {
		return year;
	}
};

struct Unit {
	const int maxLevel = 100;
	const int id;
	const DateTime birthDate;
	Unit(int id, int year) : id{id}, birthDate{year}{

	}
};

int main(){
	Unit unit{1337, 1990};
	printf("Unit #%d, born %d", unit.id, unit.birthDate.getYear());
}
```

- member initializations execute before constructor's body
  - ensures validity of all members before constructor code
  - members initialize only once

## Auto Type Deduction

```cpp
auto number{12};
auto isAlive{true};
auto name{"Marc"};
```

### Reference Types

```cpp
auto number{42};
auto& numberRef = number;
auto* numberPtr = &number;
```

## EXERCISE: LINKED LIST