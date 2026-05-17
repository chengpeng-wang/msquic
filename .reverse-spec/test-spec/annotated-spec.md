# MsQuic Annotated Test-Derived Specification

## Feature 001: Public API Validation

MsQuic must reject invalid public API inputs with deterministic status codes and preserve state.

Supporting tests: `ParameterValidation.ValidateApi`, `ValidateRegistration`, `ValidateConfiguration`, `ValidateListener`, `ValidateConnection`, `ValidateStream/*`, `ValidateParamApi`, `src/test/lib/ApiTest.cpp`.

## Feature 002: Registration and Configuration Lifecycle

Registrations and configurations must open, close, carry ALPN/settings, and support credential loading.

Supporting tests: `Basic.RegistrationOpenClose`, `ParameterValidation.ValidateConfiguration`, `ParameterValidation/WithValidateTlsConfigArgs.ValidateTlsConfig/*`, `src/test/lib/BasicTest.cpp`, `src/test/lib/ApiTest.cpp`.

## Feature 003: Listener Binding and ALPN Routing

Listeners must bind IPv4/IPv6 addresses, report local address, support multiple ALPNs, and reject conflicting ALPN use.

Supporting tests: `Basic.CreateListener`, `StartListener`, `StartListenerMultiAlpns`, `StartTwoListeners`, `StartTwoListenersSameALPN`, `Basic/WithFamilyArgs.StartListenerImplicit/*`, `StartListenerExplicit/*`.

## Feature 004: Connection Creation, Binding, and Parameters

Connections must support creation, local binding, address queries, stream-count queries, statistics, scheduling, DSCP, datagram flags, TLS secrets, and other get/set parameters.

Supporting tests: `Basic.CreateConnection`, `BindConnectionImplicit/*`, `BindConnectionExplicit/*`, `TestAddrFunctions/*`, `ParameterValidation.ValidateConnectionParam`, `ValidateTlsParam`, `ValidateStreamParam`, `ValidateGetPerfCounters`, `src/test/lib/ApiTest.cpp`.

## Feature 005: Handshake, ALPN, Certificates, and Resumption

The handshake path must succeed for valid client/server combinations and fail predictably for invalid address, ALPN, SNI, certificate, and resumption scenarios.

Supporting tests: `Handshake/WithHandshakeArgs1.Connect/*`, `Resume/*`, `ResumeAsync/*`, `ResumeRejection/*`, `HandshakeTest.InvalidAddress`, `Basic/WithFamilyArgs.BadALPN/*`, `BadSNI/*`, `ServerRejected/*`, TLS certificate validation tests in `src/test/lib/HandshakeTest.cpp` and `src/platform/unittest/TlsTest.cpp`.

## Feature 006: QUIC Version Negotiation

The implementation must support version negotiation behavior, compatible negotiation, incompatible/failing cases, and version negotiation extension state.

Supporting tests: `Basic/WithFamilyArgs.VersionNegotiation/*`, `VersionNegotiationRetry/*`, `CompatibleVersionNegotiationRetry/*`, `IncompatibleVersionNegotiation/*`, `FailedVersionNegotiation/*`, `VersionNegExtTest.*`, `VersionNegExtTest/WithType.*`, `TransportParamTest.VersionNegotiationExtension*`.

## Feature 007: Stream Data Transfer and Flow Control

MsQuic must move application data reliably across stream combinations, honor stream limits, propagate shutdown, support 0-RTT attempts, and keep stream completion accounting correct.

Supporting tests: `DataTest.cpp` ping/data tests, `Misc.StreamPriority`, `StreamMultiReceive`, `StreamBlockUnblockBidiConnFlowControl`, `StreamBlockUnblockUnidiConnFlowControl`, `StreamAbortConnFlowControl`, `StreamReliableReset*`, `StreamAppProvidedBuffers*`.

## Feature 008: Stream Events and Ownership

The event model must deliver expected stream and connection event sequences and enforce peer stream ownership callbacks.

Supporting tests: `ParameterValidation/WithValidateConnectionEventArgs.ValidateConnectionEvents/*`, `WithValidateStreamEventArgs.ValidateStreamEvents/*`, `EventTest.cpp`, `OwnershipValidation.*`.

## Feature 009: Receive Buffer Modes

Receive buffering must correctly reassemble data, handle gaps/overlaps, track pending reads, support app-owned buffers, and reset cleanly.

Supporting tests: `RecvBufferTest/WithMode.*`, `MultiRecvTest.*`, `AppOwnedBuffersTest.*`, `RecvBufferResetReadTest.*`, `src/core/unittest/RecvBufferTest.cpp`.

## Feature 010: Datagram Negotiation and Delivery

Datagrams must follow negotiated receive support and must send, receive, or cancel according to endpoint configuration.

Supporting tests: `Misc/WithDatagramNegotiationArgs.DatagramNegotiation/*`, `Basic/WithFamilyArgs.DatagramSend/*`, `DatagramDrop/*`, `src/test/lib/DatagramTest.cpp`.

## Feature 011: QUIC Frame and VarInt Encoding

Frame and varint encoders/decoders must round trip valid inputs and reject malformed inputs.

Supporting tests: `FrameTest.*`, `FrameTest/*.*`, `VarIntTest.*`, `PacketNumberTest.WellKnownDecompress`, `Drill.VarIntEncoder`, `Drill/WithDrillInitialPacketCidArgs.*`, `Drill/WithDrillInitialPacketTokenArgs.*`.

## Feature 012: Packet Number Ranges and ACK Tracking

Range and ACK tracking must correctly merge, search, limit, and detect reordering/loss conditions.

Supporting tests: `RangeTest.*`, `FrameTest.TestQuicAckTrackerDidHitReorderingThreshold`, ACK examples in `src/core/unittest/RangeTest.cpp` and `FrameTest.cpp`.

## Feature 013: Congestion Control and Pacing

CUBIC and BBR must respond correctly to ACK, loss, ECN, recovery, pacing, app-limited, and statistics scenarios.

Supporting tests: `CubicTest.*`, `CubeRootCases/CubeRootTest.*`, `BbrTest_DeepTest.*`, `src/core/unittest/CubicTest.cpp`, `src/core/unittest/BbrTest.cpp`.

## Feature 014: Platform Datapath and Address Utilities

The platform layer must support UDP/TCP datapath behavior, address parsing, event queues, worker behavior, DSCP/ECN, and socket sharing/multibind.

Supporting tests: `DataPathTest.*`, `DataPathTest/DataPathTest.*`, `PlatformTest.*`, `ToeplitzTest.*`, `src/platform/unittest/DataPathTest.cpp`, `PlatformTest.cpp`.

## Feature 015: TLS and Cryptographic Primitives

TLS and crypto must satisfy known vector tests, handshake tests, certificate validation, cipher suite negotiation, resumption, fragmentation, and key update behavior.

Supporting tests: `CryptTest.*`, `CryptTest/CryptTest.*`, `TlsTest.*`, `TlsTest/TlsTest.*`, `Handshake.ForcedKeyUpdate/*`, `KeyUpdate/*`, `src/platform/unittest/CryptTest.cpp`, `TlsTest.cpp`.

## Feature 016: Diagnostics, Statistics, and Storage

The system must expose performance counters, statistics, network statistics events, storage-backed settings, library metadata, and compatible statistics sizes.

Supporting tests: `ParameterValidation.ValidateGetPerfCounters`, `ValidateGlobalParam`, `ValidateCommonParam`, `ValidateNetStatConnEvent/*`, `SettingsTest.*`, `QuicSettingsLoad_*`, `SettingsSizes*`, `GlobalSettingsSizes*`.
