# Session Logout

## Introduction

Session is a continuous period of time an end user is authenticated by OpenID Provider(OP) and is able to access authorized Relying Party(RP).

The session of an end user typically starts when RP gets and validated an ID Token received from OP as a result of successful authentication. The OP would include a special param `session_state` in the authentication response. It is string that represents end user's login state at OP.

The `session_state` is a value of salted cryptographic hash of Client ID, Origin URI, and OP user agent state. The value is recalculated by the OP iframe.

## Session Monitoring

RP's need a way to be able to monitor authenticated end user state at OP. Following the `exp` claim in the ID Token is a way to logout user. If the user logged out at OP even before `exp` claim then RP need a way to logout end user and this can be accomplished by making authentication requests with `prompt=none`(does not prompt end user to enter credentials). However, this could result in high network traffic to OP. 