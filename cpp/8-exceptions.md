# 5 Exceptions

## Throw

```cpp
#include <stdexcept>
throw std::runtime_error{"whoopsie"};
```

## Try-Catch

```cpp
try{
	// ...
} catch (const std::runtime_error& e) {
	printf("We got some exception: %s\n", e.what());
}
```

## Inheritance Basics

```cpp
struct Pet {
	char name[100];
};

struct Dog : Pet {
	int amountBarked;
};
```

## Standard Exception Classes

### Base Class

```cpp
std::exception;
```

### Runtime Errors

```cpp
std::runtime_error : std::exception;
// the system encountered an error. Contains error code and condition
std::system_error : std::runtime_error;
// arithmetic overflows and underflows
std::underflow_error : std::runtime_error;
std::overflow_error : std::runtime_error;
```

### Logic Errors

```cpp
std::logic_error : std::exception;
// invalid input range, e.g. negative square root
std::domain_error : std::exception;
// general unexpected arguments
std::invalid_argument : std::exception;
// maximum size constraint violations
std::length_error : std::exception;
// value not in expected range, bounds-checking
std::out_of_range : std::exception;
```

- Errors caused by bad parameters
- Can be avoided by more careful programming
- And validation

### Others

```cpp
// casting not possible
std::bad_cast : std::exception;
// allocation failed (out-of-memory)
std::bad_alloc : std::exception;
```

## Rethrow

```cpp
catch (const std::system_error& ex) {
	if(ex.code()!= std::errc::permission_denied) {
		throw; // better: throw a custom exception
	}
	// get permission and retry
}
```

## Custom Exception

```cpp
struct custom_error : std::exception {

};
```

## Noexcept

```cpp
bool isPositive(int x) noexcept {
	return x > -1;
}
```

Marks a method that can under no circumstances throw an exception.
- allows compiler optimizations like "Move Semantics"
- but your program terminates if an exception is encountered

## Exception Handler & Call Stack
- exception falls through stack
- to next handler
- no handler -> terminate

## Throwing in Destructors

```cpp
#include <cstdio>
#include <stdexcept>

struct Foo {
	~Foo() {
		throw std::runtime_error("lol.");
	}
};

int main() {
	{
		try {
			{
				Foo foo;
			}
		}
		catch (const std::runtime_error e) {
			printf("caught: %s\n", e.what());
		}
	}
	return 0;
}
```

Uncaught exceptions leaving class destructors terminate your program. Period.

## Exception Performance
- no overhead as long as no exception is thrown
- on error: exception overhead is compensated by extra help debugging

## Alternatives

### Condition Checks

```cpp
void Pop() {
	// return if empty instead of risking faulty code:
	if(count == 0) return;
	// ...
}
```

### Response with Error Codes

```cpp
struct Unit{};

enum class ErrorCodes {
	Success,
	TooManyMonsters,
	InvalidId
};

struct SpawnResult {
	Unit* unit;
	ErrorCodes errorCode;
	bool success() {
		return errorCode == ErrorCodes::Success;
	}
};

bool tooManyMonsters() {
	return true;
}

bool monsterExists(char* id) {
	return false;
}

Unit* createMonster(char* id) {
	return nullptr;
}

SpawnResult spawnMonster(char* id) {
	if (tooManyMonsters()) return SpawnResult{ nullptr, ErrorCodes::TooManyMonsters };
	if (!monsterExists(id)) return SpawnResult{ nullptr, ErrorCodes::InvalidId };
	return { createMonster(id), ErrorCodes::Success };
}

int main() {
	char id[]{ "MonsterId" };
	SpawnResult spawnResult = spawnMonster(id);
	if (!spawnResult.success()) {

	}
}
```

