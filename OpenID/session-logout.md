# Session Logout

## Introduction

Session is a continuous period of time an end user is authenticated by OpenID Provider(OP) and is able to access authorized Relying Party(RP).

The session of an end user typically starts when RP gets and validated an ID Token received from OP as a result of successful authentication. The OP would include a special param `session_state` in the authentication response. It is string that represents end user's login state at OP.

The `session_state` is a value of salted cryptographic hash of Client ID, Origin URI, and OP user agent state. The value is recalculated by the OP iframe.

## Session Monitoring

RP's need a way to be able to monitor authenticated end user state at OP. Following the `exp` claim in the ID Token is a way to logout user. If the user logged out at OP even before `exp` claim then RP need a way to logout end user and this can be accomplished by making authentication requests with `prompt=none`(does not prompt end user to enter credentials). However, this could result in high network traffic to OP. A potential solution to this problem is through iframes. RP has its own invisible iframe that polls OP iframe's `postMessage` response for the end user's state.



### RP iframe

OpenID Connect session management works with two hidden iframes where both reside at the RP. One is from the RP itself and the other is from the OP. When authenticating, the OP sends an iframe to the RP. The RP embed this OP-provided iframe into the RP. The RP checks the session state via the RP iframe by continuously polling the embedded OP provided iframe, without causing network traffic. Thereby, the RP is notified when the session state of the end-user has changed. The RP does `postMessage` of `Client ID + " " + Session State` to OP iframe. 

1. The RP iframe polls the OP iframe for a session status.
1. The OP iframe sends back a message (by using HTML5 Winodw.postMessage()) about the session state as 'changed, 'unchanged' or 'error'.
1. If the session state is 'changed', the RP sends a passive request for re-authentication.
1. If the end user has logged out from the OP, the RP will receive an authentication failure message along with a new session state value. The RP handles this as a end user logout.
1. If the end user has not logged out, the RP will receive a successful authentication response along with a new session state value.
### OP iframe

The RP saves few endpoints including `check_session_iframe` and `end_session_endpoint` by parsing discovery endpoint response from the OP. The RP iframe loads another iframe using the source URI