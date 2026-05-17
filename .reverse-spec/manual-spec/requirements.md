# MsQuic Requirements Specification

## 1. System Overview

MsQuic is a cross-platform QUIC protocol library for building client and server applications over UDP. It must implement IETF QUIC behavior, integrate TLS 1.3 for authenticated encrypted handshakes, and expose a C ABI that can be called directly from C/C++ or through foreign-function interfaces such as C#, Rust, and other native interop layers.

The library must provide a callback-driven, asynchronous API centered on registrations, configurations, listeners, connections, streams, and datagrams. It must support high-throughput and low-latency use cases, run in user mode on supported platforms, and provide platform-specific TLS and networking backends while preserving a stable application-facing API.

## 2. System Boundaries

MsQuic owns QUIC transport state, packetization, loss recovery, congestion control, stream flow control, TLS integration, UDP datapath management, timers, worker execution, and callback delivery to applications.

Applications own protocol-specific semantics above QUIC, including ALPN selection, credentials, application data framing, certificate policy decisions when custom validation is enabled, stream lifetime choices, receive buffer ownership when advanced modes are selected, and persistence of resumption tickets.

The public API boundary is the function table returned from library open calls. Applications must use object handles and callbacks rather than socket-style polling. MsQuic may create platform worker threads by default, but preview/custom execution modes allow applications to drive execution using platform event queues.

MsQuic does not replace firewall, deployment, load balancer, or application protocol configuration. Operators must permit UDP traffic, configure server certificates, set deployment-specific settings, and ensure QUIC-aware routing when migration or load balancing is required.

## 3. Core Features

### Library Lifecycle

The system must initialize the library when an application opens a supported API version and must return a function table for subsequent operations. The system must release library references when the application closes the function table. Repeated open/close cycles should be supported but are expected to be more expensive than keeping a process-level API table.

The API must follow semantic versioning. Major version changes are required for breaking function signatures, function-table position changes, or behavior changes that break existing clients. Minor versions may add enum values, flags, or functions at the end of the table.

### Object Model

The system must expose a hierarchy of application objects:

- Registration: execution scope and worker-thread context for child objects.
- Configuration: reusable QUIC and TLS settings for connections.
- Listener: server-side acceptor for incoming connections.
- Connection: client or server endpoint state for one QUIC connection.
- Stream: reliable application data channel within a connection.

Each object that can deliver events must have an application callback. Automatically created server-side connections and peer-initiated streams must be handed to the application through callbacks, and the application must attach appropriate callbacks before ownership continues.

### Server Listening

A server must be able to create a listener, start it on a local address and one or more ALPN values, receive new-connection events, and accept or reject each connection. Accepted server connections must become independent of the listener and must be configured with TLS/security settings before the handshake can continue.

The listener must support asynchronous stop and later restart. Closing an active listener must implicitly stop it and release listener resources.

### Client and Server Connections

A client must be able to create a connection and start it toward a remote server using a configuration, address family, server name, and port. A server must receive accepted connection handles through listener callbacks.

Connections must indicate successful establishment, transport shutdown, peer shutdown, local and peer address changes, peer-created streams, stream availability, datagram availability, resumption events, ticket receipt, certificate events, and shutdown completion through connection events.

Applications must be able to shut down a connection with application-controlled flags and error codes. The connection must eventually report shutdown completion, after which the application may close the handle.

### Streams

Streams must be the reliable data exchange primitive. The system must support client-initiated and server-initiated streams, unidirectional and bidirectional streams, local stream creation, peer stream indication, stream start, data send, receive notification, receive completion, receive enable/disable, graceful shutdown, abortive send shutdown, abortive receive shutdown, and final stream close.

Stream ID limits must be controlled by the receiving application through settings for bidirectional and unidirectional streams. The default peer-opened stream count must be zero, so applications must opt in before allowing peer-created streams.

Send calls must be non-blocking. By default, MsQuic may copy send data into internal buffers and complete the application send once buffer ownership returns. When send buffering is disabled, MsQuic must retain application buffers until data is acknowledged and notify applications of ideal send-buffer size so they can keep the connection busy.

