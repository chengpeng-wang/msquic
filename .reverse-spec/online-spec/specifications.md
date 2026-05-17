# MsQuic Online Specification Sources

MsQuic implements the IETF QUIC transport family and several negotiated extensions. The following official or primary sources define the protocol behavior relevant to this project.

## Core Standards

### RFC 8999: Version-Independent Properties of QUIC

Source: https://www.rfc-editor.org/rfc/rfc8999

RFC 8999 defines QUIC invariants that are intended to remain stable across QUIC versions, including long-header structure, version field use, version negotiation packet basics, and connection ID concepts. MsQuic's version negotiation, packet parsing, and deployment assumptions rely on these invariants.

### RFC 9000: QUIC Transport

Source: https://www.rfc-editor.org/rfc/rfc9000.html

RFC 9000 defines the QUIC transport protocol: connections over UDP, streams, flow control, connection IDs, packet numbers, frames, transport parameters, migration, connection close, and error handling. MsQuic's core connection, stream, frame, receive buffer, datagram routing base, and transport parameter behavior are primarily grounded in this RFC.

### RFC 9001: Using TLS to Secure QUIC

Source: https://www.rfc-editor.org/rfc/rfc9001.html

RFC 9001 defines how QUIC uses TLS 1.3 without the TLS record layer, maps TLS handshake data to CRYPTO frames, derives packet protection keys, and manages encryption levels. MsQuic's TLS abstraction, key derivation, certificate validation integration, ALPN negotiation, 0-RTT behavior, and resumption behavior are tied to this specification.

### RFC 9002: QUIC Loss Detection and Congestion Control

Source: https://www.rfc-editor.org/rfc/rfc9002

RFC 9002 defines QUIC loss detection, packet number spaces, PTO, RTT estimation, ACK handling, and congestion control requirements. MsQuic's loss recovery, ACK tracking, pacing, CUBIC/BBR integration points, and network statistics behavior implement or extend this area.

## Extension Standards

### RFC 9221: QUIC Datagrams

Source: https://www.rfc-editor.org/rfc/rfc9221

RFC 9221 defines unreliable DATAGRAM frames for QUIC. MsQuic's datagram negotiation, datagram send/receive events, and cancellation behavior are implementations of this extension.

### RFC 9287: Greasing the QUIC Bit

Source: https://www.rfc-editor.org/rfc/rfc9287.html

RFC 9287 defines greasing behavior for the QUIC bit to prevent middlebox ossification. MsQuic exposes Grease QUIC Bit settings and tests connection behavior when this setting is enabled.

### RFC 9368: Compatible Version Negotiation for QUIC

Source: https://www.rfc-editor.org/rfc/rfc9368.html

RFC 9368 extends QUIC version negotiation to allow mutually supported version selection, including compatible negotiation without an extra round trip where possible. MsQuic's version settings and version negotiation extension tests map to this standard.

### RFC 9369: QUIC Version 2

Source: https://www.rfc-editor.org/rfc/rfc9369.html

RFC 9369 defines QUIC version 2 and its differences from version 1. MsQuic advertises support for RFC 9369 and includes version negotiation behavior that can select among supported versions.

## Active IETF Drafts Used By MsQuic Features

### QUIC-LB: Generating Routable QUIC Connection IDs

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-load-balancers/

This draft defines routable connection ID schemes for QUIC load balancing. MsQuic includes load balancing modes that encode server routing information into connection IDs.

### QUIC Acknowledgment Frequency

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-ack-frequency/

This draft defines ACK_FREQUENCY behavior that allows endpoints to request different acknowledgment behavior. MsQuic references ACK frequency support as an extension area.

### Reliable Stream Reset

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-reliable-stream-reset/

This draft defines reset behavior that preserves reliable delivery up to a specified stream offset. MsQuic includes reliable reset preview support and related frame tests.

## Applicability Notes

MsQuic is a QUIC transport library, not an HTTP/3 implementation. HTTP/3, QPACK, and application protocol RFCs are relevant to applications built on top of MsQuic but are not direct requirements for this library's core transport API.

MsQuic also includes platform-specific APIs and implementation choices, such as Windows XDP-for-Windows integration, Schannel/OpenSSL backends, worker-thread execution, and application callback contracts. Those are project API requirements rather than IETF protocol standards.
