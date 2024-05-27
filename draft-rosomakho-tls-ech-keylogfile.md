---
title: "SSLKEYLOGFILE extensions for Encrypted Client Hello"
category: info

docname: draft-rosomakho-tls-ech-keylogfile-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Transport Layer Security"
keyword:
 - encrypted client hello
 - sslkeylog
venue:
  group: "Transport Layer Security"
  type: "Working Group"
  mail: "tls@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/tls/"
  github: "yaroslavros/tls-ech-keylog"
  latest: "https://yaroslavros.github.io/tls-ech-keylog/draft-rosomakho-tls-ech-keylogfile.html"

author:
 -
    ins: "Y. Rosomakho"
    fullname: Yaroslav Rosomakho
    organization: Zscaler
    email: yrosomakho@zscaler.com

normative:

informative:


--- abstract

This document specifies an extension to the SSLKEYLOGFILE format {{!I-D.ietf-tls-keylogfile}} to support the logging information about Encrypted Client Hello (ECH) {{!I-D.ietf-tls-esni}} related secrets. Specifically, it introduces two new labels: ECH_SECRET and ECH_CONFIG, which respectively log the HPKE {{?RFC9180}} shared secret and the ECHConfig used for the ECH. This extension aims to facilitate debugging and analysis of TLS connections employing ECH.


--- middle

# Introduction

The SSLKEYLOGFILE format, used for information about the secrets used in a TLS connection, is instrumental in debugging and analyzing TLS traffic. With the introduction of Encrypted Client Hello (ECH), additional secret is used during the handshake process. This document extends the SSLKEYLOGFILE format to include relevant information to decrypt ECH extension, enabling comprehensive analysis of ECH-enabled connections. This document also clarifies logging of TLS 1.3 secrets if ECH was successfully negotiated.

Proposed extensions can also be used with all protocols that support ECH including TLS 1.3 {{?RFC8446}}, DTLS 1.3 {{?RFC9147}} and QUIC {{?RFC9000}}{{?RFC9001}}.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# SSLKEYLOGFILE Labels for ECH

This document defines two new labels for SSLKEYLOGFILE format: ECH_SECRET and ECH_CONFIG. Client SHOULD log those labels if it offered ECH regardless of server acceptance. Server can log those labels only if it decrypted and accepted ECH from the client. The 32-byte Random from the Outer ClientHello message is used as client_random for these log records. Client MUST NOT log those labels for connections that use GREASE ECH extension.


## ECH_SECRET

This label corresponds to KEM shared secret derived during HPKE key schedule process. Length of the secret is defined by KEM that was used for ECH.


## ECH_CONFIG

This label is used to log ECHConfig that was used for construction of ECH extension. Note that the value is logged in hexadecimal, similarly to other entries in SSLKEYLOGFILE.


# Client_random for other TLS 1.3 SSLKEYLOGFILE entries

SSLKEYLOGFILE is using Random from ClientHello as the connection identifier. This creates ambiguity since TLS handshake with ECH contains two different Random values in Outer ClientHello and Inner ClientHello.

SSLKEYLOGFILE entries corresponding to TLS 1.3 secrets for connections that successfully negotiated ECH MUST use Random from Inner ClientHello. In all other cases Random from Outer ClientHello is to be used.


# Security Considerations

Access to ECH_SECRET record in SSLKEYLOGFILE allows attacker to decrypt ECH extension and defeat privacy benefits offered by the ECH. Security considerations described in SSLKEYLOGFILE are fully applicable to this extension.

Ability to log KEM shared secret from HPKE key schedule introduces potential attack surface against HPKE process. Implementers need to secure relevant APIs accordingly to ensure that no unauthorised party can extract the KEM shared secret.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