Receive events must allow synchronous full acceptance, synchronous partial acceptance, synchronous continuation, and asynchronous processing. If an application returns pending, it must later complete the receive. If it partially accepts data and does not request continuation, receive callbacks must be disabled until explicitly re-enabled.

Advanced receive modes must support multi-receive behavior and application-owned receive buffers. App-owned buffers must place received data directly into buffers provided by the application and must close the connection if more data arrives than can be stored and the application does not provide more buffer space or shut down the stream inline.

### Datagrams

The system must support QUIC unreliable datagrams when enabled by settings and negotiated during handshake. Applications must be able to advertise receive support, learn peer send capability and maximum datagram size, send datagrams, and receive datagram events. Datagram delivery must be secure but not retransmitted on loss.

### TLS and Security

The system must integrate TLS 1.3 according to QUIC-TLS requirements. TLS must not use the normal TLS record layer for QUIC packet protection. Instead, TLS must expose handshake bytes, encryption-level metadata, and packet-protection key material to the QUIC transport.

The system must support QUIC transport parameter exchange through TLS and must support resumption ticket data needed for QUIC 0-RTT compatibility. TLS backends must include platform-appropriate implementations such as Schannel on modern Windows and OpenSSL/quictls on Linux.

Applications must be able to load credentials into configurations, receive peer certificate events when requested, complete asynchronous certificate validation, and query negotiated TLS information such as cipher suite and ALPN.

### Settings and Parameters

The system must allow global, registration, configuration, listener, connection, TLS, and stream parameters to be queried or set according to each parameter's get/set capability. Settings must support both dynamic API configuration and persistent platform configuration where supported.

Settings must include handshake and idle timeouts, flow control windows, stream counts, TLS buffer sizes, send buffering, send pacing, migration support, datagram support, resumption level, MTU discovery, load balancing mode, congestion control, ECN, XDP, version settings, stateless retry, execution configuration, and statistics queries.

Runtime setting updates must apply only where semantically valid. For example, some client connection settings must be set before connection start, global settings affect subsequent objects, and existing connections may not inherit later global or registry changes.

### Version Negotiation

The system must support configurable QUIC version lists and version negotiation extension behavior. Clients must be able to configure acceptable, offered, and fully deployed versions before connection start. Servers must configure version behavior globally before relevant configurations and listeners are created.

Fleet rollouts must allow separate acceptable, offered, and fully deployed version lists so operators can add or remove versions progressively while respecting downgrade risks during partial deployment windows.

### Execution and Threading

By default, MsQuic must create worker threads to execute transport logic, datapath work, and application callbacks. Worker placement should align with processors, NUMA nodes, and RSS behavior to reduce latency and synchronization.

For a given connection and its streams, MsQuic must avoid parallel application upcalls. Listener callbacks for new connections may run in parallel to support server scalability.

Callbacks must be lightweight. Applications that need expensive work must offload it to their own threads and notify MsQuic using the relevant completion APIs. Downcalls made from callback context must be safe from generic deadlock patterns, and recursive callbacks must not occur by default except for explicitly requested inline stream shutdown behavior.

Preview/custom execution mode must allow applications to create execution contexts backed by platform event queues and drive MsQuic by polling those contexts.

### Deployment and Operations

Operators must be able to deploy MsQuic-based protocols over UDP, commonly but not exclusively on port 443. Firewalls and middleboxes must allow UDP traffic without assuming inspectable QUIC internals beyond invariant packet properties.

The system must support deployment scenarios involving NAT rebinding, client migration, keep-alives, stateless retry, worker-overload rejection, and load balancing through encoded server IDs in connection IDs.

Diagnostics must be available through logging, counters, statistics, and troubleshooting documentation. Logging backend availability may vary by platform and build configuration.

### Platform and Extension Support

The system must support platform-specific networking and TLS backends behind common abstractions. It must optionally support Windows XDP-for-Windows datapath acceleration, UDP send/receive coalescing, receive side scaling, and other optimized datapath features when available.

The system may expose preview, experimental, or insecure features only behind opt-in build/API flags or settings, including custom execution, reliable reset, one-way delay, network statistics events, disabled encryption, and related experimental functionality.

