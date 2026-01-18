---
description: Migrate and fixup java to go
---

Migrated java code in $2 to $1. **This is suppose to be a line by line migration**, therefor it is perfectly fine not to generate idiomatic go code. Instead we need to keep code as close as possible to the original java code. Don't worry about changes needed in other files and **Don't modify other files**. Don't add any placeholders. If the type or function is not declared in the $2 then it is perfectly acceptable to have a missing reference in $1. Fallow the same declaration order as the java source.

1. First generate the machine migration output using javaGo tool
2. Then fix the generated source code fallowing Migration strategy guidelines below

# Migration strategy

## Abstract classes

- Assume we have an abstract class called Foo.
- We generate the following Go artifacts:
  1. FooData: interface containing getters and setters for all fields
  2. FooBase: struct containing all the fields
  3. FooMethods: struct containing default (non-abstract) method implementations
  4. Foo: interface that embeds FooData

````java
abstract class Foo {
  int a;
  abstract int f();
  int b() {
    return f() + a;
  }
}
```go
// 1. Field access interface
type FooData interface {
  SetA(a int)
  GetA() int
}

// 2. Base with fields
type FooBase struct {
  A int
}

func (b *FooBase) SetA(a int) { b.A = a }
func (b *FooBase) GetA() int { return b.A }

// 3. Foo interface = fields + abstract + default methods
type Foo interface {
  FooData
  F() int // abstract
  B() int // default implementation in FooMethods
}

// 4. Default (non-abstract) methods
type FooMethods struct {
  Self Foo
}

func (m *FooMethods) B() int {
  return m.Self.F() + m.Self.GetA()
}
````

### Subclass

```java
class Bar extends Foo {
  int f() {
    return 42
  }
}
```

```go
type Bar struct {
  FooBase
  FooMethods
}

func (b *Bar) F() int {
  return 42
}
```

## Overloading

- For overloading we need to generate different methods based on the parameter types.

```java
class Foo {
  int bar() {...}
  int bar(Baz baz) {...}
  int bar(Baz baz, BarBaz barBaz) {...}
  int bar(Baz baz, FooBaz fooBaz) {...}
}
```

```go
type Foo struct {}

func (this *foo) bar() {}
func (this *foo) barWithBaz(baz Baz) {}
func (this *foo) barWithBazBarBaz(baz Baz, barBaz BarBaz) {}
func (this *foo) barWithBazFooBaz(baz Baz, fooBaz FooBaz ) {}
```

## Type casts

```java
Foo a = (Foo) b;
```

```go
a := b.(Foo)
```

## Enum

- Where possible try to use go enum with name prefixes to avoid clashes. Example in java if there is enum `Foo` with values `Bar` and `Baz` use

```go
type Foo uint
const (
  FOO_BAR Foo = iota
  FOO_BAZ Foo
)
```

- If the enum in question has data create a `struct` instead.

```go

type DiagnosticErrorCode struct {
	diagnosticId string
	messageKey   string
}

// Constants ported from io.ballerina.compiler.internal.diagnostics.DiagnosticErrorCode
// Generic syntax error
var ERROR_SYNTAX_ERROR = DiagnosticErrorCode{diagnosticId: "BCE0000", messageKey: "error.syntax.error"}
```

## Mutating parameters

In java code we will have cases where we mutate values passed in as parameters. _commonly we add to lists passed in_. To deal with this we are always passing lists/arrays as pointers to arrays in go code.

## Optional

Use `Optional[T]` defined in common module
