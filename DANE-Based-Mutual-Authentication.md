This section focuses on solving the private CA issue. The objective is to have a similar set up like the PKIX in the web communication in LoRaWAN without the necessity of having the root CA being stored in the trusted store of each LoRaWAN.

## Using DNS based PKI for solving the private CA issue

First step in [Figure 4](Figures/Web-Communication-CA.png) is DNS resolution. A brief overview of DNS is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/2.DNS.md). The DNS resolution process by default doesn’t have  security features like in the case of web communication with TLS. The possibility to provide origin authentication of DNS data, data integrity and authenticated denial of existence for the DNS resolution is provided by DNS Security Protocols (DNSSEC). Introdcution to DNSSEC terminology is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/3.DNSSEC.md) and how DNSSEC enables a secure trusted chain is provided [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DNSSEC-Primer.md). Need for DNS Authentication of Named Entities (DANE) in a Web based PKIX is explained [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DANE-Primer.md), and a brief tutorial about DANE is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/5.DANE.md).

For DANE to augment the security in the existing PKIX model, the domain’s DNS zone should be DNSSEC signed, and the DNS resolution validated using DNSSEC. DANE protocol enables the domain owner to pinpoint the CA certificate the browser should use to validate the certificate obtained from the web server, thus reducing the attack probability. 

Hence, a DNS infrastructure with its security extensions such as DANE with DNSSEC could be used as a PKI complimenting the PKIX. 

### How DANE enables mutual authentication for LoRaWAN using self-signed certificates?

As explained earlier The issue with the certificate provisioning infrastructure using a single root CA (Fig. 8) creates a single private PKI. This approach fails from an operational feasibility perspective as number of stakeholders are not willing to be restricted to a single CA.

