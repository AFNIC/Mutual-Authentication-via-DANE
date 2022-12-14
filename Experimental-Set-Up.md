To Test our hypothesis of using Self-Signed Certificates for the IP space, we set up different [LoRaWANs](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/QuickStart.md).  

### Private CA issue

A CA provisioning infrastructure using self-signed certificates was set up. As a self-signed certificate is a digital certificate that's not signed by a publicly trusted CA, the self-signed certificates issued by a Root CA are not Publicly trusted. Hence, they cannot not be used outside the respective root CA infrastructure. Difference between the Root, Intermediate and leaf self-signed certificate are explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/Certificates-Tutorial.md#root-certificate)


### Experimental Set up

We set up an experimental set up (as shown in [Figure 3](Figures/CA_Provisioning_Architecture.png)) where Afnic emulated the **Root CA** role and generated intermediate certificates for two LoRaWAN - TSP (Telecom Sud Paris) and Afnic Labs.
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

We are using the open source stack, [Chirpstack](https://chirpstack.io/) for the experimental LoRaWAN setup. As per the LoRaWAN Backend specifications, the DNS infrastructure is used to resolve the IP address of the different backend elements. Details of the OTAA using DNS is explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/OTAA-Using-DNS.md). How the certificates are provisioned for the different servers in the backend elements are explained [here](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/Certificates-Tutorial.md#certificate-setup). 

### Mutual Authentication - Test case

To narrow down, let's focus on mutual authentication between Afnic's NS and TSP JS ([Step 4 in *Figure 4*](/Figures/OTAA-Test-Case.png))

<p align="center">
  <img width="750" height="450" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/OTAA-Test-Case.png">
  <br>
  <em> Fig.4 - OTAA between Afnic and TSP using DNS and self-signed certificates </figcaption> </em>
</p>


### Need for Combined certficiates

Focussing on the mutual authentication between Afnic's NS and TSP JS, we tested the validation using self-signed certificates. Since the intermediate certificates (which is used to generate the leaf self-signed certificates) for both the LoRaWANs were different, TLS handshake were emitting error messages as follows:

```sh
Sep 12 14:24:57 lorawan-test-ns chirpstack-network-server[2638245]: time="2022-09-12T14:24:57.761331841Z" level=info msg="Creating new LoRaWan client" client_name= tls_cert=/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-combined.pem tls_key=/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-key.pem
Sep 12 14:24:57 lorawan-test-ns chirpstack-network-server[2638245]: time="2022-09-12T14:24:57.795006087Z" level=info msg="creating application-server client" server="141.95.165.32:8001"
Sep 12 14:24:57 lorawan-test-ns chirpstack-network-server[2638245]: time="2022-09-12T14:24:57.835000174Z" level=info msg="finished client unary call" ctx_id=d6ff741c-b81d-4753-9f27-772220f4af99 grpc.code=OK grpc.ctx_id=12a0e23b-d62d-4a0d-af7b-1693f8fe2152 grpc.duration=12.509311ms grpc.method=HandleError grpc.service=as.ApplicationServerService span.kind=client system=grpc
Sep 12 14:24:57 lorawan-test-ns chirpstack-network-server[2638245]: time="2022-09-12T14:24:57.835878656Z" level=error msg="uplink: processing uplink frame error" ctx_id=d6ff741c-b81d-4753-9f27-772220f4af99 error="join-request to join-server error: http post error: Post \"https://js.dance-paper.iot.rd.nic.fr:8003\": x509: certificate is valid for localhost, not js.dance-paper.iot.rd.nic.fr"
```

We identified that combining the intermediate and the server leaf certificate (a combined trust chain - Fig. 5) during a TLS handshake could bypass the need for having a certificate store with all intermediate certificates. The validating server needs to store only the root CA certificate. The certificate validation process is done by sending the combined trust chain to the server’s IP address. On receiving the combined trust chain, the server first verifies the leaf certificate in the combined trust chain. When the leaf certificate is unknown, it checks the following certificate in the chain, i.e., the intermediate certificate. Since the root CA signs the intermediate certificate, the combined certificate chain becomes trusted. Thus, the backend network elements (NS, AS and the JS) could be mutually authenticated even if they are in different networks since they have a common root CA at the top of the chain of trust.

The advantage is that since the infrastructure uses self-signed certificates, there is no cost involved. The disadvantage is that the root CA should be copied to different LoRaWANs, if the self-signed certificate generated by it has to be authenticated. 

<p align="center">
  <img width="450" height="250" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/CA_Validation_Architecture.png">
  <br>
  <em> Fig.5 - Certificate Validation Process using Combined trust chain </figcaption> </em>
</p>

## Pointer Section

 * If you want to go back to the [Readme Page]
 * [Next section]


 [Readme Page]: https://github.com/AFNIC/Mutual-Authentication-via-DANE
 [Next section]: https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DANE-Based-Mutual-Authentication.md
 

