In the LoRaWAN-Key-Sharing-Challenge.md section, we discussed about the Key sharing challenge in LoRaWAN. Here in this section, we try to look at how Key management is done in the Internet and can it be reused for LoRaWAN.

## Key Management in the Internet

In web browsing communication, **first** (as shown in [*Figure 3*](/Figures/Web-Without-Security.png)), the browser obtains the IP address of a domain name using the DNS infrastructure and **secondly** it connects to the domain’s web server using the IP address via Hyper Text Transfer Protocol (HTTP) connection.

<p align="center">
  <img width="450" height="300" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/Web-Without-Security.png">
  <br>
  <em> Fig.3 - Unsecured Internet communication </em>
</p>

For the first operation, securing the communication during DNS resolution could be provided by **DNS Security (DNSSEC)**. For the second operation, the Transport Layer Security (TLS) protocol comes to the rescue, allowing the client and the server to authenticate each other and negotiate an encryption algorithm and cryptographic keys before the data is exchanged. TLS ensures that data cannot be tampered with during transit since the data is encrypted. 