## 4. System Assumptions

Applications are expected to understand asynchronous callback ownership and must not block protocol worker callbacks for long periods.

Applications must not modify or free send buffers while MsQuic owns them, and must not retain receive buffer descriptors beyond callback scope unless the documented asynchronous receive contract is followed.

Server applications must configure ALPN and credentials correctly before handshakes can complete. Client applications must use valid server names, addresses, and trust/certificate policies.

Peer stream creation is disabled unless stream count settings allow it. Datagram transmission is unavailable unless both endpoints negotiate support.

QUIC traffic uses UDP, and network infrastructure may change NAT bindings more aggressively than TCP. Operators must configure keep-alives, load balancing, and routing where needed.

Some functionality is platform-dependent. Schannel features depend on sufficiently new Windows versions; Linux builds generally use OpenSSL/quictls; Windows XDP support does not imply Linux XDP support.

## 5. Non-Functional Requirements

MsQuic must be cross-platform and expose a stable C ABI. It must be usable from native applications and foreign-function interfaces.

The implementation must be optimized for both client and server workloads, high throughput, low latency, scalable listener accept paths, efficient worker-thread placement, and minimal synchronization for per-connection processing.

The transport must encrypt packets, authenticate handshakes, protect against spoofed-handshake state exhaustion through stateless retry, and support operator controls for overload and key update behavior.

The API must be asynchronous, event-driven, and non-blocking for common send, receive, connection, listener, and shutdown operations.

The library must provide observability through statistics, counters, build/version metadata, and optional logging. Logging and tracing must be configurable so production users can balance diagnostics with performance.

Settings defaults should be suitable for most deployments. Advanced settings should be available for tested scenarios but should not require most users to tune them.

## 6. Data Model

The primary public entities are API table, registration, configuration, listener, connection, and stream handles. Handles are opaque to applications and must be closed by the appropriate close API when no longer needed.

Settings are represented as structured configuration data with field-presence flags, allowing callers to distinguish explicitly configured values from defaults. Parameters are grouped by global, registration, configuration, listener, connection, TLS, and stream namespaces.

Connection events carry typed payloads for connection establishment, shutdown, address changes, peer streams, stream availability, datagram negotiation, datagram data, resumption, certificates, and statistics.

Stream events carry typed payloads for start completion, receive buffers, send completion, peer shutdown, local shutdown completion, full stream shutdown, ideal send buffer size, peer acceptance, cancellation-on-loss, and preview receive-buffer needs.

Protocol data includes connection IDs, stream IDs, QUIC versions, transport parameters, TLS handshake data, resumption tickets, datagram payloads, packet numbers, ACK/loss state, congestion-control state, flow-control windows, and statistics.

## 7. Business Rules

Applications must call the matching close API for handles they own. Accepted server connections and indicated peer streams become application-owned only when the application accepts them according to callback contract.

A server that accepts a new connection must set a connection callback handler and must provide a configuration for the handshake to continue.

An application must not close a rejected listener-provided connection as if it owned it. If it accepts the connection, it becomes responsible for eventual connection close.

Peer-created streams must immediately receive a stream callback handler. Locally created streams do not affect the wire until started.

Send buffer ownership transfers to MsQuic after successful send queueing and returns only through send-complete events. Receive buffers must be completed or accepted according to synchronous, partial, asynchronous, multi-receive, or app-owned-buffer rules.

Connection and stream shutdown are separate from handle close. Applications should wait for shutdown-complete events before ordinary close unless intentionally performing abortive behavior documented by the API.

Version settings for clients must be set before connection start. Version settings for servers must be set globally before server configurations/listeners are created.

Registry or persistent settings provide defaults, but explicit application settings override them. Invalid persistent values must be ignored in favor of built-in defaults.

Datagram send is valid only after peer datagram receive support is negotiated. 0-RTT stream data is only attempted when the application opts in and keys/protocol conditions allow it.

Load balancing, migration, and NAT rebinding support depend on deployment configuration. Without QUIC-aware load balancing, tuple changes may route packets incorrectly; keep-alives may be required to reduce NAT rebinding risk.
