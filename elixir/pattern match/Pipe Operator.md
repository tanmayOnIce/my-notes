
## what is pipe operator ?

you can pass the result of one function to another function. like

[1,3,5,7,8,9] 
|> Enum.filter(fn x -> x >5) 
|> Enum.map(fn x -> x + 10 end) 
|> Enum.reduce(fn acc,x -> acc+x end) 
|> IO.puts
