# variant-nelua

Generic types for handling Variant data in nelua

## Warning
Before using the `Variant` types, you need to [make them local](https://github.com/edubart/nelua-lang/issues/203) or else the compiler will crash
```lua
local Variant = require "variant"
local Sum = Variant.Sum
local Option = Variant.Option
local Result = Variant.Result
```

## Requirements
- [nelua](https://nelua.io/)

## How to install
Copy `variant.nelua` into your project and require normally
```lua
local Variant = require "variant"
```

Or add to your [nlpm](https://github.com/kmafeni04/nlpm) package dependencies
```lua
{
  name = "variant-nelua",
  repo = "https://github.com/kmafeni04/variant-nelua",
  version = "COMMIT-HASH-OR-TAG",
},
```

## Quick start

```lua
local Variant = require "variant"
local Sum = Variant.Sum
local Option = Variant.Option
local Result = Variant.Result

-- Sum
local Result = @Sum(record{
  val: string,
  err: string
})

local function test(v: integer): Result
  local res: Result
  if v < 10 then
    res = Result.err("Number less than 10")
  else
    res = Result.val("Number greater than 10")
  end
  return res
end

local x = test(9)
local y = test(10)

print(x:is()) -- err
print(x:is_err()) -- true
print(x:get_err()) -- Number less than 10

print(y:is()) -- val
print(y:is_err()) -- false
print(y:get_val()) -- Number greater than 10

-- Option

local x: Option(string) = Option.Some("string")
local y: Option(string) = Option.None()
local z: Option(string) = "hello"

print(x:is()) -- some
print(x:is_some()) -- true
print(x:get()) -- string
print(y:is()) -- none
print(y:is_none()) -- true
-- print(y:get()) -- will crash
print(z:is()) -- some
print(z:get()) -- hello

-- Result

local MyResult = @Result(number, string)

local function half(v: integer): MyResult
  if v < 1 then
    return MyResult.err("Number less than 1")
  end
  return MyResult.ok(v / 2)
end

local x = half(10)
local y = half(0)

print(x:is()) -- ok
print(x:is_ok()) -- true
print(x:get()) -- 5.0
print(y:is()) -- err
print(y:is_err()) -- true
-- print(y:get()) -- will crash
```

## Reference

### Variant

```lua
local Variant = @record{}
```

### SumT

```lua
  local SumT: type <nickname(#[string.format("Sum(%s)", table.concat(Ts, ", "))]#)> = @record{
    tag: SumTag,
    val: SumVal
  }
```

### SumT:is

```lua
  function SumT:is(): string
```

### SumT:#|"is_" .. name|#

Returns a boolean depending on if the object is the defined field 'name'

```lua
    function SumT:#|"is_" .. name|#(): boolean
```

### SumT:#|"get_" .. name|#

Returns the value of the field 'name' or crashes if the object isn't that tag

```lua
    function SumT:#|"get_" .. name|#(): #[T]#
```

### Sum

Generic used to instantiate a Sum type in the form of `Sum(rec)`\
Argument `rec` is the value type that the Sum will represent

```lua
local Sum: type = #[generalize(make_sum)]#
```

### Variant.Sum

Variant module namespacing of the Sum generic

```lua
local Variant.Sum = Sum
```

### OptionT

```lua
  local OptionT: type <nickname(#[string.format("Option(%s)", T)]#)> = @Sum(record{
    some: T,
    none: niltype
  })
```

### OptionT:get

Returns the some field value or crashes if it is none

```lua
  function OptionT:get(): T
```

### OptionT:get

Metafield to allow passing direct values to options instead of wrapping them in a 'Some'

```lua
  function OptionT.__convert(n: option_convert_concept)
```

### Option

Generic used to instantiate a Option type in the form of `Option(T)`\
Argument `T` is the value type that the Option will represent

```lua
local Option: type = #[generalize(make_option)]#
```

### Option.Some

Creates an Option object with the subtype of `v`'s type

```lua
function Option.Some(v: auto): Option(#[v.type]#)
```

### Option.Some

Returns an Option object with `niltype` as it's subtype

```lua
function Option.None(): Option(niltype)
```

### Variant.Option

Variant module namespacing of the Option generic

```lua
local Variant.Option = Option
```

### ResultT

```lua
  local ResultT: type <nickname(#[string.format("Result(%s, %s)", T, E)]#)> = @Sum(record{
    ok: T,
    err: E
  })
```

### ResultT:get

Returns the value of `ok` or crashes

```lua
  function ResultT:get(): T
```

### Result

Generic used to instantiate a Result type in the form of `Result(T, E)`\
Argument `T` is the value type of the `ok` field\
Argument `E` is the value type of the `err` field

```lua
local Result: type = #[generalize(make_result)]#
```

### Variant.Result

Variant module namespacing of the Result generic

```lua
local Variant.Result = Result
```

