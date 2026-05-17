# RFC 9369: QUIC Version 2

Source: https://www.rfc-editor.org/rfc/rfc9369.html

RFC 9369 defines QUIC version 2 as a wire-compatible evolution of QUIC version 1 with selected changes. MsQuic lists RFC 9369 among protocol features and includes version negotiation support needed to deploy QUIC v2 alongside v1.

Key relevance:

- QUIC v2 uses the same broad transport architecture as QUIC v1 but changes selected wire constants and derivations.
- Applications using MsQuic can rely on version negotiation mechanisms rather than hard-coding one QUIC version.
- Tests around generated compatible version lists and version negotiation extension behavior support v2 deployment.
