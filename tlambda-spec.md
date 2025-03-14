# Typed Lambda

Language: `tlambda`

## Type Definitions

Atomic types are defined using the `type` keyword.

```haskell
type foo;
```
```status
Success
```

Type aliases are defined using `type` and `=`.

```haskell
type foo;
type bar = foo;
```
```status
Success
```

The right hand side of a type alias definition needs to be a valid type.

```haskell
type bar = foo;
```
```status
ERROR: Invalid type foo in type bar = foo;
```

A type alias defines a new type which can later be used in other type
definitions.

```haskell
type foo;
type bar = foo;
type baz = bar;
```
```status
Success
```

For any two types `t1` and `t2`, `t1->t2` is a type.

```haskell
type foo;
type bar;
type baz = foo->bar;
```
```status
Success
```

The `t1->t2` is a type only if both `t1` and `t2` are types.

```haskell
type bar;
type baz = foo->bar;
```
```status
ERROR: Invalid type foo in type baz = foo->bar;
```

```haskell
type foo;
type baz = foo->bar;
```
```status
ERROR: Invalid type bar in type baz = foo->bar;
```

The `->` type constructor is (right) associative.

```haskell
type foo;
type bar = foo->foo->foo->foo;
```
```status
Success
```

Parentheses can express different precedence.

```haskell
type foo;
type bar = (foo->foo)->foo->foo;
```
```status
Success
```

## Primitives

Primitives are defined using the `:` operator. They are given a type but not a
value. The value should come from elsewhere (e.g., a native implementation of a
function written in a different programming language).

```haskell
type int64;
int64_inc : int64 -> int64;
```
```status
Success
```

Primitives are only valid if the type is a valid type.

```haskell
type int64;
int64_inc : int64 -> invalid_type;
```
```status
ERROR: Invalid type invalid_type in int64_inc : int64 -> invalid_type;
```

## Typed Definition

A typed definition has the form `var : type = expr;`.

```haskell
type int64;
long : int64;
distance_to_moon : int64;
distance_from_moon : int64 = distance_to_moon;
```
```status
Success
```

The type needs to be a valid type.

```haskell
type int64;
long : int64;
distance_to_moon : int64;
distance_from_moon : invalid_type = distance_to_moon;
```
```status
ERROR: Invalid type invalid_type in distance_from_moon : invalid_type = distance_to_moon;
```

As for the expression, this will be discussed in the following sections.

## Type Equality

A typed definition gives us the opportunity to specify type equality. The
following examples will be of the form `foo : type1; bar : type2 = foo;`, for
different values of `type1` and `type2`. Through this, we will explore which
pairs of types equal one another.

Atomic types equal themselves.

```haskell
type int64;
foo : int64;
bar : int64 = foo;
```
```status
Success
```

And they are different from other atomic types.

```haskell
type int64;
type int32;
foo : int64;
bar : int32 = foo;
```
```status
ERROR: Type mismatch: expected int32 but inferred int64 when inferring the type of foo in bar : int32 = foo;
```

An alias to a type is considered equal to that type.

```haskell
type int64;
type long = int64;
foo : int64;
bar : long = foo;
```
```status
Success
```

```haskell
type int64;
type long = int64;
foo : long;
bar : int64 = foo;
```
```status
Success
```

A function type is equal to another if both the domain and range types are
equal.

```haskell
type int64;
foo : int64 -> int64;
bar : int64 -> int64 = foo;
```
```status
Success
```

It is not equal if the other type is not a function type.

```haskell
type int64;
foo : int64;
bar : int64 -> int64 = foo;
```
```status
ERROR: Type mismatch: expected type int64 -> int64 but inferred non-function type int64 when inferring the type of foo in bar : int64 -> int64 = foo;
```


It is not equal if either the domain or the range are not equal.

```haskell
type int64;
type int32;
foo : int64 -> int64;
bar : int32 -> int64 = foo;
```
```status
ERROR: Type mismatch: expected int32 but inferred int64 when inferring the type of foo in bar : int32 -> int64 = foo;
```

```haskell
type int64;
type int32;
foo : int64 -> int64;
bar : int64 -> int32 = foo;
```
```status
ERROR: Type mismatch: expected int32 but inferred int64 when inferring the type of foo in bar : int64 -> int32 = foo;
```

Here too, equality takes aliases into account.

```haskell
type int64;
type long_func = int64 -> int64;
foo : long_func;
bar : int64 -> int64 = foo;
```
```status
Success
```
