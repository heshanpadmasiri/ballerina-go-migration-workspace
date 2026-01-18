---
description: Flatten abstract class conversion
---

When migrating an abstract class we generate multiple go constructs. For example consider abstract class called `Foo`. We generate the followings,

1. FooData: interface containing getters and setters for all fields
2. FooBase: struct containing all the fields
3. FooMethods: struct containing default (non-abstract) method implementations
4. Foo: interface that embeds FooData

````java
abstract class Foo {
  int a;
  int b() {
    return a;
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

Instead we need to flatten this to a single struct as

```go
type FooBase struct {
  int a;
}

func (this *FooBase) B() {
  return this.a
}
```

NOTE: this transform can only be done for abstract classes without abstract methods
