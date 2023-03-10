
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
