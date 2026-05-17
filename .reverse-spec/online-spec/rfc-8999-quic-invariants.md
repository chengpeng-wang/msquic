# RFC 8999: Version-Independent Properties of QUIC

Source: https://www.rfc-editor.org/rfc/rfc8999

RFC 8999 defines QUIC properties that remain stable across versions. MsQuic needs these invariants for parsing initial packets enough to perform version handling, routing by connection ID, and generating version negotiation responses.

Key relevance:

- QUIC packets run over UDP and include enough invariant structure for endpoint and middlebox handling without exposing version-specific internals.
- Long-header packets carry a version field and connection IDs.
- Version negotiation packets let a server tell a client that the selected version is unsupported.
- Implementations must not assume version-specific semantics beyond the invariant fields before selecting a supported version.
