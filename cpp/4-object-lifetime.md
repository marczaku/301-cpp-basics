# 4 Object Lifetime

1. The object’s storage duration begins, and storage is allocated.
2. The object’s constructor is called.
3. The object’s lifetime begins.
4. You can use the object in your program.
5. The object’s lifetime ends.
6. The object’s destructor is called.
7. The object’s storage duration ends, and storage is deallocated.

## Automatic Storage Duration

```cs
void addNumbers(int a, int b) {
	int result = a+b;
	return result;
}
```

What Objects:
- objects defined within a scope
- "local variables"

Storage Duration:
- automatically allocated at the beginning of the enclosing scope
- automatically deallocated at the end of the enclosing scope

In above example, `a`, `b` and `result` are automatically allocated when the function is invoked and automatically deallocated just before the function returns

## Static Storage Duration

```cpp
#include <cstdio>

int globalCounter{0};
static int staticGlobalCounter{0};

void runCounters() {
	static int staticLocalCounter{0};
	globalCounter++;
	staticGlobalCounter++;
	staticLocalCounter++;
	printf("globalCounter: %d, staticLocalCounter: %d, staticGlobalCounter: %d\n", globalCounter, staticLocalCounter, staticGlobalCounter);
}

int main() {
	runCounters();
	runCounters();
	runCounters();
}

```

What Objects:
- declared at namespace scope (including global namespace)
- declared with `static` or `extern` keywords

Storage Duration:
- automatically allocated on program start
- automatically deallocated on program exit

In above example, `globalCounter`, `staticGlobalCounter` and `staticLocalCounter` all exist with only one instance in memory and allocated as long as the program runs.

### Difference between the statics
- al have a static storage duration
- `static` global has an internally visible linker-symbol
- global has an externally visible linker-symbol
- `static` local has a visibility limited by its scope

### Statics in Classes

```cpp
#include <cstdio>

struct Battery {
	int power;
	static int totalPower;
	Battery(int power) : power{power}
	{
		chargeTotalBattery(power);
	}
	static void chargeTotalBattery(int power) {
		totalPower += power;
		printf("Total Battery Power: %d\n", totalPower);
	}
};

int Battery::totalPower = 200;

int main() {
	Battery a{25};
	Battery b{30};
	Battery::chargeTotalBattery(50);
	printf("In the end, we have a power of: %d\n",Battery::totalPower);
}
```

- accessible through `TypeName::memberName`
- fields only initializable from global scope
  - unless they're `const`

## Thread Storage Duration

```cpp
#include <cstdio>

struct Battery {
	int power;
	static thread_local int totalPower;
	static thread_local bool threadLocalInitialized;
	Battery(int power) : power{power}
	{
		chargeTotalBattery(power);
	}
	static void chargeTotalBattery(int power) {
		if(!threadLocalInitialized)
		{
			threadLocalInitialized = true;
			totalPower = 200;
		}
		totalPower += power;
		printf("Total Battery Power: %d\n", totalPower);
	}
};

int main() {
	Battery a{25};
	Battery b{30};
	Battery::chargeTotalBattery(50);
	printf("In the end, we have a power of: %d\n",Battery::totalPower);
}
```

What Objects:
- declared with `thread_local` keyword

Storage Duration:
- automatically allocated on thread start
- automatically deallocated on thread exit

In above example, `totalPower` and `threadLocalInitialized` are allocated separately on each thread and deallocated for each thread when it ends.

## Dynamic Storage Duration

```cpp
#include <cstdio>

struct Battery {
	int power;
	static thread_local int totalPower;
	Battery(int power) : power{power}
	{
		printf("Battery()\n");
		chargeTotalBattery(power);
	}
	~Battery() {
		printf("~Battery()\n");
	}
	static void chargeTotalBattery(int power) {
		totalPower += power;
		printf("Total Battery Power: %d\n", totalPower);
	}
};
thread_local int Battery::totalPower{200};

int main() {
	Battery* battery{nullptr};
	for(int i = 0; i < 3; i++) {
		{
			printf("Do you want to create a battery? [y/n]\n");
			fflush(stdin);
			char answer = getchar();
			if(answer == 'y') {
				battery = new Battery{10};
			}
		}
		{
			printf("Do you want to delete a battery? [y/n]\n");
			fflush(stdin);
			char answer = getchar();
			if(answer == 'y') {
				delete battery;
				battery = nullptr;
			}
		}
	}
	printf("In the end, we have a power of: %d\n",Battery::totalPower);
}
```

