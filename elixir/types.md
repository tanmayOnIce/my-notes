## what are the basic types in elixir ?
number,boolean,string,atom,tuple,lists

> Keep in mind that ** In functional language every expression must return a value**

## what is atom ?
Atom are constant values, a tag or level that never changes.
Atoms are preceded by a semicolon. The convension is to use snake_case.

like :an_atom

## what is list ?
List are series of values that are contained in square brackets. Unlike java list is not a class. it does not have any method. To work and manipulate use modules List or Enum. It is stored in linked list.

example - [1,"hello",:foo_bar]

## what is tuple ?
Same as list. but uses curly braces and stores in contiguous memory space. Thus updating a tuple is more expensive.

Tuple are mostly used to return values. Like an atom and the value 

for example -
{:ok,42} or {:error,"cannot calculate value"}

## what are string in elixir ?
String in Elixir are binary, meaing a list of bytes.

like list_of_bytes = <<97,98,99,100>>
"abcd"

## does Elixir supports string interpolation ?

Yes like "#{:hello_atom} elixir"

## how to concat String ?
using <> operator.

"hello" <> "elixir"
"helloelixir"

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

## what does '=' operator do in elixir ?
It is a match operator.

When we write 
a = 2
a = 3

what really happens - 
1. right side is evaluated (2,3)
2. It is then matched against left hand side.
3. If matches, it is boudned to the right-side value.

## what is pin operator ?
It only verifies a match without rebounding a variable.

like

a = 3
^a = 3
^a = 7
** (MatchError) no match for right hand side value: 7

## how to use pattern matching in funciton ?

```
defmodule Bot do

	def greet("") do
		IO.puts "None to greet."
	end
	
	def greet(name) do
		IO.puts "Hello {#name}"
	end
end

```

## how to deconstruct list ?

[head | tail] = [1,2,3,4]

head
1

tail
2,3,4

[a,b,c] = [1.2.3]
a
1

b
2

[a,b] = [1,2,3]

** (MatchError) no match of right hand side value: [1,2,3]

[a,b,_] = [1,2,3]

It can work on hash map also

%{a: a, b: b} = %{a: 4, b: "hello",c: :foo}

a
4
b
"hello"

## what is recursion ?

Keep in mind that elixir does not support loops. You have to use recursion.

like -

```
defmodule Factorial do
	def do_it(0) do
	 1
	 end
	 
	 def do_it(n) do
	  n * do_it(n -1)
	 end
end

```

Recursion have two pattern -
1. determine and implement the basic case
2. Determine and implement the general case.

## how tail-call optimization is done ?

It is when we are not allocating new stack frame for  a function because the calling function will simply return the value it gets from the called function.

For this the last call the function does should be the recursive call. For the above case the last thing is multiplying. This is how to write tail-call optimized code.

```
defmodule Factorial do
	def do_it(n) do
		internal_do_it(n,1)
	end
	
	defp internal_do_it(0,acc)
		acc
	end
	
	defp internal_do_it(n,acc)
		internal_do_it(n-1,n*acc)
	end
end

```

## how to add two list ?

use ++ operator

[1,3,5] ++ [2,4]
[1,3,5,2,4]

list can also be subtracted
[1,2,3,4,5] -- [1,3,5]
[2,4]


## how to process list in elixir ?

```
defmodule ListUtils do
	def sum([]) do
		0
	end
	def sum([h | t]) do
		h + sum(t)
	end
end
```

Enum module also contains function for processing 

`Enum.reduce([1,3,4,5], fn x,acc -> x+acc end)`

you can use map operation for transforming 

Enum.map(["a","b","c"],fn x -> String.to_atom(x) end)
[:a,:b,:c]

also filter is supported
Enum.filter([1,2,3,4,5],fn x -> x >2 end)

you can also do function referencing

Enum.map(["a","b","c"],&String.to_atom/1)


## how do you use map ?

```
book = %{
	title : "Programming Elixir",
	author: %{
		first_name: "Dave",
		last_name: "Thomas"
	},
	year: 2018

}

```

## how to access a field in map ?

book[:title]
"Programming Elixir"

Keep in mind that you cannot change the value once initialized. like-

book[:title] = "Programming Java"
** (CompileError) iex:5: cannot invoke remote function Access.get/2 inside match

To change we need to use put function

```
Map.put(book,:title,"programming Elixir >= 1.16")

%{
	author:%{first_name:"Dave",last_name: "Thomas},
	title: "programming Elixir >= 1.6",
	year: 2018
}

```

Keep in mind map.put create a new map It DOES NOT UPDATE THE PREVIOUS MAP.

also it can be done by -

```
new_book = {book | title: "Programming Elixir >= 1.6"}


```

You can also use fetch function 

Map.fetch(book,:year)

{:ok,2018}
It returns both state and result

if we don Map.fetch(book,:foo)
:error

we can use pattern matching 

{:ok,y} = Map.fetch(book,:year)

y
2018

## Are control flow present in elixir ?

Yes but pattern matching is more prominient.

## what are the control flow present ?
if, unless,case.

## what is if structure ?

if test_condition do 
	# true case
else 
	# false case
end

we can assign if statement 

a = if test_statement do

## what should we do if there more than 2 case ?

Use case statement 

```
welcome_message = case get_language(user) do
	"IT" -> "Benvenuto #{user.name}"
	"ES" -> "Bienvenido #{user.name}"
	"DE" -> "Willkommen #{user.name}"
	_ -> "welcome"
end

```

## what are Guards in function ?

It allows function to execute that passes the guard.

```
defmodule Foo do
	def devide_by_10(value) when value > 0 do
	
	value/10
	
	end
end
	
```

There are some build in guard function present -

like is_string,is_atom,is_binary,is_list,is_map

```
defmodule Foo do
	def devide_by_10(value) when value > 0 and (is_float(value) or is_integer(value)) do
		value/10
	do
do

```

## what is pipe operator ?

you can pass the result of one function to another function. like

[1,3,5,7,8,9] 
|> Enum.filter(fn x -> x >5) 
|> Enum.map(fn x -> x + 10 end) 
|> Enum.reduce(fn acc,x -> acc+x end) 
|> IO.puts


## what are type specification and what is @spec and @type macros?

it addes type hinting of the module signature

@spec - It helps to understand if it is method signature and return type

```
defmodule Math do
	@spec sum(integer,integer) :: integer
	def sum(a,b) do
		a+b
	end
end
```

@spec macro can also be used to return if multiple type of return is possible

```
defmodule Math do
	@spec dev(integer,integer) :: {:ok,integer} | {:error,String.t}
	def div(a,b) do
		a/b
	end
end
```

For this to work you need Dialyxir


