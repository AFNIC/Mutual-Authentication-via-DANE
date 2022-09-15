[Previously](LoRaWAN-Key-Sharing-Challenge.md), we discussed about the Key sharing challenge in LoRaWAN. In this section, we describe how Key management is done in the Internet? The objective is to check whether the same Key management methods could be deployed for LoRaWAN.

## Key Management in the Internet

In web browsing communication, **first** (as shown in [*Figure 3*](/Figures/Web-Without-Security.png)), the browser obtains the IP address of a domain name using the DNS infrastructure and **secondly** it connects to the domain’s web server using the IP address via Hyper Text Transfer Protocol (HTTP) connection.

<p align="center">
  <img width="350" height="250" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/Web-Without-Security.png">
  <br>
  <em> Fig.3 - Unsecured Internet communication </em>
</p>

Normally the data sent between a browser and a web server is in plain text – leaving the data vulnerable to eavesdropping. This data might include credit card numbers, login password, personal health details etc., which has to be transmitted securely. Hence, the data sent should be encrypted.

For the *first* operation (in [*Figure 3*](/Figures/Web-Without-Security.png)), securing the communication during DNS resolution could be provided by **DNS Security (DNSSEC)**. For the *second* operation, the **Transport Layer Security (TLS)** protocol comes to the rescue, allowing the client and the server to authenticate each other and negotiate an encryption algorithm and cryptographic keys before the data is exchanged. TLS ensures that data cannot be tampered with during transit since the data is encrypted. 

## Public-Key Infrastructure X.509 (PKIX) - What it is and why we need it for securing Internet communication?

Currently, the most commonly used protocol for web security is TLS. This technology is still commonly referred to as SSL,  a predecessor to TLS. Hereafter only TLS will be used throughout this document.

Encrypting and decrypting the data in the TLS protocol is done by a matching pair of cryptographic keys: **public** and **private** key. The Data encrypted by a public key can be decrypted only by the corresponding private key and vice versa, enabling secure communication with unknown users.

A website (e.g., a bank) publishes its public key for anyone to download. An account holder (for example Alice) in the bank, encrypts a message using the public key and sends it to the bank. Only the bank can decrypt the message using its private key. Thus, Alice is sure that her message is accessed only by the bank and not by anyone else.

On the other hand, there is a possibility that an impersonator publishes their public key posing as Alice’s bank. Alice will encrypt the message using the public key and send it to the impersonator, thinking she is communicating with her bank. The impersonator could do a man in the middle and copy the message. As the impersonator is the owner of the public and the private key, it will enable them to decrypt and read the message sent by Alice. Thus, there arises a possibility that anyone can create a public key for accessing any domain name.

Hence, binding between the identity (e.g., the domain name) and the public key is necessary. This is done by a Public Key Infrastructutre (PKI). PKI is a collection of technologies, processes and policies that provides the means to verify the authenticity of «Public Keys». 

PKIs role is in Facilitating Trust over the Internet via Key management. The role includes:
  * Create (Duration, CSR, Validation..)
  * Store
  * Distribute (CRL)
  * Revoke (Pinning, Stapling)

### PKI X.509 (PKIX) – the PKI for web communication

In a web communication, authentication is about ensuring that users know whom they are talking to, and in most cases, that "whom," is represented by a « domain name ». To initiate a secure web communication, a user might enter the Uniform Resource Identifier (URI)  « https://www.afnic.fr » into a web browser, wherein the string before the « :// » is the protocol identifier and the string after the « :// » indicates the domain name of the server. Hypertext Transfer Protocol Secure (HTTPS) indicates that the communication between the user and the web server of the domain should be done securely using the Transport Layer Security (TLS) protocol.

In order to establish a TLS connection, the browser asks the Web server to send its public key. As we have seen earlier for encryption purpose, the web server for the domain has its private key stored safely and makes its public key accessible to all. The public key  send by the web server to the browser is in the form of PKI X.509 digital certificate. The **X.509 standard** proposed by the ITU and ISO provides a **mechanism to bind a particular public key to a specific identity**. 

For authentication purpose, the browser needs to verify that the PKIX digital certificate send to it, is actually  from the server authorized to represent the domain name.

### CAs role in the PKIX ecosystem

The domain holder can do this binding by creating a digital certificate based on X.509 standards. In this case, it is called a **Self-signed certificate**. If the self-signed certificate is obtained from a trusted source by the application using the certificate for authentication, then it is accepted. Otherwise there is no guarantee of the certificate’s authenticity.

This is where the need for a **trusted third party** arises. It is just like the passport wherein it can be issued only by a trusted third party delegated by a country’s government. The trusted authority attests that the person in the photo is identified by a particular name, surname and other credentials.

In the PKIX ecosystem, the role of the trusted authority is played by organizations called Certificate Authorities (CAs). A certificate issued by a given CA binds the given domain name with information such as the certificate assignee, the entity which has requested the certificate, its validity period etc. The CA is used to assert to the browser that the web server represents the domain asked by the client. A TLS connection is established between the browser and the web server on successful authentication of the certificate. With the TLS connection established, the traffic between the browser and the web server is encrypted and is protected against any third-party eavesdropping.

Similar to how a passport attested by one country’s trusted authority is accepted by other countries as a validated document for authenticating a person, browser vendors (such as Firefox, Chrome, Internet explorer, Safari etc.,) accept digital certificates created only by certain CAs. The browser vendors authorize an organization to be a CA only after understanding that they are trustworthy and they follow strict principles and procedures to provide certificates only for correct domain holders. Once the browser vendors authorize an organization to be a CA, the latter’s digital certificate is added to the list of trusted CAs (a trust store) in the browser library. Thus, once a client using a browser accesses a domain name which has a digital certificate generated by one of the CAs among its pre-installed list, the certificate is implicitly trusted, as shown in Fig. 2
4.

<p align="center">
  <img width="350" height="250" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/Web-Without-Security.png">
  <br>
  <em> Fig.3 - Unsecured Internet communication </em>
</p>









