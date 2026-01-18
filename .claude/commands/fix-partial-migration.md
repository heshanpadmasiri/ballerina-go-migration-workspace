---
description: Fix partially migrated file
---

Fix partially migrated $1. Corresponding java is $2. Fallow coding conventions in the rest of the migrated source. Don't worry about changes needed in other files and _Don't modify other files_. Don't add any placeholders. Fallow the same declaration order as the java source.

# Common migration mistakes and their fixes

## Invalid `Foo.bar` function call

Check for function starting with `FooBar*` that accepts the same arguments. If you failed to find the corresponding function leave as is

## Missing functions

If the corresponding function is in a different java file and you can't find the corresponding go function (look for a go file corresponding to the same java file) **don't create any placeholders** and leave the function call as is

## Ternary expression

Ternary expression would be migrated as is. Fix them by declaring a new variable to hold the value (if not an assignment, if an assignment use target variable). Then use if else block to assign the variable.

## Switch expression

Switch expression would be migrated as is. If it is assigning to a variable declare the variable before the switch expression and assign in the switch block. If expression is returned then add returns to each branch.

- Be careful about the fact in java switch branches fallthrough by default and in go you need to explicitly use `fallthrough`

## Marker interfaces

For marker interfaces that simply extend a single interface you can use a type alias to that. In that case you should add a `Is${Marker}(value $Base) bool` function. Otherwise just introduce a new interface with inclusion of both interfaces.

## Illegal identifiers in function names

For overloaded methods we use the type of arguments in the name to give distinguishing names. If this has caused some illegal identifiers try to replace it with meaningful names (ex: []T -> Ts)
