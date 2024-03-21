# Identity Provider Tokens

## Federated Authentication

When using keycloak to federate authentication to upstream identity provider, keycloak depends on the tokens sent by the external identity provider to successfully authenticate user.

The tokens are then used to create user in keycloak database and then assigned with federated identity linkage, i.e. the user record will be tagged with provider user id and provider username. However, the user record in keycloak database will have its own username and id.

## Keycloak Setup

- Add IDP config in the keycloak realm
- Set `store IDP tokens` to true
- Create a public OpenID client
- Under client settings and `scope` tab, turn off full access and select `broker` client and then assign `read-token` role
- If using confidential client then you don't need next step
- Login with a user
- Verify that user record was created
- Under user settings, navigate to `role mapping` tab
- Assign the `read-token` role from broker client to that user
- Relogin again with user
- Copy the access token and do a POST call to below URL by passing the token as a bearer token under Authorization header

```
GET /auth/realms/{realm}/broker/{provider_alias}/token HTTP/1.1
Host: localhost:8080
Authorization: Bearer {keycloak_access_token}
```

- Copy the tokens in the response and use JWT.io to verify the claims sent by external IDP