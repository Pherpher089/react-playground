# Lanugage Level VS Domain Modeling Level

## Language Level (C#)

These are runtime concepts:

- Reference Type -> `class`
- Value Type -> `struct`
- `record` -> adds value-based equality
- `record struct` -> value type + record behavior

These describe memory behavior.

#

## Domain Modeling Level (DDD Concepts)

These are business modeling concepts:

- Entity
- Value Object

These describe meaning and identiy, not memory layout.

## What is an Entity?

An **Entity** is something that:

- Has Identity
- Can change over time
- Is tracked by who it is, not what it looks like

In my system:

- `WorkOrder` is an entity.
- `User` is an entity.

Why?

Because:

If two work orders have:

- Same title
- Same Priority
- Same description

They are still NOT the same work order.

Why?

Because they have different IDs.

Identity matters.

#

## Entity Rule

> Entities are defined by identity, not by their values.

Even if every proerty changes, the entity is still the same entitiy because the identity is the same.

#

## What is a Value Object

A value Object:

- Has no identity
- Is defined entirely by its values
- Is immutable
- Two values objects with the same values are equal

Examin my system:

```C#
public readonly record struct UserId(Guid Value);
```

if two `UserId`s have the same Guid -> they are equal.

Another example:

if we had:

```C#
public record Name(string First, string Last);
```

If two Names contain the same Fist and Last name -> they are equal
