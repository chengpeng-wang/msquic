# RFC 9368: Compatible Version Negotiation for QUIC

Source: https://www.rfc-editor.org/rfc/rfc9368.html

RFC 9368 defines a mechanism for client and server to select a mutually supported QUIC version, including compatible negotiation when first-flight formats are compatible. MsQuic's version settings and version negotiation extension behavior align with this standard.

Key relevance:

- Clients send version information that can prevent downgrade attacks and enable compatible negotiation.
- Servers compare acceptable, offered, and fully deployed version lists when negotiating.
- Operators can roll out versions gradually using separate version lists.
- MsQuic exposes version settings on clients and globally for servers and tests incompatible, compatible, retry, and failure cases.
