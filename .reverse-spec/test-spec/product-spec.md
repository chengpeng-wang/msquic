# MsQuic Test-Derived Product Specification

## Feature 001: Public API Validation

MsQuic must reject invalid public API inputs with deterministic status codes. Null output pointers, null object handles where handles are required, invalid ALPN buffers, unsupported get/set parameter operations, incorrect buffer lengths, and invalid state transitions must fail without corrupting library state.

## Feature 002: Registration and Configuration Lifecycle

Applications must be able to open and close registrations, create configurations with valid ALPN and settings, load credentials, and use configurations for client and server connections. The system must support synchronous registration close and preview asynchronous close when available.

## Feature 003: Listener Binding and ALPN Routing

Server applications must be able to create listeners, start them on implicit or explicit IPv4/IPv6 addresses, expose selected local addresses, and advertise one or more ALPN values. Multiple listeners may share a port only when their ALPN sets do not conflict; overlapping ALPNs on the same address must be rejected.

## Feature 004: Connection Creation, Binding, and Parameters

Client applications must be able to open connections, optionally bind local addresses or interfaces, start connections to local or remote endpoints, query local and remote addresses, and retrieve/update supported connection parameters. Connection parameters must include QUIC version, settings, statistics, stream counts, close reason phrase, scheduling scheme, datagram capability, DSCP, TLS secrets, original destination CID, network statistics, and close mode.

## Feature 005: Handshake, ALPN, Certificates, and Resumption

MsQuic must complete client/server handshakes over IPv4 and IPv6 with supported ALPNs, optional retry, QUIC grease behavior, multiple client initials, asynchronous configuration, certificate validation variants, TLS secret capture, and session resumption. Failed ALPN, SNI, certificate, version, and reachability cases must produce expected connection failure behavior.

## Feature 006: QUIC Version Negotiation

Connections must support default versions, old-version attempts, version negotiation, compatible version negotiation, incompatible version rejection, failed version negotiation, and version negotiation extension transport parameters. Version settings must affect both client connection start and server negotiation behavior according to configured lists.

## Feature 007: Stream Data Transfer and Flow Control

Connections must support reliable data exchange across multiple streams, bidirectional and unidirectional modes, client-initiated and server-initiated streams, FIFO and non-FIFO scheduling, 0-RTT send attempts, key updates during active traffic, partial/incomplete data handling, and connection or stream shutdown propagation.

## Feature 008: Stream Events and Ownership

The stream event model must report start completion, receive, send completion, peer send shutdown, peer send abort, peer receive abort, send shutdown completion, full shutdown completion, ideal send buffer size, peer acceptance, and cancel-on-loss behavior in valid order. Peer-created streams must be accepted with callbacks and must expose expected stream flags.

## Feature 009: Receive Buffer Modes

The receive buffer must support single, circular, multiple, and application-owned modes. It must correctly handle gaps, overlapping writes, partial drains, virtual length growth, pending reads, out-of-buffer conditions, external buffer ownership, and reset behavior.

## Feature 010: Datagram Negotiation and Delivery

Datagram send must be initially queueable but actual send capability must follow negotiated peer receive support. If both peers enable datagrams, datagram sends must complete and peers must receive them; if the peer does not enable receiving, queued datagrams must be canceled. Datagram behavior must work for IPv4 and IPv6.

## Feature 011: QUIC Frame and VarInt Encoding

Core framing must encode and decode ACK, stream, crypto, token, flow-control, connection ID, path challenge/response, connection close, reset stream, reliable reset, stop sending, and blocked frames. Malformed frames must fail decoding safely and mark invalid frames where appropriate. Variable-length integer encoding and packet number decompression must match known values and randomized round trips.

## Feature 012: Packet Number Ranges and ACK Tracking

Range tracking must merge adjacent and overlapping packet number ranges, preserve separated ranges, search for individual values or ranges, enforce allocation limits, and support ACK/loss examples. ACK tracking must detect reordering threshold hits.

## Feature 013: Congestion Control and Pacing

CUBIC and BBR congestion control must initialize with expected defaults, update congestion windows on ACK/loss/ECN events, handle persistent congestion, avoid double reductions during recovery, process spurious congestion, support pacing calculations, produce network statistics where enabled, and implement HyStart and BBR state transitions correctly.

## Feature 014: Platform Datapath and Address Utilities

The platform layer must initialize datapaths, validate invalid initialization, bind and rebind UDP sockets, support UDP data over IPv4 and IPv6, support polling mode, preserve ECN/DSCP where available, share client sockets, support multiple listeners, and exercise TCP listener/connect/reject/disconnect/data paths used by platform support.

## Feature 015: TLS and Cryptographic Primitives

The TLS/crypto layer must derive QUIC initial keys from known test vectors, support AES-GCM and ChaCha20 packet protection where available, compute HKDF and hashes, perform TLS handshakes, negotiate ALPN and cipher suites, handle certificate file/self-signed/CA/client-certificate cases, support custom and deferred certificate validation, support resumption and rejection, and handle fragmented handshakes.

## Feature 016: Diagnostics, Statistics, and Storage

The system must expose global performance counters, connection statistics, platform-format statistics, network statistics events, stream statistics, library version/hash, TLS provider, persistent settings storage where supported, and settings size discovery for compatibility.
