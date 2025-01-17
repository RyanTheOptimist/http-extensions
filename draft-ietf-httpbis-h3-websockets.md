---
title: "Bootstrapping WebSockets with HTTP/3"
number: 9220
docname: draft-ietf-httpbis-h3-websockets-latest
submissiontype: IETF
consensus: true
area: Transport
category: std

ipr: trust200902
area: ART
workgroup: HTTP
keyword: extended connect
keyword: 42
venue:
  group: HTTP
  type: Working Group
  home: https://httpwg.org/
  mail: ietf-http-wg@w3.org
  arch: https://lists.w3.org/Archives/Public/ietf-http-wg/
  repo: https://github.com/httpwg/http-extensions/labels/h3-websockets
github-issue-label: h3-websockets
stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    name: Ryan Hamilton
    organization: Google
    email: rch@google.com

normative:

  HTTP:
    title: "HTTP Semantics"
    date: 2022-04
    seriesinfo:
      RFC: 9110
      DOI: 10.17487/RFC9110
    author:
      -
          ins: R. Fielding
          name: Roy T. Fielding
          org: Adobe
          role: editor
      -
          ins: M. Nottingham
          name: Mark Nottingham
          org: Fastly
          role: editor
      -
          ins: J. Reschke
          name: Julian Reschke
          org: greenbytes
          role: editor

  HTTP2:
    display: HTTP/2
    title: "HTTP/2"
    date: 2022-04
    seriesinfo:
      RFC: 9113
      DOI: 10.17487/RFC9113
    author:
      -
          fullname: Martin Thomson
          org: Mozilla
          role: editor
      -
          fullname: Cory Benfield
          org: Apple Inc.
          role: editor

  HTTP3:
    display: HTTP/3
    title: "Hypertext Transfer Protocol Version 3 (HTTP/3)"
    date: 2022-04
    seriesinfo:
      RFC: 9114
      DOI: 10.17487/RFC9114
    author:
      -
          ins: M. Bishop
          name: Mike Bishop
          org: Akamai
          role: editor

informative:


--- abstract

The mechanism for running the WebSocket Protocol over a single stream
of an HTTP/2 connection is equally applicable to HTTP/3, but the
HTTP-version-specific details need to be specified. This document describes
how the mechanism is adapted for HTTP/3.

--- middle

# Introduction

"Bootstrapping WebSockets with HTTP/2" {{!RFC8441}} defines an extension
to HTTP/2 {{HTTP2}} that is also useful in HTTP/3 {{HTTP3}}.
This extension makes use of an HTTP/2 setting.  {{Appendix A.3 of HTTP3}}
gives some guidance on what changes (if any) are appropriate when porting
settings from HTTP/2 to HTTP/3.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# WebSockets Upgrade over HTTP/3

{{!RFC8441}} defines a mechanism for running the WebSocket Protocol
{{!RFC6455}} over a single stream of an HTTP/2 connection. It defines
an Extended CONNECT method that specifies a new ":protocol"
pseudo-header field and new semantics for the ":path" and ":authority"
pseudo-header fields. It also defines a new HTTP/2 setting sent by a server to
allow the client to use  Extended CONNECT.

The semantics of the pseudo-header fields and setting are identical to those
in HTTP/2 as defined in {{!RFC8441}}. {{Appendix A.3 of HTTP3}} requires that
HTTP/3 settings be registered separately for HTTP/3. The
SETTINGS_ENABLE_CONNECT_PROTOCOL value is 0x08 (decimal 8), as in HTTP/2.

If a server advertises support for Extended CONNECT but receives an
Extended CONNECT request with a ":protocol" value that is unknown or is
not supported, the server SHOULD respond to the request with a 501 (Not
Implemented) status code ({{Section 15.6.2 of HTTP}}). A server MAY
provide more information via a "problem details" response {{!RFC7807}}.

The HTTP/3 stream closure is also analogous to the TCP connection
closure of {{!RFC6455}}. Orderly TCP-level closures are represented as
a FIN bit on the stream ({{Section 4.2 of HTTP3}}). RST exceptions are
represented with a stream error ({{Section 8 of HTTP3}}) of type
H3_REQUEST_CANCELLED ({{Section 8.1 of HTTP3}}).

# Security Considerations

This document introduces no new security considerations beyond those
discussed in {{!RFC8441}}.

# IANA Considerations

This document registers a new setting in the "HTTP/3 Settings"
registry ({{Section 11.2.2 of HTTP3}}).

Value:
: 0x08

Setting Name:
: SETTINGS_ENABLE_CONNECT_PROTOCOL

Default:
: 0

Status:
: permanent

Specification:
: This document

Change Controller:
: IETF

Contact:
: HTTP Working Group (ietf-http-wg@w3.org)

--- back

# Acknowledgments
{:numbered="false"}

This document had reviews and input from many contributors in the IETF HTTP
and QUIC Working Groups, with substantive input from David Schinazi, Martin
Thomson, Lucas Pardue, Mike Bishop, Dragana Damjanovic, Mark Nottingham, and
Julian Reschke.
