### Challenges of using X.509 digital certificates for Securing LoRaWAN Communication

  * **Constrained IoT devices:** LoRa-based IoT devices are highly constrained: they have little memory, limited processing capacity, and limited power. Class 0 and 1 EDs are very constrained with RAM size much less than 10 KB and flash memory much less than 100 KB. 
  
  * **Constrained IoT network:** The LoRaWAN specification  defines a maximum data payload for each worldwide region. This full payload size varies by DataRate (DR) because of the maximum on-air transmission time allowed for each regional specification. While 52 bytes is the maximum payload in Europe, in the U.S. and other regions that operate in the 900MHz ISM band, the lowest DR is restricted to 11 bytes. These limits are chosen to meet the requirements of the regulatory agencies of the region.
  
  * **Non Availability of dedicated CA infrastructure for IoTs:** As mentioned earlier, the browser client (such as Chrome or Firefox) has a certificate trust store containing hundreds of Root CA certificates. The browser authenticates any server that delivers an X.509 certificate digitally signed by anyone of the Root CA in its certificate store. Such certificate store infrastructure is unavailable in the LoRaWAN backend network elements or any IoT backend infrastructures.
  
  * **Cost:** Even if we assume the infrastructure exists, the digital certificates come at a cost, which is not viable for most IoT services. 
  * 
  * **Label length:** CAs such as ”Let’s encrypt” provides X.509 digital certificates for free. However, it was not possible to benefit since they do not offer certificates for domain names with more than ten labels (JoinEUI has more than 16 labels). 

The primary issue with the X.509 digital certificates is its size. Thus not compatible with resource constrained IoT networks. It is impossible to send a 2048 byte X.509 digital certificate over a LoRaWAN Communication which has an Maximum Transfer Unit (MTU) of 52 bytes. Due to the ED and the network constraints, The CA model for issuing the X.509 digital certificates is not operationally feasible for LoRaWAN. 
