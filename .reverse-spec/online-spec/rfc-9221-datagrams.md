# RFC 9221: QUIC Datagrams

Source: https://www.rfc-editor.org/rfc/rfc9221

RFC 9221 defines unreliable DATAGRAM frames for QUIC. MsQuic exposes this as datagram receive enablement, peer send capability, datagram send calls, datagram received events, and cancellation when peer support is unavailable.

Key relevance:

- Datagram support must be advertised through a transport parameter.
- Datagram frames are protected by QUIC encryption but are not retransmitted on packet loss.
- Applications can use datagrams for unreliable application data alongside reliable streams.
- MsQuic tests cover negotiation enabled/disabled behavior and IPv4/IPv6 datagram send paths.
