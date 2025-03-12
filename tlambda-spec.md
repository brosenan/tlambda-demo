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
