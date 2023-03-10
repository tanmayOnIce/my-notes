
## Path Object

1. It holds the relative paths to the individual endpoints and their operations.
2. The path field must begin with forward /.
3. The path field is appended to the server object URL and construct the full URL.
4. http://{host}:{port}/openapi/getCustomerById

example- 
```
paths:
	/getCustomerByID:
		get:
			description: retrive customer by id
			operationId: getGetCustomerById
```

## Operation Id

1. it is unique String to identify the operation.
2. This must be unique within the specification file
3. it is case-sensitive.

## Different type of path definition

1. **Variable UserId** - /customer/{userId}
2. **fixed UserId** - /customer/204050

## Openapi input data

1. Parameter - path,query
2. Request body - payload