
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