# Variables

## Introduction

A *variable* in Lua is a name that refers to a storage location holding a value. Lua is **dynamically typed**, which means variables do not have fixed types; rather, values do. Before the first assignment, a variable's value is `nil`.

There are two main “kinds” of variables in Lua:

1. **Global variables**
2. **Local variables**

---

## Naming and Declaration

### Identifiers

* A valid variable name (identifier) in Lua consists of Latin letters, digits, and underscores. It **must not** start with a digit.
* Lua is **case-sensitive**: `foo`, `Foo`, and `FOO` are distinct.

### Default (Global) Variables

* In Lua, any assignment to a name without the `local` keyword creates or updates a **global variable**.
* Global variables are stored in Lua’s global environment, typically `_ENV` in newer Lua versions.
* Example:

  ```lua
  x = 10           -- global variable
  y = "hello"      -- global string
  ```

* If you read a global variable before ever assigning to it, Lua returns `nil` (no runtime error).

### Local Variables

* Use the `local` keyword to declare a variable whose **scope** is limited to a block (a chunk, function, loop, `do … end`, etc.).
* Example:

  ```lua
  local counter = 0   -- a local variable
  function foo()
    local message = "Inside foo"
    print(message)
  end
  print(counter)       -- accessible here
  -- print(message)    -- error: message is nil / undefined here
  ```

* Local variables are **lexically scoped**, meaning their visibility is defined by the program's textual structure.
* According to best practices, it's generally safer to use `local` wherever possible to avoid polluting the global namespace.

---

## Variable Types and Values

Though variables themselves are untyped, the **values** they hold can be of different *Lua types*. Some of the primary types are:

* `nil`; absence of a value
* `boolean`; `true` or `false`
* `number`; integer or float
* `string`; text strings
* `function`; first-class functions
* `table`; associative arrays (used for arrays, maps, objects)
* `thread`; for coroutines
* `userdata`; for C data

You can check the type of a value using Lua’s built-in function:

```lua
print(type(123))          --> "number"
print(type("Lua"))        --> "string"
print(type(print))        --> "function"
print(type(nil))          --> "nil"
```

---

## Assignment and Multiple Assignment

### Single Assignment

* Use `=` to assign a value to a variable.
* Example:

  ```lua
  a = 5
  b = "world"
  ```

### Multiple Assignment

* Lua supports multiple assignment in a single line.
* Example:

  ```lua
  x, y, z = 1, 2, 3
  ```

* It also allows swapping or returning multiple values easily:

  ```lua
  a, b = b, a
  function foo() return 10, 20 end
  m, n = foo()   -- m = 10, n = 20
  ```

---

## Scope and Lifetime

### Scope

* **Global scope**: A global variable is visible in all parts of the code (unless shadowed by a local).
* **Local (lexical) scope**: A `local` variable exists in the block where it's declared.
* **Block scope**: Local variables may be declared in blocks such as `if … end`, `for … end`, or `do … end`.

Example of block scope:

```lua
do
  local temp = "I live here"
  print(temp)
end
-- print(temp)  --> nil (temp is out of scope)
```

### Lifetime

* The *lifetime* of a local variable begins when the code execution reaches its declaration and ends when the block terminates.
* Global variables persist for the duration of the Lua **environment** (unless explicitly set to `nil`).
* Because Lua uses lexical scoping, nested functions can “capture” local variables from outer scopes.

---

## Upvalues and Closures

### Upvalues

* An **upvalue** is a local variable from an outer function that is referenced by a nested (inner) function.
* This is central to creating **closures**, because the inner function retains access to these variables even after the outer function has returned.

Example:

```lua
function outer()
  local x = 10
  local function inner()
    return x * 2
  end
  return inner
end

local f = outer()
print(f())  -- prints 20
```

Here, `x` is an upvalue for `inner`.

* Closures share upvalues by reference, not by copy.
* Even when the outer function ends, the upvalue persists (as long as the closure exists).

### Debugging Upvalues (Advanced)

* Lua’s `debug` library provides functions to inspect and manipulate upvalues:

  * `debug.getupvalue(func, index)`; retrieve the name and value of the upvalue
  * `debug.setupvalue(func, index, newValue)`; change the upvalue’s value
* These are advanced features mostly used in metaprogramming or runtime introspection.

---

## Practical Guidance and Best Practices

1. **Prefer `local` by default**
   Use local variables wherever possible to avoid polluting the global namespace.
2. **Be mindful of naming collisions**
   Since global names are shared, two parts of a large program could inadvertently overwrite each other’s variables.
3. **Use upvalues and closures wisely**
   Closures are powerful, but overuse can make code harder to reason about. When using nested functions, think carefully about which variables need to persist.
4. **Avoid unnecessary globals**
   Unless a variable truly needs to be shared across modules or functions, limit its visibility.
5. **Use `type()` for debugging**
   When debugging your Lua code, `print(type(var))` is useful for validating what kind of value you’re dealing with.

---
