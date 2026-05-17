# RFC 9287: Greasing the QUIC Bit

Source: https://www.rfc-editor.org/rfc/rfc9287.html

RFC 9287 defines greasing behavior for the QUIC bit to reduce middlebox ossification. MsQuic includes a setting to enable Grease QUIC Bit behavior and handshake tests that exercise this option.

Key relevance:

- Endpoints may vary the QUIC bit when greasing is negotiated/enabled.
- Implementations and deployments should avoid depending on the QUIC bit always having one fixed value.
- This feature supports protocol extensibility and deployability across middleboxes.
