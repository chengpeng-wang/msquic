# Active QUIC Draft Extensions Relevant to MsQuic

## QUIC-LB: Generating Routable QUIC Connection IDs

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-load-balancers/

The draft defines load-balancer-friendly connection IDs. MsQuic's load balancing modes can encode server ID information in connection IDs so routing infrastructure can direct packets without decrypting QUIC payloads.

## QUIC Acknowledgment Frequency

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-ack-frequency/

The draft defines a way for endpoints to request less or more frequent acknowledgments. MsQuic references ACK frequency as a supported draft feature and has transport/loss recovery infrastructure that must remain compatible with configurable ACK behavior.

## Reliable Stream Reset

Source: https://datatracker.ietf.org/doc/draft-ietf-quic-reliable-stream-reset/

The draft defines stream reset with guaranteed delivery up to a selected offset. MsQuic includes reliable reset preview support and frame encode/decode tests for reliable reset stream frames.
