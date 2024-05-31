# Token Revocation

The client requests authorization server through HTTP POST to revoke tokens

The revocation endpoint accepts two query params `token` and `token_type_hint`. 

The `token` parameter includes the actual token received by the client application after successful authentication by the end user. 

The `token_type_hint` that hints authorization server about the type of the token. It can take two values, `access_token` and `refresh_token`.

Additionally the client has to authenticate with authorization server prior to making revocation request, i.e. in case of confidential client, authorization server requires the client credentials. Below is the example.

```sh
POST /revoke HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
token=45ghiukldjahdnhzdauz&token_type_hint=refresh_token
```

# Introspection 

The authorization server's introspection endpoint accepts a token and returns metadata of that token including its status like active.


For example, the following shows a protected resource calling the token introspection endpoint to query about an OAuth 2.0 bearer token.  The protected resource is using a separate OAuth 2.0 bearer token to authorize this call.

The following is a non-normative example request:

```sh
POST /introspect HTTP/1.1
Host: server.example.com
Accept: application/json
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer 23410913-abewfq.123483
token=2YotnFZFEjr1zCsicMWpAA
```

In this example, the protected resource uses a client identifier and client secret to authenticate itself to the introspection endpoint.

The protected resource also sends a token type hint indicating that it is inquiring about an access token.

The following is a non-normative example request:

```sh
POST /introspect HTTP/1.1
Host: server.example.com
Accept: application/json
Content-Type: application/x-www-form-urlencoded
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW 

token=mF_9.B5f-4.1JqM&token_type_hint=access_token
```