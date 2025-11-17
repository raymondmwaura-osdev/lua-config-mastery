# For Loop

## Syntax and Variants

Lua provides primarily two variants of the `for` statement: the **numeric for** and the **generic for**.

### Numeric for

The numeric form uses a control variable whose value is implicitly local to the loop, together with an initial value, a final value, and an optional step value:

```lua
for var = exp1, exp2, exp3 do
  -- body
end
```

* `exp1` is evaluated once to provide the initial value.
* `exp2` is evaluated once to provide the limit.
* `exp3` (the step) is evaluated once; if omitted, it defaults to `1`.
* The control variable `var` takes successive values starting at `exp1`, incremented by `exp3`, until it passes `exp2` (considering the sign of `exp3`).
* The control variable is local to the loop body and cannot safely be modified inside the loop body.

### Generic for

The generic form is used for iterating over collections (tables, strings, or user-defined iterators). Its syntax is:

```lua
for var1, var2, … in iterator_function(state, …) do
  -- body
end
```

* An *iterator function* is invoked repetitively to produce values.
* Each loop iteration assigns the returned values to the loop variables (`var1`, `var2`, …).
* The loop ends when the iterator returns `nil`.
* Common iterator functions include `pairs()`, `ipairs()`, or `string.gmatch()`.

---

## Semantic and Execution Details

### Evaluation Order (Numeric for)

* The expressions `exp1`, `exp2`, and `exp3` are evaluated **once** before any iteration begins.
* The control variable is initialized to the value of `exp1`.
* At each iteration:

  * Check if the control variable has passed the limit (`exp2`) considering the sign of the step.
  * If it is within range, execute the body.
  * Then increment the control variable by the step (`exp3`) and repeat the check.
* Once the limit is exceeded, the loop ends and control transfers to the statement following the loop.
* The control variable is local to the loop; after termination it is not accessible (or accessible only as a separate variable if one assigned to it explicitly).
* One should **not modify** the control variable within the loop body; doing so leads to undefined or unpredictable behavior.

### Iteration over Collections (Generic for)

* The generic `for` uses the iterator function to produce one or more values each iteration.
* The loop variable(s) receive the values, and the loop body executes.
* When the iterator returns `nil`, the loop terminates.
* The generic form is flexible and allows iteration over arbitrary collections or user-defined sequence generators.
* Examples of built-in iterators:

  * `pairs(table)`; iterate over all key/value pairs in a table.
  * `ipairs(table)`; iterate over array-style integer keys 1,2,3… until a `nil` is found.
  * `string.gmatch(str, pattern)`; iterate over sub-strings matching `pattern`.

---

## Practical Examples

### Numeric for; counting upwards

```lua
for i = 1, 5 do
  print(i)
end
```

This prints 1 through 5 in sequence.

### Numeric for; custom step and counting downwards

```lua
for i = 10, 1, -1 do
  print(i)
end
```

This prints from 10 down to 1, stepping by -1 each time.

### Iterating an array via numeric for

```lua
local arr = { 10, 20, 30, 40 }
for i = 1, #arr do
  print("Index:", i, "Value:", arr[i])
end
```

Here `#arr` is the length operator; the loop uses numeric for to traverse the array.

### Generic for; iterating a table with non-numeric keys

```lua
local person = { name = "Alice", age = 30, job = "Engineer" }
for key, value in pairs(person) do
  print(key, "=", value)
end
```

This prints each key/value pair in the table (order unspecified).

### Generic for; iterating sequential array using ipairs

```lua
local fruits = { "Apple", "Banana", "Cherry" }
for index, fruit in ipairs(fruits) do
  print(index, fruit)
end
```

This prints index and value for each element until a `nil` is encountered.

### Generic for; iterating substrings via pattern

```lua
local text = "Lua is powerful"
for word in string.gmatch(text, "%a+") do
  print(word)
end
```

This prints each word (sequence of letters) found in the string.

---

## Advanced Considerations

### Nested Loops

Loops may be nested one inside another. For instance:

```lua
for i = 1, 3 do
  for j = 1, 2 do
    print("i=", i, "j=", j)
  end
end
```

The inner loop executes completely for each iteration of the outer loop.

### Performance Considerations

* The numeric form is highly efficient for simple counting loops.
* Generic loops involve iterator overhead but are essential for traversing collections or user-defined sequences.
* When iterating large tables where performance is critical, prefer numeric loops with direct indexing if possible.

### Loop Control Statements

* The `break` statement may be used inside a loop body to exit the loop immediately.
* There is **no** language-provided `continue` keyword (in standard Lua); alternative control flow must be implemented via conditional logic inside the loop.
* One should not alter the loop control variable (in numeric `for`) within the body; doing so renders the loop semantics unpredictable.

### Custom Iterators

Lua allows programmers to define custom iterator functions that conform to the generic `for` protocol: returning successive values until `nil`. This enables abstract or domain-specific iteration patterns.

---

## Best Practices and Recommendations

* Use numeric `for` when the number of iterations is known and a simple counter suffices.
* Use generic `for` when iterating collections (tables, strings, custom sequences).
* Avoid modifying loop control variables in numeric loops.
* Use `pairs` for hash-style tables (non-sequential keys) and `ipairs` for sequential integer-key arrays.
* For clarity and maintainability, prefer descriptive loop variable names when the purpose of the loop is non-trivial.
* When iterating large data structures, consider performance trade-offs between numeric and generic loops.
* If you require early exit from a loop, use `break` rather than manipulating the control variable.
* Always be aware of the scope of loop variables; do not assume they persist after loop termination.

---
