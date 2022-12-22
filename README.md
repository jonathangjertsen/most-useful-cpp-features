# Most useful C++ features

Coming from an embedded C perspective

## Templates as type-safe macros

Macro to clamp a number between a lower and upper bound

```C
#define CLAMP(A, LOWER, UPPER)  ((A) > (UPPER) ? (UPPER) : (A) < (LOWER) ? (LOWER) : (A))
```

As template

```C++
template<class T>
int clamp(T a, T lower, T upper)
{
    if (a > upper)
    {
        return upper;
    }
    else if (a < lower)
    {
        return lower;
    }
    else
    {
        return a;
    }
}
```

## Namespaces

C style:

```C
void MYCOMPANY_my_protocol_generate_item();
```

With namespaces

```C++
mycompany::my_protocol::generate_item();
```

or

```C++
using namespace mycompany;
my_protocol::generate_item();
```

## Better enums

C style:

```C
typedef enum {
    MY_ERROR_TYPE_NONE      = 0,
    MY_ERROR_TYPE_UNDERFLOW = 1,
    MY_ERROR_TYPE_OVERFLOW  = 2,
} my_error_type_t;
```

C++ enums can be scoped and can define the underlying datatype

```C++
enum class my_error_type_t : uint8_t
{
    NONE      = 0, // Becomes my_error_type_t::NONE
    UNDERFLOW = 1, // Becomes my_error_type_t::UNDERFLOW
    OVERFLOW  = 2, // Becomes my_error_type_t::OVERFLOW
};
```

## Scope bound resource management / RAII

C style: need to be careful with things that need to be temporarily disabled, opened/closed, etc

```C
void interrupt_safe_function()
{
    bool was_disabled = IntMasterDisable();

    if (!do_thing())
    {
        if (!was_disabled)
        {
            IntMasterEnable();
        }
        return;
    }

    if (!do_next_thing())
    {
        if (!was_disabled)
        {
            IntMasterEnable();
        }
        return;
    }

    do_other_thing();
    if (!was_disabled)
    {
        IntMasterEnable();
    }
}
```

In C++ you can write this class once:

```C++
class DisableInterruptInScope
{
public:
    explicit DisableInterruptInScope()
    {
        was_disabled = IntMasterDisable();
    }

    ~DisableInterruptInScope()
    {
        if (!was_disabled)
        {
            IntMasterEnable();
        }
    }

    bool was_disabled = false;
};
```

Then you can rely on the destructor to do the right thing every time

```C++
void interrupt_safe_function()
{
    DisableInterruptInScope d{};

    if (!do_thing())
    {
        return;
    }
    if (!do_next_thing())
    {
        return;
    }
    do_other_thing();
}
```

## std::bitset

It is like a bitvector type

Example TBD

## constexpr

TBD

## typedef -> using

C

```C
typedef uint32_t count_t;
typedef void (*callback_t)(count_t arg);
```

C++ `using` is more intuitive

```C++
using count_t = uint32_t;
using callback_t = void(*)(count_t arg);
```

## Functions taking struct pointer -> methods

TBD

## Driver struct with callbacks -> inheritance

TBD

## Pointers -> references

TBD

## Nicer for loops

C

```C
static something_t somethings[] = {
    // ...
};

void iterate_over_somethings()
{
    for (int i = 0; i < sizeof(somethings) / sizeof(somethings[0]); i++)
    {
        const something_t *something = &somethings[i];
        foobarize(something->x);
    }
}
```

C++

```C++
static something_t somethings[] = {
    // ...
};

void iterate_over_somethings()
{
    for (const auto& something : somethings)
    {
        foobarize(something.x);
    }
}
```

