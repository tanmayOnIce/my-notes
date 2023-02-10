1. Resource owner -> me. who owns the data
2. client -> the application that wants to get data
3. authorization server -> where i can say yes.
4. resource server -> who holds the data.
5. authorization grant -> i allow to have permission to client to access it.
6. Redirect URI -> where should i end up in the flow
7. Access token -> the token the client really needs.


scopes -> permissions
consent -> 

Back channel (highly secured channel)
front channel (less secured channel)

client id can be shared in front channel

grant types - 
1. authorization code (front channel + back channel)
2. implicit (front channel)
3. Resource owner password credentials (back channel only)
4. Client credentials (back channel only) (service to service call)


## why should not use oauth for authentication ?

1. No standard way to get the user's information
2. Every implementation is a little different
3. No common set of scopes

OpenID connect is authentication. It is a layer for authentication.

## what OpenID connect adds 
1. ID token
2. UserInfo endpoint for getting more user information
3. standard set of scopes
4. Standardized implementation

In mobile based authentication we need to add PKCE

In JS based application when we are storing User ID. Make sure it