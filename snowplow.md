# Snowplow Cookieless Tracking

## User Identifiers

### domain_userid

    - needs a single stream collector to collect from top level domain
    - cookie set at client side
    - cookie used to store client session data as well
    - ITP has it limited to 7 days

### network_userid

    - server side identifier
    - cookie set by stream collector
    - thrid party cookies are being blocked by ITP especially when stream collector operates at a different domain than the website
	
## Cookieless tracking

### Cookieless and Anonymous 

- Track page view without any identifiers optionally
- Enable or disable anonymous tracking

```yaml
snowplow("newTracker", "sp", "{{collector_url_here}}", {
  appId: "my-app-id",
  eventMethod: “post”,
  anonymousTracking: { withServerAnonymisation: true },
  stateStorageStrategy: "none",
  contexts: {
    webPage: true
  }
});
```
### Disable Server side Cookies but capture IP address

- Disable server side or collector cookies by setting

```yaml
cookie {
  enabled = false
}
```

- Captures IP address but cookies are not saved in the browser
- Possible to capture user agent information as well

```yaml
anonymousTracking: true
```

### Enable browser only cookies

- Create and preserve client side cookies to capture user session data

```yaml
anonymousTracking: { withSessionTracking: true }
```

#### Just sessions but not cookies

- Removes cookies all together but still manages to track user sessions

```yaml
anonymousTracking: { withSessionTracking: true, withServerAnonymisation: true }
```

## IP Anonymization

- Replace segments of IP address with `X`
- Ex.: IPv4 address of `255.255.255.255` to `255.255.x.x.`

## Summary

|Approach|cookies required?|user identifiers|consent banner|enrichments|
|-|-|-|-|-|
|Track with no cookies but capture IP address|No|user_ipaddress|yes|IP Anonymization|
|Track anonymously all the time|No||optional||