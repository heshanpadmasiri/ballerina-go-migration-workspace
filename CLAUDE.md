Aim of this project is to line by line migrate the existing java based Ballerina compiler into go

# Source structure

- `ballerina-lang`
  Contains the original java source code
- `ballerina-lang-go`
  Contains the migrated go source code
- `javaGo`
  Contains the machine translation code

# Migration strategy

- **Objective is to migrate code line by line from java instead of trying to create a idiomatic go code**
  - For this first machine translate using `javaGo` and then fix and migration errors.
    - If there are any repeated patterns of errors then we should update `javaGo` to correctly migrate that code
- We don't need to create separate go files for each java file it is okay to have multiple of them in the same go file. Just add a comment saying where it original java code started and ended.

## Migration fixes

### Overloading

- Machine translation will create multiple methods with the same name for the overloaded methods. We just need to give more descriptive names (common base name with a suffix explaining the difference, example: `CreateNode`, `CreateNodeWithDiagnostics`) and update usages.

### Type casts

- Machine translation should properly detect these and migrate them properly but in cases this haven't done, fallow this pattern

```java
Foo a = (Foo) b;
```

```go
a, ok := b.(Foo)
if !ok {
  panic("expected Foo")
}
```

### Cyclic dependencies

- When there is a cyclic dependency relevant code (ie. method, enum, etc not necessarily the whole file) should be moved to common

### Enum

- Where possible try to use go enum with name prefixes to avoid clashes. Example in java if there is enum `Foo` with values `Bar` and `Baz` use

```go
type Foo uint
const (
  FOO_BAR Foo = iota
  FOO_BAZ Foo
)
```

- If the enum in question has data create a struct instead. For example see `diagnostic-errors.go`. Here we will have value defined for each enum variant

```go

type DiagnosticErrorCode struct {
	diagnosticId string
	messageKey   string
}

// Constants ported from io.ballerina.compiler.internal.diagnostics.DiagnosticErrorCode
// Generic syntax error
var ERROR_SYNTAX_ERROR = DiagnosticErrorCode{diagnosticId: "BCE0000", messageKey: "error.syntax.error"}
```

### Mutating parameters

In java code we will have cases where we mutate values passed in as parameters. _commonly we add to lists passed in_. In such cases in migrated code we need to return both the mutate list as well as the normal return value. These mutated values need to be propagated upto the level where the value being mutated was defined.

## Common pitfalls

- In java switch statements fallthrough by default where is in go you must explicitly fallthrough

## Module specific migration notes

### Parser

- Put all the `internal` package code in to the st-node.go file
- We have generated code in `*-gen.go` code which are generated using `nodes.json`
