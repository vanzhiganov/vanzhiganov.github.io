---
layout: post
title: "BIND - delegate a sub domain for a zone"
subtitle: "delegate a sub domain for a zone"
tags: [dns, bind9]
---

The scenario:

- DNS zone `myzone.com` defined in BIND.
- Authoritative name server at `123.16.123.1`.
- Subzone `sub.myzone.com` with an authoritative name server at `123.16.123.10`.
- Wishing to forward sub-zone to authoritative name server.

## Config

```
$ORIGIN myzone.com.
$TTL 1D
@ IN SOA ns1.myzone.com. hostmaster.myzone.com. (
  2015010100 ; serial
  21600      ; refresh
  3600       ; retry
  604800     ; expire
  86400 )    ; minimum TTL
;
@       IN  NS  ns1
ns1     IN  A   123.16.123.1  ; glue record
ns1sub  IN  A   123.16.123.10 ; glue record
;
;
$ORIGIN sub.myzone.com.
$TTL 1D
@ IN NS ns1sub.myzone.com.
```

## How it works

- Resolver asks for answer to `foo.sub.myzone.com` from name server.
- Via `sub.myzone.com` zone, is given answer of `ns1sub.myzone.com` as the authoritative name server.
- Resolver able to resolve IP address of `123.16.123.10` for `ns1sub.myzone.com` via parent zone A "glue" record.
- Now resend original query of `foo.sub.myzone.com` to nameserver server at `123.16.123.10`.
- All done.

## Reference

- https://www.zytrax.com/books/dns/ch9/delegate.html
