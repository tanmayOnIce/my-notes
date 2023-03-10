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


