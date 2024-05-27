# Session Logout

## Introduction

Session is a continuous period of time an end user is authenticated by OpenID Provider(OP) and is able to access authorized Relying Party(RP).

The session of an end user typically starts when RP gets and validated an ID Token received from OP as a result of successful authentication. The OP would include a special param `session_state` in the authentication response. It is string that represents end user's login state at OP.

The `session_state` is a value of salted cryptographic hash of Client ID, Origin URI, and OP user agent state. The value is recalculated by the OP iframe.

## RP Initiated Logout

The RP request OP to logout the authenticated end user. This is done through redirecting end user to the logout endpoint of the OP. The logout endpoint is obtained from `end_session_endpoint` from the OPs discovery endpoint response.

### Terminology

The id_token_hint is recommended and rest of the params are optional.

#### id_token_hint

The ID Token previously issued by the OP to RP. This needs to be passed to logout endpoint as a hint about end user to be logged out


#### logout_hint

It's an hint to OP about the end user being logged out. Example values include email, username, and sid.

#### client_id

Client Identifier through which OP can identify the client

#### post_logout_redirect_uri

The RP URI to which end user is redirected after a successful logout

#### state

String value to maintain state between logout and redirection to `post_logout_redirect_uri`

### Rules

- RP can either use GET or POST methods to send logout requests to OP.
- When id_token_hint is included, the OP should verify issuer, exp, aud, and/or Sid claims as part of the validation. If same is not included then the OP should ask user if wish to logout globally and if user confirms yes then OP uses one of the below mentioned logout schemes to perform a global logout.

## Session Management

RP's need a way to be able to monitor authenticated end user state at OP. Following the `exp` claim in the ID Token is a way to logout user. If the user logged out at OP even before `exp` claim then RP need a way to logout end user and this can be accomplished by making authentication requests with `prompt=none`(does not prompt end user to enter credentials). However, this could result in high network traffic to OP. A potential solution to this problem is through iframes. RP has its own invisible iframe that polls OP iframe's `postMessage` response for the end user's state.

### RP iframe

OpenID Connect session management works with two hidden iframes where both reside at the RP. One is from the RP itself and the other is from the OP. When authenticating, the OP sends an iframe to the RP. The RP embed this OP-provided iframe into the RP. The RP checks the session state via the RP iframe by continuously polling the embedded OP provided iframe, without causing network traffic. Thereby, the RP is notified when the session state of the end-user has changed. The RP does `postMessage` of `Client ID + " " + Session State` to embedded OP iframe. Below are the steps as follows

1. The RP iframe polls the OP iframe for a session status.
1. The OP iframe sends back a message (by using HTML5 Window.postMessage()) about the session state as 'changed, 'unchanged' or 'error'.
1. If the session state is 'changed', the RP sends a passive request for re-authentication.
1. If the end user has logged out from the OP, the RP will receive an authentication failure message along with a new session state value. The RP handles this as a end user logout.
1. If the end user has not logged out, the RP will receive a successful authentication response along with a new session state value.

### OP iframe

The RP saves few endpoints including `check_session_iframe` and `end_session_endpoint` by parsing discovery endpoint response from the OP. The URI found under `check_session_iframe` property is used as the source for embedded iframe in RP iframe. The embedded iframe has access to user agent state (stored in a cookie or local storage) of OP. The RP iframe polls this embedded iframe for monitoring session of end user. The OP updates the user agent state based on the events triggered by end user like login, logout etc. This updated state is tracked by the OP or embedded iframe, which sends the response based on respective updated state.

### User Agent Blocking Third Party Cookies

Some browers are starting to block third party cookies which is creating problems in tracking end user state across sites. The website data can include cookies and session or local storage. Specifically, the website content from site on one origin cannot be accessed by the website on a different origin currently active on a tab in user agent window.

The result of blocking third party cookies is that, the OP iframe cannot access end user state and thus returns `changed` for every callout from RP iframe and this results in infinite loop of re-authentications. The back channel logout is not affected by this blockage.

## Front Channel Logout

Similar to session management, this logout mechanism also uses user agent and iframes to logout users but the only difference is that it does not require RP to have an iframe, instead HTTP GET calls are made to RP URLs to logout the user.

The primary requirement for this feature to work is that RP should support HTTP-based logout and it should be able to register a logout URI with the OP. 

The OP renders `<iframe src="frontchannel_logout_uri">` in a page with the registered logout URI as the source to trigger the logout actions by the RP. Upon receiving a request to render the logout URI in an iframe, the RP clears state associated with the logged-in session, including any cookies and HTML5 local storage.

Optionally the OP can also include `iss` and `sid` in the logout request for RP to consume so that the RP can compare the values with the ones in the saved ID Token received after successful authentication. If the comparison fails, the RP can simply ignore the logout request from the OP.

The RP optionally can register `frontchannel_logout_session_required`, a boolean value that insists OP to include `iss` and `sid` in the logout request to the RP.

The OP has to provide `frontchannel_logout_supported`, boolean value in the response of the discovery endpoint so that RP can know if the OP has support for Front Channel Logout.

### User Agent Blocking Third Party Cookies

Due to the blocking of third party cookies, the iframe loaded by OP with RP provided logout URI   would fail and the reason for that is the `frontchannel_logout_uri` in the OP iframe would be blocked from accessing end user's login state as the OP iframe and RP belong to different origins. The back channel logout wouldn't be impacted by this blockage.

## Back Channel Logout

The back channel logout does not use user agents and iframes to communicate logout requests to RPs like the above mentioned methods do, instead it uses direct back channel communication.

The advantage with back channel logout is that it does not depend on user agent tab to be active which is a must for other methods to work. However one down side is that it does not have access to website content like cookies or session/local storage and due to which it has no access to end user data. This needs to be taken care by OP by sending state information explicitly for every logout request sent to RP. Other downsides are that the RP has to create custom logic to remove user state unlike just removing cookies and clearing storage in case of front channel logout, and the RP has to register a logout URI with OP and ensure the OP is able to reach it and not blocked by any firewalls.

### OP Support For Back Channel Logout

The OP advertises `backchannel_logout_supported`, boolean metadata value in the response of the discovery endpoint so that RP can know if the OP has support for Back Channel Logout. It also has an optional metadata value called `backchannel_logout_session_supported`, through which it supports passing `sid` claim in the Logout Token. Know that it's already being passed in an ID Token so the RPs could validate it before performing actual logout.

### RP Support For Back Channel Logout

The RP has to register `backchannel_logout_uri` and optionally `backchannel_logout_session_required`, a boolean value to insist OP to pass `sid` in the Logout Token.

### OP Remembering RPs

The OP needs to know which RPs need to be sent logout requests through Back Channel and for this purpose it tracks `visited sites` cookie.

### Logout Token

The token includes mandatory claims such as `iss`, `aud`, `iat`, `exp`, `jti, and `events`.

The token can optionally include `sub` and/or `sid`.

The Logout Token may contain either `sid` or `sub`. If `sid` is excluded then all the sessions identified by `sub` and `iss` are logged out.

The `nonce` claim is prohibited from being added to Logout Token as this can prevent attackers from reusing Logout Token as an ID Token.

