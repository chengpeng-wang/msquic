# RFC 9001: Using TLS to Secure QUIC

Source: https://www.rfc-editor.org/rfc/rfc9001.html

RFC 9001 defines how QUIC integrates TLS 1.3. MsQuic must use TLS for authentication, key derivation, ALPN, certificate handling, transport parameter exchange, and resumption while letting QUIC perform packet protection.

Key relevance:

- QUIC uses TLS handshake messages carried in CRYPTO frames rather than TLS records.
- TLS produces secrets that QUIC uses for packet protection at Initial, Handshake, 0-RTT, and 1-RTT levels.
- MsQuic must expose TLS-derived metadata such as negotiated ALPN and handshake information to applications.
- Certificate validation may be platform-default, disabled for tests, custom, deferred, or client-authenticated depending on credentials and callbacks.
- Resumption and 0-RTT require retaining and validating QUIC transport parameter compatibility.
