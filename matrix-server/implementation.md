# Lynix's matrix server writeup

Documentation of how matrix server is implemented in lynix's infra

> **Motivation**
> 
> Because discord bad and is spying on users we have decided to move to matrix.
> Because matrix can be referred to as "complex" this writeup was made.

## Basics

We require 3 domain:
1. main - used as main matrix domain
2. client - used to connect clients to home server software
3. federation - used to allow communication between home servers

The software we are using is: 
 - Dendrite - https://github.com/matrix-org/dendrite
 - Bridges (used to break discord TOS (very important))

## Server/Client discovery

This part is based off
 - https://spec.matrix.org/v1.7/server-server-api/#server-discovery
 - https://spec.matrix.org/v1.7/client-server-api/#server-discovery

The main domain webserver need to have 2 additonal routes (they are added via traefitek)

For federation:
```
GET {main}/.well-known/matrix/server 
```
```json
{
  "m.server": "federation <see Basics federation>:<federation port (443)>"
}
```

For client:
```
GET {main}/.well-known/matrix/client 
```
```
{
  "m.homeserver": {
    "base_url": "https://client <see Basics client>"
  }
}
```

**REMEMBER TO SET THE TYPE TO `aplication/json`**

## Reverse Dendrite proxy

We require a reverse proxy with SSL/HTTPS support that routes:

```
https://client <see Basics client> ===> Dendrite:ClientPort
```
and:

```
https://federation <see Basics federation> ===> Dendrite:FederationPort
```

## Dendrite config

This part is based off https://matrix-org.github.io/dendrite/installation/docker/install
