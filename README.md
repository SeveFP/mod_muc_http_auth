# Introduction

This module externalizes MUC authorization via HTTP.  
Whenever a user wants to join a MUC, an HTTP GET request is made to `authorization_url`
with the user bare jid (`userJID`) and the MUC jid (`mucJID`) as GET parameters.  
Example:  
`https://www.prosody.im/users/can-join/?userJID=romeo@example.com&mucJID=teaparty@chat.example.com`

This allows an external service to decide whether a user is authorized to join a MUC or not.  

When a user is authorized to join a MUC, this module expects the following JSON payload:
```
{
    allowed: true,
    error: "",
}
```
Otherwise, either the user not being authorized or some failure in the external service:
```
{
    allowed: false,
    error: "Some error message to be displayed in this module's logs",
}
```

# Configuring

## Enabling

``` {.lua}
Component "rooms.example.net" "muc"

modules_enabled = {
    "muc_http_auth";
}

```


## Settings

|Name |Description |Default |
|-----|------------|--------|
|muc_http_auth_url| URL of the external HTTP service to which send `userJID` and `mucJID` in a GET request | "" |
|muc_http_auth_enabled_for| List of MUC names (node part) to enable this module for | nil |
|muc_http_auth_disabled_for| List of MUC names (node part) to disable this module for | nil |
|muc_http_auth_insecure| Disable certificate verification for request. Only intended for development of the external service. | false |


This module can be enabled/disabled for specific rooms. Only one of the following settings must be set.
```
-- muc_http_auth_enabled_for = {"teaparty"}
-- muc_http_auth_disabled_for = {"teaparty"}
```
If none is set, all rooms in the MUC component will have this module enabled.

Note: Use the node part of the MUC jid for these lists. Example:  

Wrong:
`muc_http_auth_enabled_for = {"teaparty@rooms.example.net"}`

Correct:
`muc_http_auth_enabled_for = {"teaparty"}`