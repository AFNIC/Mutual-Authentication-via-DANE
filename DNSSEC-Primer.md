In PKIX, data origin authentication is provided by the digital certificate. TLS provides confidentiality and data integrity. Data is encrypted only during transmission, and the data stored in the server end is not encrypted. In the case of DNSSEC, the data at the server end, i.e. in the DNS zone, is encrypted using the public and private keys. Hence data in the DNS zone is encrypted before being transmitted in the network. In the PKIX model at the client end, an HTTP user agent such as a web browser is used for validation. In DNSSEC, a validating resolver is used to verify the integrity of the transmitted data.

### DNS Zone

Since DNSSEC is an extension of DNS which acts upon a DNS zone, it is important to have some basic idea of a DNS zone. As shown in [Figure 6](/Figures/DNS-Hierarchy1.png), the Top Level Domain (TLD) “.fr” has two sub domains “example.fr” and “afnic.fr”. But, “.fr”, “afnic.fr” and “example.fr” are three separate zones. Each of these zones contains all the data for their specific domains. For example, the “.fr” zone contains data specific to the “.fr” do- main managed by one entity. Similarly, “example.fr” contains data specific to the “example.fr” domain, which will/could be managed by a different entity. 

<p align="center">
  <img width="250" height="150" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/DNS-Hierarchy1.png">
  <br>
  <em> Fig.6 - DNS Tree example </em>
</p>

A fictitious example of a DNS zone file for a fictitious domain “example.fr” is as follows:

```sh
          ; Zone file for www.example.fr
          $TTL 1h                ; Time To Live
          example.fr IN SOA ns.example.fr. 
                host.example.fr.
          (
              2022060304; Serial number
              3h          ; Refresh
              1h          ; Retry
              1h          ; expire
              1h          ; Negative cache 
          )
          example.fr.  IN  NS     dns1.examplehost.fr.
          example.fr.  IN  NS     dns2.examplehost.fr.
          example.fr.  IN  A      192.168.0.100
```
To enable DNSSEC, using the Public-Key cryptographic technique, the zone administrator (for a zone such as ”example.fr”) creates a public and private key pair. The private key is accessible only to the domain owner, and all can access the public key. 

DNSSEC zone administrators are recommended to create two pairs of public and private keys, wherein one is called the Zone Signing Key (ZSK), and the other pair is called the Key Signing Key (KSK). Both public keys are published in the domain’s DNS zone, and they are referred to as ”DNSKEY”. A sample of DNSKEY record in a signed DNS zone is as follows:

```sh
        ; ZSK public key
        example.fr.  IN   DNSKEY 256 3 5                          
         AwEAAda013Wp4CQaUBrExCIRZCYpThKqyr   
          pAaC7rAm2Jn+VlYnzIqmwELmn0EqIsdf03
          /e7cV8Bao94dX3xdcK+kZ6t5Of1hOLal5q
          yn/nsKZlH247VsEE62lHQNBnxPBHIpwUL7                             
                                     
        ; KSK Public key
        example.fr.  IN   DNSKEY 257 3 5           
          A9Vze/B+hmwDJ+83cZ1JWW2G9geiboeMy
          iuuoXB7FVavuIHJtiux+WjseJeQ4XYUGV
          DZkPyXiJWQ/rL7azGiZB2CKPMxHyr3L5P      			
          d1rjC50DQS45TFDvemAmvezCBs6sUtlD8
```
