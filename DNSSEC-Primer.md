In PKIX, data origin authentication is provided by the digital certificate. TLS provides confidentiality and data integrity. Data is encrypted only during transmission, and the data stored in the server end is not encrypted. In the case of DNSSEC, the data at the server end, i.e. in the DNS zone, is encrypted using the public and private keys. Hence data in the DNS zone is encrypted before being transmitted in the network. In the PKIX model at the client end, an HTTP user agent such as a web browser is used for validation. In DNSSEC, a validating resolver is used to verify the integrity of the transmitted data.

### DNS Zone

Since DNSSEC is an extension of DNS which acts upon a DNS zone, it is important to have some basic idea of a DNS zone. As shown in [Figure 6](/Figures/DNS-Hierarchy1.png), the Top Level Domain (TLD) “.fr” has two sub domains “example.fr” and “afnic.fr”. But, “.fr”, “afnic.fr” and “example.fr” are three separate zones. Each of these zones contains all the data for their specific domains. For example, the “.fr” zone contains data specific to the “.fr” do- main managed by one entity. Similarly, “example.fr” contains data specific to the “example.fr” domain, which will/could be managed by a different entity. A fictitious example of a DNS zone file for a fictitious domain “example.fr” is as follows:

<p align="center">
  <img width="350" height="225" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/DNS-Hierarchy1.png">
  <br>
  <em> Fig.6 - DNS Tree example </em>
</p>

