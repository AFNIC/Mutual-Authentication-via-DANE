## LoRaWAN Key Sharing Challenge

Bootstrapping trust when an LoRa ED device connects to the network and starts to operate is a security concern. The ED is usually equipped with an identifier and a **Pre-Shared Key (PSK)** to contact to the Backend elements on the Internet associated with the ED for onboarding. PSK needs to be shared between stakeholders (as shown in [*Figure 2*](/Figures/Key-Sharing-Challenge.png)) in the supply chain—from the Original Equipment Manufacturer (OEM) to the ED owner, the network service provider, the application server provider, etc. PSK is often shared insecurely, such as printing the keys on the back of devices, sending via mail or printing on the invoice. There have been reports of massive breaches of PSK provisioning systems, which are vulnerable to passive pervasive monitoring. There are secure ways in which PSK could be shared, but they rely on proprietary cloud services or secure key elements, which could increase the cost of LoRaWAN services.


<p align="center">
  <img width="500" height="250" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/Key-Sharing-Challenge.png">
  <br>
  <em> Fig.2 - Process of Key sharing between the ED manufacturer and other Stakeholders </figcaption> </em>
</p>
