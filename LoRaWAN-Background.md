## LoRaWAN brief background

LoRaWAN is an asymmetric protocol with a star topology  as shown in the [*Figure 1*](/Figures/LoRaWAN_Key_Distribution-1.png). Data transmitted by the LoRaWAN **End-Device (ED)** is received by a **Radio Gateway (RG)**, which relays it to a **Network Server (NS)**. The NS decides on further processing the incoming data based on the ED’s unique identifier (DevEUI). The NS has multiple responsibilities like forwarding the uplink from the ED to the **Application Server (AS)**, queuing the downlink from the AS to the ED, forwarding the ED onboarding request to the appropriate AA (Authentication & Authorization) servers, named as **Join Server (JS)** in LoRaWAN terminology. While the ED is connected to the RG via LoRa modulated *RF messages*, the connection between the RG, the NS and the AS is done through *IP traffic* and can be backhauled via Wi-Fi, hardwired Ethernet or Cellular connection.

<p align="center">
  <img width="600" height="225" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/LoRaWAN_Key_Distribution-1.png?raw=true">
  <br>
  <em> Fig.1 - LoRaWAN Topology & Key Distribution</figcaption> </em>
</p> 

The JS acting as the AA server controls the terms on how the ED gets activated (i.e., onboarded) to a selected LoRaWAN.  There are two types of ED activation: **Over the Air Activation (OTAA)** and **Activation by Personalization (ABP)**. With ABP, the ED is directly connected to a LoRaWAN by hardcoding the cryptographic keys and other parameters required for secured communication. With OTAA, the parameters necessary to create a secured session between the ED and the servers in the Internet are created dynamically. This secured session is similar to the TLS handshake used in the HTTPS connection. OTAA is preferred over ABP since it is dynamic, decouples the ED and the backend infrastructure, and doesn’t need session keys to be hardcoded. 

The ED performs a Join procedure (i.e. the onboarding process) with the JS during OTAA by sending the Join Request (JR). The JR payload contains the ED’s unique identifier (i.e. the DevEUI), the cryptographic AES-128 root keys: NwkKey, AppKey and JoinEUI (unique identifier pointing to the JS).  
