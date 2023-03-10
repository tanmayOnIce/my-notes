
## how elixir projects are organize ?
they are organimed in module.
like -
```
defmodule MyFunctions do

# your functions here

end
```

here defmodule is a macro, whose name is needed to call the function it contains.

like

```
defmodule MyFunctions do

	def sum(a,b) do
		a+b
	end
	
	def sub(a,b) do
		a -b
	end

end

```

we call the function like MyFunctions.sum(4,7). You can call function without parentheses like `MyFunction.sum 4 7`

Module are used to group functions.

## what is arity and how it is used ?

Arity is an important aspect of Elixir functions. A function in Elixir is determined by its name and arity which is the number of argument it takes.

## how to express one line function ?

def sum(a,b), do: a+b

## how to define a anonymous function ?

You have to use fn

a = fn -> 42 end
print_result(a)

to write anonymous function with paramter

sum = fn (a,b) -> a+b end

sum.(4,5)
9

> Keep in mind that .() is the way to call function that are define like values


## Why and How to do import ?

Import is used when you do not want to use full name for a function that is moduleName.function_name

```
defmodule MyFunctions do
	def sum(a,b) do
		a+b
	end
	
	def sub(a,b) do
		a-b
	end
end

defmodule DoSomeMath do
	import MyFunctions
	def add_and_subtract(a,b,c) do
		sub(sum(a,b),c)
	end
end

```

functions can be selected specifically like

import MyFunction, only: [sum: 2]

The atom only specifies we want only sum/2 function

function are identified in Elixir by name/arity.

