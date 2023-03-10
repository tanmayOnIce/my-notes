
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
