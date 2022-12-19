# 3 Classes
## Plain-Old-Data Classes

- C-Compatible
- Highly Efficient to Copy or Move
- Efficiently represented in Memory

### Definition

```cpp
struct Book {
	char name[256];
	int year;
	int pages;
	bool hardcover;
}
```

### Usage

```cpp
int main() {
	Book book;
	book.name = "Harry Potter Part 1";
	book.year = 2000;
	printf("Book %s (%d)", book.name, book.year);
}
```

### Order
- Order of members is maintained
- CPU Register Length causes member alignment
- Rule: Order members from small to large

## Union

- Puts all members into the same place.
- Useful in Low-Level Optimization

### Definition

```cpp
union Variant {
	bool isTrue;
	int number;
	double decimal;
}
```

### Usage

```cpp
Variant v;
v.number = 42;
printf("Nice: %d", v.number);
v.decimal = 2.71828;
printf("Nope: %d", v.number);
```

## Fully-Featured Classes

- Classes which contain Methods
- Allow for Encapsulation

### Methods

- Member Functions
- Have access to all class Members

```cpp
struct Time {
	void addYear() {
		year++;
	}
	int year;
};
```

```cpp
int main() {
  	Time time;
	time.year = 2022;
	printf("Time: %d\n",time.year);
	time.addYear();
	printf("Time: %d\n",time.year);
}
```

### Access Control

- structs are `public` per default:

```cpp
struct Time {
	void addYear() {
		year++;
	}
	bool setYear(int newYear) {
		if(newYear < 1990) return false;
		year = newYear;
		return true;
	}
	int getYear() {
		return year;
	}
private:
	int year;
}
```

- classes are `private` per default:

```cpp
class Time {
	int year;
public:
	void addYear() {
		year++;
	}
	bool setYear(int newYear) {
		if(newYear < 1990) return false;
		year = newYear;
		return true;
	}
	int getYear() {
		return year;
	}
}
```

- there is no other difference between `struct` and `class` in C++!

### Initializing Members

Problem:

```cpp
int main() {
	Time time;
	clock.setYear(1980); // will fail, <1990
	printf("Time: %d\n", time.getYear());
	clock.addYear();
	printf("Time: %d\n", time.getYear());
}
```

### Constructors

```cpp
Time() {
	year = 2022;
}
Time(int in_year) {
	if(!setYear(in_year)) {
		year = 2022;
		// (better: throw an exception)
	}
}
```

```cpp
int main() {
	Time a();
	Time b(2030);
}
```

## Initialization

### Fundamental Type: Zero

```cpp
int a = 0;
int b {};
int c = {};
int d; // uninitialized
```

### Fundamental Type: Arbitrary Value

```cpp
int e = 42;
int f{42};
int g = {42};
int h(42);
```

### PODs

```cpp
struct Pod {
	uint64_t a;
	char b[256];
	bool c;
};

int main() {
	Pod pod1{};
	Pod pod2 = {};
	Pod pod3{42, "Hello"};
	Pod pod4{42, "Hello", true};
	// Pod pod5 = 0;
	// Pod pod6{"Hello", 42}; // invalid types/order
	// Pod pod7(42, "Hello", true); // does not work
}
```

#### Call-By-Value

```cpp
#include <cstdio>

struct Pod {
	int a;
	char b[256];
	bool c;
};

void modify(Pod pod) {
	pod.a = 99;
	pod.c = false;
	printf("Pod changed: %d, %s, %d\n", pod.a, pod.b, pod.c);
}

int main() {
	Pod pod{42, "Hello", true};
	printf("Pod before: %d, %s, %d\n", pod.a, pod.b, pod.c);
	modify(pod);
	printf("Pod after: %d, %s, %d\n", pod.a, pod.b, pod.c);
}
```

Whenever you assign a value to a new variable
- a copy is created
- if you modify that copy, the original remains unaffected

### Arrays

```cpp
int array1[]{1,2,3}; // 1, 2, 3
int array2[5]{}; // 0, 0, 0, 0, 0
int array3[5]{1,2,3}; // 1, 2, 3, 0, 0
int array4[5]; // uninitialized
```

### Fully Featured Classes

```cpp
struct SuchClass {
	SuchClass() {
		printf("(no argument)\n");
	}
	SuchClass(char c) {
		printf("char: %c\n", c);
	}
	SuchClass(int i) {
		printf("int: %d\n", i);
	}
}
```

```cpp
int main(){
	SuchClass s1;
	SuchClass s2{};
	SuchClass s3{'c'};
	SuchClass s4{255};
	SuchClass s5('g');
	SuchClass s6 = {'l'};
	SuchClass s7(); // Function Declaration :o) // most vexing parse
}
```

### Narrowing Conversions

```cpp
float a{1};
float b{2};
int narrowedResult(a/b); // ignored
int result{a/b}; // warning
```

### Initializing Class Members

```cpp
struct Customer{
	bool active = true;
	int probationPeriod{6};
	char name[256] = {"unnamed"};
	// int notPossible(5);
};
```

### Brace yourself
- Always use braced initializers
- Uniform Initialization

## Destructor
- release file handles
- flush network sockets
- free dynamic objects

```cpp
struct Monster{
	~Monster(){
		printf("<dramatic deathrattle>\n");
	}
}
```

```cpp
int main(){
	printf("creating monster.\n");
	{
		Monster monster;
	}
	printf("done.\n");
}
```

## EXERCISE: CALCULATOR CLASS
- Make an Object-Oriented Calculator Application
