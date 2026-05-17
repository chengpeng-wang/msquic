# RFC 9000: QUIC Transport

Source: https://www.rfc-editor.org/rfc/rfc9000.html

RFC 9000 is the primary transport standard implemented by MsQuic. It defines connection establishment, streams, transport parameters, flow control, packet processing, frames, connection IDs, migration, and connection termination.

Key relevance:

- MsQuic's connection object corresponds to a QUIC endpoint state machine.
- Stream APIs expose QUIC reliable streams, flow control, stream ID limits, send and receive shutdown, and stream errors.
- Frame tests cover RFC 9000 frame encoding/decoding behavior such as STREAM, ACK, RESET_STREAM, STOP_SENDING, MAX_DATA, MAX_STREAM_DATA, NEW_CONNECTION_ID, RETIRE_CONNECTION_ID, PATH_CHALLENGE, PATH_RESPONSE, and CONNECTION_CLOSE.
- Connection ID and migration support map to RFC 9000 routing and path validation concepts.
- Transport parameters are exchanged during the handshake and affect flow control, datagram support, version negotiation extension behavior, idle timeouts, and stream limits.
