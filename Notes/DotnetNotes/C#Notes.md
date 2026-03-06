# C# Notes

# Q: What is a struct again?

In C#, there are two fundamental catagories of types:

- Reference types -> `class`
- Value types -> `struct`

## Classes (reference typess)

- stored on the heap
- Variables hold references (pointers)
- can be null
- compared by reference by default

```C#
var a = new User(...);
var b = a;

b.FirstName = "Chris";
// a.FirstName is now also "Chris"
```

Because both vaiables are the same object.

#

# Structs (Value types)

- Stored inline (stack or inside another object)
- Variables hold the actual value
- Cannot be null (unless `Nullable<T>`)
- Copied on assignment

```C#
var a = new UserId(Guid.NewGuid());
var b = a;

b = new UserId(Guid.Empty);
// a is unchanged
```

Structs behave like primitievs (`int`, `bool`, `DateTime`).

#

# Why `UserId` is a struct

We used:

```C#
public readonly reacord struct UserId(Guid Value)
```

We choose a struct because:

- It represents a small, immutable value
- It should behave like a Guid
- It shouldn't be nullable by default
- It avoids unnessisary heap allocation

This is called a **Value Object** in domain modeling.

User is not an entity -- it's just a strongly typed wrapper around a Guid.

#

# What does `record` mean?

`record` is a C# keyword introduced in C# 9.

It's used for data-centric types with value equality.

There are two kinds:

- `record class`
- `record struct`

I used a `record struct`

#

## what does `record` give you?

it automatically gives:

- Value-based equality
- `ToString()`
- Proper `GetHashCode()`
- Deconstruction support

Example:

```C#
var a = new UserId(guid);
var b = new UserId(guid);

Console.WriteLine(a == b); // true
```

If this were a normal struct without overriding equality, it still compares by value -- but records generate cleaner, safer equality automatically.

#

is `record struct` a class? -> No.

#

# What is `nameof` again?

`nameof` is a compile-time operator that return the name of a symbol as a string.

Example:

```C#
throw new argument exception("Title cannot be empty. ", nameof(title));
```

`nameof(title)` becomes:

```C#
"title"
```

But here's why it matteres:

If you rename the parameter `title` -> `workOrderTitle`,
`naemof(title)` automatically updates.

If you had written:

```C#
"Title"
```

That sring would automatically update.

#

## Why this matters in real projects

- Avoids magic strings
- Refactors-safe
- Prevents typeos
- Compiler checks it

#
