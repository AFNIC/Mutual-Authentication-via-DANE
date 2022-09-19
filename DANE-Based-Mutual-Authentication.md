This section focuses on solving the private CA issue. The objective is to have a similar set up like the PKIX in the web communication in LoRaWAN without the necessity of having the root CA being stored in the trusted store of each LoRaWAN.

## Using DNS based PKI for solving the private CA issue

First step in [Figure 4](Figures/Web-Communication-CA.png) is DNS resolution. A brief overview of DNS is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/2.DNS.md). The DNS resolution process by default doesn’t have  security features like in the case of web communication with TLS. The possibility to provide origin authentication of DNS data, data integrity and authenticated denial of existence for the DNS resolution is provided by DNS Security Protocols (DNSSEC). Introdcution to DNSSEC terminology is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/3.DNSSEC.md) and how DNSSEC enables a secure trusted chain is provided [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DNSSEC-Primer.md). Need for DNS Authentication of Named Entities (DANE) in a Web based PKIX is explained [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DANE-Primer.md), and a brief tutorial about DANE is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/5.DANE.md).

For DANE to augment the security in the existing PKIX model, the domain’s DNS zone should be DNSSEC signed, and the DNS resolution validated using DNSSEC. DANE protocol enables the domain owner to pinpoint the CA certificate the browser should use to validate the certificate obtained from the web server, thus reducing the attack probability. 

Hence, a DNS infrastructure with its security extensions such as DANE with DNSSEC could be used as a PKI complimenting the PKIX. 

### How DANE enables mutual authentication for LoRaWAN using self-signed certificates?

As explained [earlier](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Experimental-Set-Up.md#private-ca-issue), the issue with the certificate [provisioning infrastructure](Figures/CA_Provisioning_Architecture.png) using a single root CA  creates a single private PKI. This approach fails from an operational feasibility perspective as number of stakeholders are not willing to be restricted to a single CA.

The IETF [DANCE](https://datatracker.ietf.org/wg/dance/about/) (DNS Authentication of Named Clients Everywhere) WG (Working Group) seeks to make PKI- based IoT device identity universally discoverable, more broadly recognized, and less expensive to maintain by using DNS as the constraining namespace and lookup mechanism. DANCE builds on patterns established by the original DANE RFCs to enable client and sending entity certificate, public key, and trust anchor discovery. DANCE allows entities to possess a first-class identity, which, thanks to DNS, may be trusted by any application also relying on the DNS. A first- class identity is an application-independent identity.
The IETF DANCE WG is discussing two Internet drafts (([TLS extension for DANE Client](https://www.ietf.org/archive/id/draft-huque-tls-dane-clientid-06.html) and [TLS Client Authentication via DANE TLSA records](https://datatracker.ietf.org/doc/html/draft-huque-dane-client-cert-08) based on DANE, which possibly solves the single root CA issue. For this to work, a TLS Client should have a signed DNS TLSA record (as in the [Figure](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/raw/main/images/tlsa.jpg)) published in the DNS zone corresponding to its DNS name and X.509 certificate or public key.
[19] specifies a TLS extension to convey a DANE Client Identity to a TLS server. The extension contain the client identity in the form of the DNS domain name that is expected to have a DANE TLSA record published for it as shown in the example below:

```sh
        light_sensor._device.example.com. IN TLSA ( 312
          0f8b48ff5fd94117f21b6550aaee89c8
          d8adbc3f433c8e587a85a14e54667b25
          f4dcd8c4ae6162121ea9166984831b57
          b408534451fd1b9702f8de0532ecd03c )
```

During the TLS handshake, the server requests a client certificate (via the ”Client Certificate Request” message). The server then extracts the DANE client identity, constructs the DNS query name for the corresponding TLSA record and authenticates the client’s certificate or public key. During mutual authentication, both the client and the server could be authenticated as shown in the below [Figure 7](/Figures/DANE_Client_Authentication.png).

<p align="center">
  <img width="350" height="400" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/DANE_Client_Authentication.png">
  <br>
  <em> Fig.7 - Mutual authentication facilitated by DANE during TLS handshake </em>
</p>

DANE-based mutual authentication enables using a self- signed certificate with different Root CA’s. Thus, each institution can choose its Root CA to sign the certificates and validate dynamically based on DANE client identity.

