To Test our hypothesis of using Self-Signed Certificates for the IP space, we set up different [LoRaWANs](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/QuickStart.md)).  

### Private CA issue

A CA provisioning infrastructure ([*Figure 3*](/Figures/CA_Provisioning_Architecture.png)) was set up. As a self-signed certificate is a digital certificate that's not signed by a publicly trusted CA, the self-signed certificates issued by a Root CA are not Publicly trusted. Hence, they cannot not be used outside the respective root CA infrastructure.


### Experimental Set up

We set up an experimental set up where Afnic emulated the Root CA role and generated intermediate certificates for two LoRaWAN - TSP (Telecom Sud Paris) and Afnic Labs. Complete details on setting up the infrastructure is provided in the [Quick Start guide](https://github.com/AFNIC/IoTRoam-Tutorial/blob/master/QuickStart.md)

<p align="center">
  <img width="550" height="200" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/CA_Provisioning_Architecture.png">
  <br>
  <em> Fig.3 - Self-Signed Certificate provisioning infrastructure </figcaption> </em>
</p>
