[Previoulsy](LoRaWAN-Key-Sharing-Challenge.md), we discussed about the Key sharing challenge in LoRaWAN. Here in this section, we try to look at how Key management is done in the Internet and can it be reused for LoRaWAN.

## Key Management in the Internet

In web browsing communication, **first** (as shown in [*Figure 3*](/Figures/Web-Without-Security.png)), the browser obtains the IP address of a domain name using the DNS infrastructure and **secondly** it connects to the domain’s web server using the IP address via Hyper Text Transfer Protocol (HTTP) connection.

<p align="center">
  <img width="450" height="300" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/Web-Without-Security.png">
  <br>
  <em> Fig.3 - Unsecured Internet communication </em>
</p>

Normally the data sent between a browser and a web server is in plain text – leaving the data vulnerable to eavesdropping. This data might include credit card numbers, login password, personal health details etc., which has to be transmitted securely. Hence, the data sent should be encrypted.

For the *first* operation ((in [*Figure 3*](/Figures/Web-Without-Security.png)), securing the communication during DNS resolution could be provided by **DNS Security (DNSSEC)**. For the *second* operation, the **Transport Layer Security (TLS)** protocol comes to the rescue, allowing the client and the server to authenticate each other and negotiate an encryption algorithm and cryptographic keys before the data is exchanged. TLS ensures that data cannot be tampered with during transit since the data is encrypted. 

## Public-Key Infrastructure X.509 (PKIX) - What it is and why we need it for securing Internet communication?

Currently, the most commonly used protocol for web security is TLS. This technology is still commonly referred to as SSL,  a predecessor to TLS. Hereafter only TLS will be used throughout this document.

Encrypting and decrypting the data in the TLS protocol is done by a matching pair of cryptographic keys: **public** and **private** key. The Data encrypted by a public key can be decrypted only by the corresponding private key and vice versa, enabling secure communication with unknown users.

A website (e.g., a bank) publishes its public key for anyone to download. An account holder (for example Alice) in the bank, encrypts a message using the public key and sends it to the bank. Only the bank can decrypt the message using its private key. Thus, Alice is sure that her message is accessed only by the bank and not by anyone else.

On the other hand, there is a possibility that an impersonator publishes their public key posing as Alice’s bank. Alice will encrypt the message using the public key and send it to the impersonator, thinking she is communicating with her bank. The impersonator could do a man in the middle and copy the message. As the impersonator is the owner of the public and the private key, it will enable them to decrypt and read the message sent by Alice. Thus, there arises a possibility that anyone can create a public key for accessing any domain name.

Hence, binding between the identity (e.g., the domain name) and the public key is necessary. The **X.509 standard** proposed by the ITU and ISO provides a mechanism to bind a particular public key to a specific identity. 
