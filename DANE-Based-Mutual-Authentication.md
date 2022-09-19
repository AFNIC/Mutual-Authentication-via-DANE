This section focuses on solving the private CA issue. The objective is to have a similar set up like the PKIX in the web communication in LoRaWAN without the necessity of having the root CA being stored in the trusted store of each LoRaWAN.

## Using DNS based PKI for solving the private CA issue

First step in [Figure 4](Figures/Web-Communication-CA.png) is DNS resolution. A brief overview of DNS is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/2.DNS.md). The DNS resolution process by default doesn’t have  security features like in the case of web communication with TLS. The possibility to provide origin authentication of DNS data, data integrity and authenticated denial of existence for the DNS resolution is provided by DNS Security Protocols (DNSSEC) 
