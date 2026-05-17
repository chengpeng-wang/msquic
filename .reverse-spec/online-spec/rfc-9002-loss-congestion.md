# RFC 9002: QUIC Loss Detection and Congestion Control

Source: https://www.rfc-editor.org/rfc/rfc9002

RFC 9002 defines QUIC recovery behavior. MsQuic's ACK tracking, RTT estimation, loss detection, PTO behavior, bytes-in-flight accounting, pacing, and congestion control hooks derive from this standard.

Key relevance:

- Packet number spaces and ACK processing affect loss detection and retransmission behavior.
- RTT and acknowledgement delay feed timers and recovery logic.
- Congestion control must limit sending based on bytes in flight, congestion window, persistent congestion, and recovery state.
- MsQuic includes CUBIC and BBR behavior around the standard recovery interface, including tests for ACK/loss/ECN, pacing, app-limited samples, and network statistics.
