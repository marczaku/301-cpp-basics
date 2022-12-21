# 8 Compile-Time Polymorphism
Similar to Generics in C#

## Defining a Template Class

```cpp
template<typename THealth>
struct Player
{
	THealth health = 20;
	Player(THealth health) : health{health} {}
	void DealDamage(THealth health)
	{
		this->health -= health;
	}
};
```

## Using a Template Class

```cpp
int main()
{
	Player<int> player{10};
	player.DealDamage(5);
	printf("Player Health: %d\n", player.health);
	Player<float> player2{100.2f};
	player2.DealDamage(59.99f);
	printf("Player2 Health: %f\n", player2.health);
}
```

### Invalid Type Arguments

```cpp
struct Foo{};
int main()
{
	Player<Foo> player{Foo{}};
	player.DealDamage(Foo{});
}
```

## Defining a Template Function

```cpp
template<typename TMessage>
void PrintMessage(const TMessage& message)
{
	printf("Print: %s\n", message.ToString());
}
```

## Using a Template Function

```cpp
struct ErrorMessage
{
	char* ToString() const
	{
		return new char[]{"There has been an error."};
	}
};
int main()
{
	PrintMessage(ErrorMessage{});
}
```

## Samples: Named Conversion Functions

### const_cast

Can be used to convert a const object to a non-constant one:

```cpp
void TakeDamage(const Unit& attacker, int damage) {
	Unit& nonConstAttacker = const_cast<Unit&>(attacker);
	attacker.TakeDamage(*this, 5);
}
```

### static_cast

Reverses an implicit conversion

```cpp
void Attack(Unit* target){
	Ghost* ghost = static_cast<Ghost*>(target);
}
```

### reinterpret_cast

Allows to reinterpret memory as a different type, e.g. to look at the bytes of a `float`:

```cpp
#include <cstdio>

int main()
{
    float f = 1;
    printf("%x", *reinterpret_cast<int*>(&f));
}
```

### EXERCISE: narrow_cast

> In Bjarne Stroustrup's "The C++(11) programming language" book, section "11.5 Explicit Type Conversion" you can see what it is.
> 
> Basically, it is a homemade explicit templated conversion function, used when values could be narrowed throwing an exception in this case, whereas static_cast doesn't throw one.
> 
> It makes a static cast to the destination type, then converts the result back to the original type. If you get the same value, then the result is OK. Otherwise, it's not possible to get the original result, hence the value was narrowed losing information.

### EXERCISE: average
- Implement a function that calculates the average of an array of doubles
- Implement the same function for type int
- Now, make the function runtime-polymorphic

## Template Type Deduction

```cpp
int a = 255;
std::byte b = narrow_cast<int, std::byte>(a);
// can be reduced to:
std::byte b = narrow_cast<std::byte>(a);
```

```cpp
int a[]{1,2,3,4,5,6};
int average = average<int>(a/*...*/)
// can be reduced to:
int average = average(a);
```

## Type Checking in Templates

```cpp
template<typename T>
T sqr(T value){
    return value * value;
}

int main() {
    int i = 5;
    sqr(i);
    sqr(&i);
}
```

- typechecking happens during template instantiation
  - step during compilation
- `sqrt<int>` compiles, since operator `int * int` is defined
- `sqrt<int*>` does not compile, since operator `int* * int*` is not defined

Similar to duck-typing
> If it looks like a duck and quacks like a duck, then it must be type duck.

## Array Size Checking Through Templates

```cpp
#include <cstdio>

template<size_t N>
void print_all(int (&a)[N])
{
    for(const auto b : a)
    {
        printf("%d, ", b);
    }
}

int main()
{
    int numbers[]{1, 2, 3, 4, 5};
    print_all(numbers);
}
```

## Concepts

### Predefined Concepts
[You can find them here](https://en.cppreference.com/w/cpp/concepts)

### Custom Concepts

```cpp
template <typename T>
concept multipliable = requires (T a, T b) {
    a*b;
};
```

The expression needs to compile. It is not actually evaluated.

### Using Concept as Type Constraints
Postfix:
```cpp
template<typename T>
	requires multipliable<T>
T square(T a)
{
    return a * a;
}
```

Inline:
```cpp
template<multipliable T>
T cubed(T a)
{
    return a * a * a;
}
```

```cpp
int main()
{
    int number = 3;
    auto result = square(number);
    auto result2 = square(&number);
}
```

### Using Concept as Parameter Type Constraint

Postfix:
```cpp
size_t index_of_max(auto* values, size_t count)
	requires std::totally_ordered<decltype(values)>
{
    size_t max_index{};
    for (size_t i{1}; i < count; ++i)
    {
        if(values[i] > values[max_index]) max_index = i;
    }
    return max_index;
}
```

Inline:
```cpp
size_t index_of_min(std::totally_ordered auto* values, size_t count)
{
    size_t min_index{};
    for (size_t i{1}; i < count; ++i)
    {
        if(values[i] < values[min_index]) min_index = i;
    }
    return min_index;
}
```

### Using Concept as

### Concept Resolution
The function specialization with the most specific constraints is chosen.\

#### Problem:

```cpp
template <typename T>
concept has_x = requires (T v) {
    v.x;
};
template <typename T>
concept coord = requires (T v) {
    v.x;
    v.y;
};
void function(has_x auto x) {}
void function(coord auto x) {}

struct X {
    int x;
};

struct Y {
    int x;
    int y;
};

int main() {
    function(X{}); // OK, only one viable candidate
    function(Y{}); // Fails, ambiguous
}
```

#### Solution:

```cpp
template <typename T>
concept has_x = requires (T v) {
    v.x;
};
template <typename T>
concept coord = has_x<T> && requires (T v) {
    v.y;
};
void function(has_x auto x) {}
void function(coord auto x) {}
struct X {
    int x;
};
struct Y {
    int x;
    int y;
};
int main() {
    function(X{}); // OK, only one viable candidate
    function(Y{}); // OK, coord is more specific
}
```

#### Rule of Thumb:
- `A = (X1 ... XN); B = Y && (X1 ... XN);` B is considered to be more specific than A
- `A = (X1 ... XN); B = Y || (X1 ... XN);` A is considered to be more specific than B

### EXERCISE: BUILD AN AVERAGEABLE CONCEPT AND USE IT
Template:
```cpp
template<typename T>
concept bool Averageable = std::something_with_default_constructible_v<T>
        && requires (T a, T b) {
            // insert mathematical operations and result type here
            // like this: `{ a % b } -> T;`
        };
```

## static_assert
- gets evaluated at compile time
- throws compile errors if invalid

```cpp
// validate during compile-time that integral division rounds towards zero:
static_assert(-5 / 2 == -2);
```

## Non-Type Template Parameters

```cpp
#include <cstdio>

template <size_t Index, typename T, size_t Length>
T& get(T (&arr)[Length])
{
    static_assert(Index < Length, "Out-of-bounds access");
    printf("Accessing element %zd of %zd\n", Index, Length);
    return arr[Index];
}

int main()
{
    int numbers[]{1, 2, 3, 4, 5};
    printf("%d\n", get<2>(numbers));
    printf("%d\n", get<5>(numbers));
}
```