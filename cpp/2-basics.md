# 2 Basics

## Comments

### C-Style Comments

```cpp
/*
 * multi-line comment
 */
```

Careful: Nesting Comments does not work!
```cpp
/*
void foo() {
	int a = 3;
	int b = 2;
	/* int c = 5; */
	return 5;
}
*/
```

### Cpp-Style Comments
```cpp
// single-line comment
int number = 12; // span the rest of the line
```

## Output

### Write Line: Puts

Prints the given string and a newline to the console.

```cpp
puts("Hello, World.");
```

### Write: Printf

```cpp
printf("Hello, World.");
```

### Write Format: Printf

```cpp
printf("Ten %d, Twenty %d, Thirty %d", 10, 20, 30);
```

## Variables

### Declaration

```cpp
int health;
int x, y, z;
```

#### Global

```cpp
int health;
int main() {

}
```

#### Local

```cpp
int main() {
	int health;
}
```

### Assignment Operator
```cpp
int a;
a = 5;
```

- right-associative (executes right to left)

### Initialization

```cpp
int health = 100;
int minHealth = 0, maxHealth = 100;
```

### Scope
- Within File
- Below the line where it was declared
- Within enclosing Curly Braces
- Must be unique within Scope

## Basic Data Types

- Object-Oriented (State and Behaviour)
- Definition = Type
- Strongly-Typed

### Integers

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

#### Guaranteed sizes

```cpp
#include <cstdint>
int8_t a;
int16_t b;
int32_t c;
int64_t d;
uint8_t e;
```

- provides guaranteed sizes

#### Literals

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

#### Printf

```cpp
unsigned int a = 255;
printf("%c, %o, %u, %x", a, a, a, a);
```

### Floating-Point Types

```cpp
float f = .3f;
double d = .3;
long double ld = .3l;
```

- `float` often 4 bytes
- `double` often 8 bytes
- `long double` often same size as `double`

#### Literals

```cpp
float d = 10.3f;
float e = .3f;
float f = 6.3e+12;
float g = 6.3e-12;
```

#### Printf

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

### Characters

```cpp
char ascii = 'a';
char16_t utf16 = u'a';
char32_t utf32 = U'a';
wchar_t unicode = L'a';
```

#### Escaping

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

#### Unicode

```cpp
wchar_t a = '\u0041'; // 4-digit unicode
wchar_t beer = U'\U0001F37A'; // 8-digit unicode
```

#### Printf

```cpp
#include <cstdio>

int main() {
  char x = 'M';
  wchar_t y = L'Z';
  printf("Windows binaries start with %c%lc.\n", x, y);
}
```

### Boolean

```cpp
bool b = false; // 0
bool c = true; // 1
```

#### Printf

```cpp
#include <cstdio>

int main() {
  bool b1 = true;  ➊ // b1 is true
  bool b2 = false; ➋ // b2 is false
  printf("%d %d\n", b1, b2); ➌
}
```

### std::byte

Later

### size_t

```cpp
size_t floatSize = sizeof(float);
```

- can store the maximum size of a theoretically possible object of any type (including array)

#### Printf

```cpp
printf("float: %zu", sizeof(float));
```

### void

```cpp
void sayHello() {
	printf("Hello!");
}
```

### Eumeration Types

#### Scoped Enum (Modern, C++ Style)

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

#### Unscoped Enum (Old, C-Style)

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

## Operators

### Operator Types
- Unary `-a`, `+a`, `a++`
- Binary `a+b`, `a*b`, `a<b`
- Ternary `a ? b : c`

### Arithmetic Operators
- `+`, `-`, `/`, `*`, `%`, `++`, `--`

### Comparison Operators
- Two arguments
- Return Type `bool`
- `==`, `!=`, `<`, `<=`, `>`, `>=`

### Logical Operators

- `!`, `&&`, `||`

### Bitwise Operators

- `~`, `&`, `|`, `^`, `>>`, `<<`

### Compound Assignment Operators

- `+=`, `-=`, `*=`, `/=`, `%=`, `|=`, `&=`, `^=`, `<<=`, `>>=`

## Input

### Read Character: getchar

```cpp
printf("Do you want to continue? y/n");
char c = getchar();
{ // clear unparsed characters from the buffer
	char _c;
	while ((_c = getchar()) != '\n' && _c != EOF);
}
switch(c){
	/*...*/
}
```

### Read String: fgets

Later

### Read Format: scanf

```cpp
// required to allow using scanf on Windows
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>

int main() {
    for(int i = 0; i < 5; i++)
    {
        AskAgain:
        // ask for input
        printf("Give me two numbers. e.g. 3, 7\n");
        // declare variable to store the information in
        int num1, num2;
        // read input from the console. Returns the number of successfully parsed arguments 
        int result = scanf("%d, %d", &num1, &num2);
        
        { // clear unparsed characters from the buffer
            char c;
            while ((c = getchar()) != '\n' && c != EOF);
        }

        // if not both numbers were provided, we go back to asking again
        if(result != 2) goto AskAgain;
        // else, we can print the average of both provided numbers:
        printf("The average is %d.\n", (num1+num2)/2);
    }
}
```

## Control Structures

### Jumps
- `goto`, `break`, `continue`

### Branches
- `if`, `switch`

#### Switch

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

### Loops
`for`, `while`, `do..while`

## Expressions

Every expression is a valid C++ statement. Including:

```cpp
a = b = 10 + 2;
a = 10 + 2;
10 + 2;
2;
```

### Expression Short-Cutting

```cpp
int a = 0, b = 0;
if(++a || ++b){
	printf("True!");
}
printf("a: %d, b: %d", a, b);
```

- first sub-expression is already true
- so second sub-expression of `||` doesn't have to be executed
- can be very useful e.g. `if(player && player.canAttack)`
- can also be unintended