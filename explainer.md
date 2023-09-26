# Encrypted Client Hello

## The problem

Browser clients reveal the domain name of the target sites during TLS handshakes via the Server Name Indication (SNI) field. This has long been considered unideal, but there historically had been no good way to encrypt the SNI without adding round trips to the protocol. Given that domain names were also leaked to DNS, this was considered low priority to fix.

## The solution

Nowadays, we have various methods for encrypted DNS. Encrypted Client Hello (ECH) provides an opt-in mechanism for sites to post keys in an HTTPS RR in DNS that are then used to encrypt the initial ClientHello message during a TLS handshake. This prevents a network eavesdropper from being able to see the destination domain, especially if the HTTPS RR is resolved over encrypted DNS.

ECH is a TLS protocol extension in the final stages of [standardization](https://datatracker.ietf.org/doc/draft-ietf-tls-esni/) by the IETF. For more information, see [Section 1](https://www.ietf.org/archive/id/draft-ietf-tls-esni-16.html#section-1) and [Section 3](https://www.ietf.org/archive/id/draft-ietf-tls-esni-16.html#section-3).

## Security considerations

ECH prevents eavesdropping. An active attacker capable of blocking or tampering with HTTPS RR requests could prevent ECH from being used, or decrypt ECH records. This has no bearing on the final security of the TLS connection, and can be mitigated by using encrypted DNS with a trusted mechanism for discovery and authentication.

Browsers decide whether or not to require encrypted DNS when using ECH, and how (if at all) to surface that information to users.

## Chrome considerations

Chrome implements ECH via BoringSSL. Chrome will use ECH whenever it is able to resolve an HTTPS RR record with ECH keys, regardless of whether or not encrypted DNS was in use.

