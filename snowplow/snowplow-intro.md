# Snowplow

## User Identifiers

When using JavaScript tracker along with Snowplow Collector, there are two user identifiers are being collected alsong with session information, user ip address and any custom user identifers. The two main identifiers are `domain_userid` and `network_userid`

|Field|	Type|	Description|	Reqd?|	Example|
|-|-|-|-|-|
|`user_id`|	text|	Unique ID set by business|	No|	‘jon.doe@email.com’|
|`domain_userid`|	text|	User ID set by Snowplow using 1st party cookie|	No|	‘bc2e92ec6c204a14’|
|`network_userid`|	text|	User ID set by Snowplow using 3rd party cookie|	No|‘ecdff4d0-9175-40ac-a8bb-325c49733607’|
|`user_ipaddress`|	text|	User IP address|	No|	‘92.231.54.234’|
|`domain_sessionidx`|	int|	A visit / session index|	No|	3|
|`domain_sessionid`|	text|	A visit / session identifier|	No|	‘c6ef3124-b53a-4b13-a233-0088f79dcbcb’|

### `domain_userid`

- Client side identifier
- Set by JavaScript tracker in a cookie within the user browser
- ITP limited the lifetime to 7 days
- It is also used to store the user session data, which helps understand user's data over each of their sessions

### `network_userid`

- Server side identifier
- Set by the snowplow collector
- It is transmitted with every request of JavaScript tracker
- Cookies are blocked when JavaScript tracker and Snowplow collector operate at different domains

## Cookies

Cookies are simple stores of information kept in a user’s browser. Websites use cookies to store information so that it can be persisted across different web pages

### First Party Cookies

- When a cookie is set by the website owner then it is called a `first party cookie` 
- User A visits site `snowplowanalytics.com`. If the Snowplow collector is set up on `collector.snowplowanalytics.com`, then this is a first party cookie

### Second Party Cookies

- When set by third party websites other than the current site then it is called as `third party cookie`
- User A visits site `snowplowanalytics.com`. If the Snowplow collector is set up on `collector.snplow.net`, then this is a third party cookie

### Types of Cookies

#### `__sp_id`

- Initialized by JavaScript tracker
- Expires from 2 years of set date or `cookieLifetime` tracker property
- Stores user info when user first visits a site and updated on subsequent visits. It stores:
  - Unique ID and Session
  - Number of visits
  - Timestamps
- Helps identify users activity across a domain
- `__sp_id.xxxx` maps to `domain_userid` event paramenter

#### `__sp_ses`

- Initialized by JavaScript tracker
- Expires from 30 mins of set date or `sessionCookieTimeout` tracker property
- Used to identify the current user session on a site

#### `sp`

- Setup through collector config
- Stores snowplow collector generated unique id for a user that is sent with all subsequent tracking events
- Can be used as first party cookie when collector is on the same domain as that of the site
- Can be disabled through setting `collector.cookie.enabled` to false
- `sp` maps to `network_userid` event paramenter

### Secure cookies

A Secure cookie is only sent to the server with an encrypted request over the HTTPS protocol

### SameSite cookies

- The `SameSite` attribute of `Set-Cookie` HTTP response header allows us to declare if a cookie should be restricted to a first party or same site context

- Considering any site with any TLD ` .com, .co.uk, .net, etc` and a domain, if there exists several sites with different subdomains associated with them then all these sites are considered to be samesite

- When creating `SameSite` cookie, the attribute can be set with one of four available options
  - `None`: The cookie would be sent with every request irrespective of parent domain. This requires `Secure` attribute to be present
  - `Lax`: This is default. This would restrict the cookies to be sent with requests originating from the ones other than samesites.
  - `Strict`: It will allow cookies to be sent with the requests originating from first party context. That is if the cookie matches the site currently shown in the browser's URL bar