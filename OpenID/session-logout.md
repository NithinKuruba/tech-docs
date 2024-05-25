# Session Logout

## Introduction

Session is a continuous period of time an end user is authenticated by OpenID Provider(OP) and is able to access authorized Relying Party(RP).

The session of an end user typically starts when RP gets and validated an ID Token received from OP as a result of successful authentication. The OP would include a special param `session_state` in the authentication response. It is string that represents end user's login state at OP.

The `session_state` is a value of salted cryptographic hash of Client ID, Origin URI, and OP user agent state. The value is recalculated by the OP iframe.

## Session Monitoring

RP's need a way to be able to monitor authenticated end user state at OP. Following the `exp` claim in the ID Token is a way to logout user. If the user logged out at OP even before `exp` claim then RP need a way to logout end user and this can be accomplished by making authentication requests with `prompt=none`(does not prompt end user to enter credentials). However, this could result in high network traffic to OP. A potential solution to this problem is through iframes. RP has its own invisible iframe that polls OP iframe's `postMessage` response for the end user's state.

### RP iframe

The RP loads an invisible iframe which knows ID and origin URI of the OP iframe. The RP does `postMessage` of `Client ID + " " + Session State` to OP iframe and it receives one of the three responses `unchanged`, `changed`, and `error`.

Upon receipt of response `changed`, the RP makes authentication request with `prompt=none` and if it receives new ID Token from OP, it simply updates the state at its end. The RP iframe continues the polling again. In case of receiving `unchanged`, the RP does nothing. The end user will be forced to login if RP receives `error` response.

### OP iframe

The RP saves few endpoints including `check_session_iframe` and `end_session_endpoint` by parsing discovery endpoint response from the OP. The RP iframe loads another iframe using the source URI 