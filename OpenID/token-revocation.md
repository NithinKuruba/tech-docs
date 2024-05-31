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

For example, the following response contains a set of information about an active token:

The following is a non-normative example response:

HTTP/1.1 200 OK
Content-Type: application/json

{
  "active": true,
  "client_id": "l238j323ds-23ij4",
  "username": "jdoe",
  "scope": "read write dolphin",
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238,
  "extension_field": "twenty-seven"
}

If the introspection call is properly authorized but the token is not active, does not exist on this server, or the protected resource is not allowed to introspect this particular token, then the authorization server MUST return an introspection response with the
"active" field set to "false".  Note that to avoid disclosing too much of the authorization server's state to a third party, the authorization server SHOULD NOT include any additional information about an inactive token, including why the token is inactive.

The following is a non-normative example response for a token that has been revoked or is otherwise invalid:

HTTP/1.1 200 OK
Content-Type: application/json

{
  "active": false
}