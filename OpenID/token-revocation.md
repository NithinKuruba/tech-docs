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