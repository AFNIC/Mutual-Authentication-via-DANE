To Test our hypothesis of using Self-Signed Certificates for the IP space, we set up different [LoRaWANs](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/QuickStart.md).  

### Private CA issue

A CA provisioning infrastructure using self-signed certificates was set up. As a self-signed certificate is a digital certificate that's not signed by a publicly trusted CA, the self-signed certificates issued by a Root CA are not Publicly trusted. Hence, they cannot not be used outside the respective root CA infrastructure. Difference between the Root, Intermediate and leaf self-signed certificate are explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/Certificates-Tutorial.md#root-certificate)


### Experimental Set up

We set up an experimental set up (as shown in [Figure 3](Figures/CA_Provisioning_Architecture.png)) where Afnic emulated the Root CA role and generated intermediate certificates for two LoRaWAN - TSP (Telecom Sud Paris) and Afnic Labs.
The intermediate certificates were generated for the NS, JS and the AS in each LoRaWAN. 


Complete details on setting up the infrastructure is provided in the [Quick Start guide](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/QuickStart.md)

<p align="center">
  <img width="550" height="200" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/CA_Provisioning_Architecture.png">
  <br>
  <em> Fig.3 - Self-Signed Certificate provisioning infrastructure </figcaption> </em>
</p>

We tested the experimental set for LoRaWAN Over the Air Authentication (OTAA). 

The ED performs a Join procedure with the JS during OTAA by sending the Join Request (JR). The JR payload contains the ED’s unique identifier (DevEUI), the cryptographic AES-128 root keys: NwkKey, AppKey and JoinEUI (unique identifier pointing to the JS).  

The JS associated to the ED also has prior information such as the ED’s DevEUI, the cryptographic keys: NwkKey and AppKey required for generating session keys to secure the communication between the ED and the NS and AS. These are the pre-shared information between the ED and the AA server in the Internet. As mentioned earlier, for this experimental set up we are focussing on mutual authentication in the IP space. Hence the ED association to the backend elements will be done with the AES PSKs.

We are using the open source stack, [Chirpstack](https://chirpstack.io/) for the experimental LoRaWAN setup. As per the LoRaWAN Backend specifications, the DNS infrastructure is used to resolve the IP address of the different backend elements. Details of the OTAA using DNS is explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/OTAA-Using-DNS.md). How the certificates are provisioned for the different servers in the backend elements are explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/Certificates-Tutorial.md) 


### Need for Combined certficiates

The Backend elements 

During testing, we identified that combining the intermediate and the server leaf certificate (a combined trust chain - Fig. 9) during a TLS handshake could bypass the need for having a certificate store with all intermediate certificates. The validating server needs to store only the root CA certificate. The certificate validation process is done by sending the combined trust chain to the server’s IP address. On receiving the combined trust chain, the server first verifies the leaf certificate in the combined trust chain. When the leaf cer- tificate is unknown, it checks the following certificate in the chain, the intermediate certificate. Since the root CA signs
