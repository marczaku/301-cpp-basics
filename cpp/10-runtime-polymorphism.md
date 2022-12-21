# 7 Runtime Polymorphism
Inheritance-Based Polymorphism like in C#

## Problem without Polymorphism

```cpp
#include <cstdio>

struct BaseLogger{
	void Log(const char* message)
	{
		printf("BaseLogger: %s\n", message);
	}	
};

struct DerivedLogger : BaseLogger
{
	void Log(const char* message)
	{
		printf("DerivedLogger: %s\n", message);
	}
};

int main()
{
	DerivedLogger logger{};
	BaseLogger& sameLogger = logger;
	logger.Log("This is a DerivedLogger class instance.");
	sameLogger.Log("This is also a DerivedLogger class instance.");
}
```

## Virtual Methods
Allows deriving class to override a method implementation

```cpp
#include <cstdio>

struct BaseLogger{
	virtual void Log(const char* message)
	{
		printf("BaseLogger: %s\n", message);
	}	
};

struct DerivedLogger : BaseLogger
{
	void Log(const char* message) override
	{
		BaseLogger::Log(message);
		printf("DerivedLogger: %s\n", message);
	}
};

int main()
{
	DerivedLogger logger{};
	logger.Log("Hello, World.");
}
```

## Abstract Method

```cpp
struct ILogger{
	virtual void Log(const char* message) = 0;
};
```

## Defining Interfaces

You define interfaces in C++ by defining a purely virtual class:

```cpp
struct ILogger {
	virtual ~ILogger() = default;
	virtual void Log(const char* message) = 0;
};
```

## Virtual Destructor

If you don't define a virtual constructor in the base class, the runtime can't invoke the correct Destructor if you do:

```cpp
ILogger logger = new ConsoleLogger{};
delete logger;
```

## Implementing Interfaces

```cpp
struct ConsoleLogger : ILogger {
	void Log(const char* message) override {
		printf("Log: %s", message);
	}
};
```

## Using Interfaces

### Constructor Injection

```cpp
struct Player {
	Player(ILogger& logger) : logger{logger} {}

	void Spawn() {logger.Log("Player spawned!");}
private:
	ILogger& logger;
};
```

```cpp
int main()
{
	ConsoleLogger logger{};
	Player player{logger};
	player.Spawn();
}
```

### Property Injection

```cpp
struct Player {
	void SetLogger(ILogger* logger){
		this->logger = logger;
	}
	void Spawn() {
		if(logger)
			logger->Log("Player spawned!");
	}
private:
	ILogger* logger{};
};
```

```cpp
int main()
{
	ILogger* logger = new ConsoleLogger{};
	Player player{};
	player.SetLogger(logger);
	player.Spawn();
}
```

### Or Both

```cpp
struct Player {
	Player(ILogger* logger) : logger{logger} {}
	void SetLogger(ILogger* logger){
		this->logger = logger;
	}
	void Spawn() {
		if(logger)
			logger->Log("Player spawned!");
	}
private:
	ILogger* logger{};
};
```