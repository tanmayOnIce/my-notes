
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
