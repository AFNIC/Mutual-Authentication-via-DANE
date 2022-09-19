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

In a DNSSEC-signed zone, every single Resource Record set (RR set )  has one (sometimes more ) corresponding RR signature (RRSIG) record. The RRSIG is the digital signature of the RRset. The RRSIG is produced by hashing the RRset and encrypting the hash with the concerned private key.
The private key of the ZSK is used to produce the RRSIG for all the contents of the DNS zone, except for the two DNSKEY records. It is important to note that the signed DNSSEC zone contains the RRSIG and the original unsigned data. The below example shows the contents of the “A” type RR for a DNSSEC, signed zone “example.fr”.

        ; Unsigned `A' type RR
        example.fr.  1   A   192.168.0.100

        ; RRSIG for `A' type RR
        example.fr. 1 RRSIG A 5 5 1  
        (
          202206300640 202206030640 3960 example.fr.   
          s8dMOWQjoTKEo1bsK+EYUY+32Bd84300FcJfl 
          00FcJflqthv1u60DVDVobllhqt0AaiD/dlnn7     
          32Bd84300FcJflqthv1u60DVDVobllhqt0Aai
        )

### Building the Chain of Trust

DNSSEC adds a level of security wherein if the original IPv4 address (e.g., “192.16.0.100”) in the zone “example.fr” is modified to “198.51.100.1” by an impersonator during DNS resolution, a DNSSEC validating client application will find it out. The reason is that the corresponding RRSIG will not match with the hash of the original RR on un-signing.

But there is a possibility that the impersonator successfully sent the false IP address and the corresponding RRSIG, signed by their own generated private/public key pair before the valid response. And, if the public key is trusted, a DNSSEC validating client application would not be able to find out that the data has been tampered with. 

In the PKIX model, to counter such a compromise, the certificate generated by the web server was authenticated by a trusted third party, i.e. the CA. In the DNSSEC case, the validation of the public key is based on a cryptographic **chain of trust**.

<p align="center">
  <img width="400" height="300" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/DNSSEC_Trust_Chain.png">
  <br>
  <em> Fig.7 - An example of how the chain of trust is established </em>
</p>

[Figure 4](Figures/DNSSEC_Trust_Chain.png)  illustrates the process of building the chain of trust:

  1. From the KSK DNSKEY of the “zone example.fr”, a Delegation Signer (DS) RR [9] is created. The DS RR is the hash of the KSK DNSKEY. This DS RR is published in the parent zone of “example.fr”, which is “.fr”.
  2. This record has to be signed by the parent zone ZSK private key, i.e. by the ZSK private key in “.fr” to get the RRSIG of the "example.fr" DS.
  3. Similarly the DS record for the “.fr” zone is published in its parent zone, which is the DNS root “.” 
  4. As in Step '2', the RRSIG of ".fr" DS is obtained by by signing the ".fr" DS with the "." KSK  as shown in step 4.

This is how the chain of trust is established.

### Verification of the DNS resolution data using the 'Chain of trust'

Previous section explained the process of building the chain of trust. This section describes how a DNSSEC-aware resolver validates a DNSSEC-enabled DNS query/response. For DNSSEC resolution, the ”public key” of the DNS zone must be configured with a DNS resolver that validates with DNSSEC. Usually, most resolvers have the public key (KSK DNSKEY) of the DNS root ”. ”as ”Trust Anchor”.

The resolver can be on the local computer or at a remote location (such as at the ISP). Let’s assume that the DNSSEC validating resolver is at the ISP and doesn’t have the IP address of ’example.fr’ in its cache. It begins a recursive query to identify the DNS server that holds the authoritative information for ’example.fr’.

The DNS resolution process starts top-down from the ”.” to ”.fr” to ”example.fr” authoritative servers. Each of these authoritative servers will add additional DNSSEC data to the DNS responses. This additional data, in effect, is the digital signature of the DNS data contained in the response, i.e. the RRSIG.

  1. The ’A’ record of ’example.fr’ is validated through its RRSIG (in the same reply) using the DNSKEY of ’example.fr’
  2. The DNSKEY of ’example.fr’ is validated through its RRSIG using the DS of ’example.fr’
  3. The DNSKEY of ’.fr’ is validated through its RRSIG using the DS of ’.fr’
  4. The DNSKEY of the root zone ’.’ is validated through its RRSIG using the trust-anchor
