---
applyTo: '**/*.toit'
---
# Toit conventions

## Comments
- comments should start with a capital letter and end with a period.
- Also remember that Toitdocs for methods start with a 3rd person verb.
- Toitdocs use a variant of markdown, where subsequent lines of a paragraph are indented.
  ```
  /**
  Does something.

  A paragraph over
    multiple lines.
  */
  ```

## Naming
- Use `kebab-case` for variables and functions.
- Use `PascalCase` for classes.
- Use `SNAKE_CASE` for constants.

## Error handling
- Use `e := catch: ...` to catch errors.
- Use `try: ... finally: ...` to run finally code.

## Common types
Some of the common types in Toit include `int`, `float`, `bool`, `string`, `List`,
`Map`, `Set`, `Lambda`, `ByteArray`.

## Indentation
- Use 2 spaces for indentation.
- Use spaces, not tabs.
- Use 4 spaces for indentation of parameters.

## Literals
- `{:}` is an empty map; `{"foo": "bar"}` a map with an entry.
- `[]` is an empty list;  `[1, 2, 3]` is a list with three entries.
- `{}` is an empty set; `{1, 2, 3}` is a set with three entries.
- `#[]` is an empty byte array, `#[1, 2, 3]` is a byte array with three entries.

If a literal is not on one line, add a trailing comma and indent as follows:
```
foo := [
  "foo",
  "bar",
  "baz",
]
```

## Types
- Types are declared with a `/` suffix.
- Return types are declared with a `->` suffix, on the same line as the function name.
  Parameters may be declared on later lines, indented by 4 spaces.

```
  my-function -> int
      param1
      param2/string
      [block-param]:
    return in-body param1...
```
- Use `?` to indicate that a type can be `null`.
```
  my-variable_/string?
  foo my-parameter/int? -> int:
```

## Blocks
Blocks are lambdas that can not be stored in a field or global. They are
a cheaper but more limited alternative to lambdas.
- Use `:` to start a block.
- Use [block] to indicate a parameter that is a block.

## Loops
- Prefer `x.repeat: ...` over `for i := 0; i < x; i++: ...` for loops, if the
body of the loop doesn't break.
- Use `collection.do: ...` to iterate over a collection.
- Use `it` as implicit parameter in a block, if the block is short and no type is needed:
  ```
  collection.do: print it
  ```
  Use a block parameter otherwise:
  ```
  collection.do: |item/string|
    print item
  ```
- Use `continue.do` or `continue.repeat`, ... to start the next iteration of a loop in
  a block:
  ```
  collection.do: |item/string|
    if item == "foo": continue.do
    print item
  ```

## Variable/Field declaration
- Use `=` for assignment.
- Use `:=` to introduce a mutable variable or field. Inside functions prefer mutable variables over immutable variables.
- Use `::=` to introduce an immutable variable or field.
- Use a `_` suffix to indicate that field or function is private.
  ```
  my-private-field_/string
  ```
- Introduce an immutable field by declaring it with its type, which must be initialized in the constructor.
  ```
  class MyClass:
    my-field_/string
  ```
- Use `.field` in constructors to initialize fields, if the parameter is just assigned to the field, and
  the parameter is not named. In that case the parameter does not need a type.
  ```
  class MyClass:
    my-field_/string
    my-field2_/int

    constructor .my-field_ .my-field2_:
  ```
- Use `:= ?` to indicate that a mutable field must be initialized in the constructor.
  ```
  class MyClass:
    my-field_/string := ?

    constructor .my-field_:
  ```
- Use `this.field_ = parameter` if the constructor uses a named parameter that doesn't match the field name, or if the parameter has a different name.
  ```
  class MyClass:
    my-field_/string
    my-field2_/int := ?

    constructor unnamed/int --some-name/string:
      this.my-field_ = some-name
      this.my-field2_ = unnamed
  ```
- Use `?` suffix on types to indicate that the variable/field can be `null`.
  ```
  class MyClass:
    my-field?/string := ?

    constructor .my-field_:
  ```