What Objects:
- explicitly allocated using the `new` keyword

Storage Duration:
- allocated on `new`
- deallocated on `delete` or `delete[]`

In above example, `battery` is allocated and deallocated explicitly only, if the user approves it. 

### new

When the `new` expression executes, the C++ runtime 
- allocates memory to store a `Battery`-object
- invokes the class's constructor
- returns its pointer `Battery*`

### delete
When the `delete` expression executes, the C++ runtime
- invokes the class's destructor
- deallocates the object's memory
- returns `void`

Note: The Memory does not get cleaned up
- Bug: Use after Free
- You might use an object that's already been deleted
- It doesn't become obvious anytime soon, because the values look good
- But when a new object gets allocated in the same memory address
- Things go down-under. Connection between `delete` and the Bug appearing is unclear

### Dynamic Arrays

```cpp
#include <cstdio>

struct Battery {
	int power;
	static thread_local int totalPower;
	Battery(int power) : power{power}
	{
		printf("Battery()\n");
		chargeTotalBattery(power);
	}
	~Battery() {
		printf("~Battery()\n");
	}
	static void chargeTotalBattery(int power) {
		totalPower += power;
		printf("Total Battery Power: %d\n", totalPower);
	}
};
thread_local int Battery::totalPower{200};

int main() {
	printf("How many Batteries do you want? [0-9]\n");
	fflush(stdin);
	char answer = getchar();
	int answerNum = answer - '0';
	Battery** batteries {new Battery*[answerNum]};
	for(int i = 0; i < answerNum; i++) {
		batteries[i] = new Battery{10};
	}
	for(int i = answerNum-1; i > -1; i--) {
		delete batteries[i];
	}
	delete[] batteries;

	printf("In the end, we have a power of: %d\n",Battery::totalPower);
}
```

## Memory Leaks
- If you allocate memory
- But forget to deallocate it
- Resources are lost FOREVER
- Or until your program exits (whichever happens sooner)

## Tracing Object Life Cycle

```cpp
#include <cstdio>

struct Tracer {
  const char* const name;
  Tracer(const char* name) : name{ name }➋ {
    printf("%s()\n", name);
  }
  ~Tracer() {
    printf("~%s()\n", name);
  }
};

static Tracer t1{ "Static" };
thread_local Tracer t2{ "Thread-local" };

int main() {
  printf("Step 1\n");
  Tracer t3{ "Automatic" };
  printf("Step 2\n");
  const auto* t4 = new Tracer{ "Dynamic" };
  printf("Step 3\n");
}
```

## Exercise: String-Class!

Please do this exercise. We will continue working on this!

### private Members
- `xxx length;`
- `xxx buffer;`
- `xxx maxSize;`

### public Members
- `ctor(xxx maxSize)`
  - add a log so you see that an empty string gets constructed
  - create buffer
  - initialize length & maxSize
- `ctor(xxx defaultText, xxx maxSize)`
  - add a log so you see that a non-empty string gets constructed
  - create buffer
  - initialize length & maxSize
  - append defaultText
- `~()`
  - add a log so you see what string gets deconstructed
  - delete buffer
- `append(xxx text)`
  - checks for maxSize! -> exception
  - adds the given text to the string's buffer
  - updates length
- `appendLine(xxx text)`
  - checks for maxSize! -> exception
  - adds the given text + line break to the string's buffer
  - updates length
- `void print()`
  - prints the string that's currently buffered
- `xxx getString()`
  - returns a c-style string for the currently buffered contents
