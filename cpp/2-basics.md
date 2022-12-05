# 2 Basics

## Integers

| Type | Signed | Win32 | Unix32 | Win64 | Unix64 | Printf |
|------|--------|-------|--------|-------|--------|--------|
| short | Yes | 2 | 2 | 2 | 2 | %hd |
| unsigned short | No | 2 | 2 | 2 | 2 | %hu |
| int | Yes | 4 | 4 | 4 | 4 | %d |
| unsigned int | No | 4 | 4 | 4 | 4 | %u |
| long | Yes | 4 | 4 | 4 | 8 | %ld |
| unsigned long | No | 4 | 4 | 4 | 8 | %lu |
| long long | Yes | 8 | 8 | 8 | 8 | %lld |
| usigned long long | No | 8 | 8 | 8 | 8 | %llu |

- The sizes of numeric types depend on the OS and the Compiler
- Your application can behave inconsistent

### Guaranteed sizes

```cpp
#include <cstdint>
int8_t a;
int16_t b;
int32_t c;
int64_t d;
uint8_t e;
```

- provides guaranteed sizes

### Literals

```cpp
int a = 0b11; // binary
int b = 011; // octal
int c = 11; // decimal
int d = 0x11; // hexadecimal
int e = 1'000'000; // improve readability
```

```cpp
int a = 1234;
unsigned int a = 1234u;
long a = 1234l;
unsigned long a = 1234ul;
long long a = 1234ll;
unsigned long long a = 1234ull;
```

- Numeric literals per default have the smallest fitting type
  - e.g. 111111 can be stored in `int`

### Printf

```cpp
unsigned int a = 255;
printf("%c, %o, %u, %x", a, a, a, a);
```

## Floating-Point Types

```cpp
float f = .3f;
double d = .3;
long double ld = .3l;
```

- `float` often 4 bytes
- `double` often 8 bytes
- `long double` often same size as `double`

### Literals

```cpp
float d = 10.3f;
float e = .3f;
float f = 6.3e+12;
float g = 6.3e-12;
```

### Printf

```cpp
float f = .0003f;
printf("%f, %e, %g", f, f, f);
```

- `%f`: decimal digits
- `%e`: scientific notation
- `%g`: shorter of either above

```cpp
double d = 0.003d;
long double ld = 0.0003l;
printf("%lf, %Lf", d, ld);
```

## Characters

```cpp
char ascii = 'a';
char16_t utf16 = u'a';
char32_t utf32 = U'a';
wchar_t unicode = L'a';
```

### Escaping

- Newline: '\n'
- Horizontal Tab: '\t'
- Vertical Tab: '\v'
- Backspace: '\b'
- Carriage Return: '\r'
- Alert: '\a'
- Backslash: '\\'
- Single Quote: '\''
- Double Quote: '\"'
- Null Character: '\0'

### Unicode

```cpp
wchar_t a = '\u0041'; // 4-digit unicode
wchar_t beer = U'\U0001F37A'; // 8-digit unicode
```

### Printf

```cpp
#include <cstdio>

int main() {
  char x = 'M';
  wchar_t y = L'Z';
  printf("Windows binaries start with %c%lc.\n", x, y);
}
```

## Boolean

```cpp
bool b = false; // 0
bool c = true; // 1
```

### Printf

```cpp
#include <cstdio>

int main() {
  bool b1 = true;  ➊ // b1 is true
  bool b2 = false; ➋ // b2 is false
  printf("%d %d\n", b1, b2); ➌
}
```

## Comparison Operators
- Two arguments
- Return Type `bool`
- `==`, `!=`, `<`, `<=`, `>`, `>=`

## Types
- Unary
- Binary
- Ternary

## Logical Operators

- `!`, `&&`, `||`

## std::byte

Later

## size_t

```cpp
size_t floatSize = sizeof(float);
```

- can store the maximum size of a theoretically possible object of any type (including array)

### Printf

```cpp
printf("float: %zu", sizeof(float));
```

## void

```cpp
void sayHello() {
	printf("Hello!");
}
```

## Arrays

```cpp
int numbers[100];
```

### Initialization

```cpp
int array[] = {1, 2, 3, 4};
```

### Index Operator

```cpp
printf("Second element: %d", array[1]);
```

## for

You should not use `int`, since it does not guarantee to be able to store an Array of `MAX_SIZE`:

```cpp
for(int i = 0; i < 10; ++i) {
	printf("%d\n", i);
}
```

Instead use `size_t`, especially, if you want to support any edge case:

```cpp
for(size_t i = 0; i < 5; i++) {
	printf("%d\n", array[i]);
}
```

## Range-Based For

```cpp
for(int number : array) {
	printf("%d\n", number);
}
```

## Number of Elements in Array

The hard, manual way:

```cpp
size_t count = sizeof(array) / sizeof(int);
```

Better:

```cpp
#include <array>
size_t count = std::size(array);
```

## C-Style Strings

```cpp
char text[] = "Hello world.";
char16_t chinese[] = u"\u4e66\u4e2d";
char32_t utf32[] = U"abc";
wchar_t unicode[] = L"abc";
```
- Null-Terminated String
- Means, it ends with first occurence of `'\0'` or `0`

### Multiline

```cpp
char text[] = "hello "
	"world "
	"this is " "c++.";
```

### Printf

```cpp
printf("Here you go: %s", text);
```

## Eumeration Types

### Scoped Enum (Modern, C++ Style)

```cpp
enum class ChessPiece {
	Rook,
	Pawn,
	Queen,
	...
};
```

```cpp
ChessPiece rook = ChessPiece::Rook
```

### Unscoped Enum (Old, C-Style)

```cpp
enum ChessPiece {
	Rook,
	Pawn,
	Queen,
	...
};
```

```cpp
ChessPiece rook = Rook;
```

- Less safe to use, because values can be accessed without preceding type name `ChessPiece::`
- Use for backwards-compatibility with C applications

## Switch

```cpp
int number = 1;
switch(number) {
	case 0: {
		// option a 
	} break;
	case 1: {
		// option b
	} break;
	default: {
		printf("Error!");
	}
}
```

Performance: Compiler can generate:
- jump table: no comparison needed
- binary decision tree: optimized if..else with binary search
- worst case: same as if..else

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

## EXERCISE: STRING WITH ALL LETTERS
- Create a String with all letters A-Z using a loop
## EXERCISE: CALCULATOR 
- Make a Calculator Application
