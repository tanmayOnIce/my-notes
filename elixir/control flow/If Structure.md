
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

