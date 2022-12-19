# 4 Functions and Headers

IPO
- Input: Parameters
- Processing: Body
- Output: Return Type + `return` Keyword

## Definition

```cpp
int addNumbers(int a, int b) {
	return a + b;
}
```

## Invocation

```cpp
int result = addNumbers(3, 5); // 8
```

## ORDER MATTERS!

```cpp
void foo() {
	bar(); // ERROR: use of undeclared identifier 'bar'
}

void bar() {
	foo();
}
```

## Exit Code

```cpp
int main() {
	return 5;
}
```

Generally:
- `0` - SUCCESS
- other codes - ERROR CODES


## EXERCISE: STEP FUNCTION (SIGN)
- Make a function that returns -1/0/+1 for negative/zero/positive numbers
## EXERCISE: ABS FUNCTION
- Make a function that always returns the positive of a number
## EXERCISE: GAME MENU
- Make a small Console Game Menu. Each Menu should be a function of its own. Ask the user for what menu to open next.
  - MAIN MENU
    - `New Game`: open GAME
	- `Settings`: open OPTIONS
  - OPTIONS
    - `Load Game`: open GAME
	- `Back`: MAIN MENU
  - GAME
    - `Next Level`: open GAME
	- `Back`: MAIN MENU
