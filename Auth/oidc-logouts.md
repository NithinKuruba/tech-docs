# OpenID Connect Logout Mechanisms

## OpenID Connect Sessions Management

### Intro

- This spec defines how to monitor the end user login status at the OP so that RP can logout end user who has logged out of OP
- This spec and Front-Channel Logout spec use user agent to communicate logout requests from OP to RPs
- The Back-Channel Logout uses direct back channel to communicate logout requests from OP to RPs
- The RP Initiated Logout is used by RP independently or in conjunction with the above specifications to logout the end user

### Creating and Updating Sessions

- The session is created with RP validates the end user's ID Token ret
