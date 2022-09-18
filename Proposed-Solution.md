As shown in the [*Figure 1*](/Figures/LoRaWAN_Key_Distribution-1.png), ED is connected to the RG via LoRa modulated *RF messages* (i.e., the RF Space) and the connection between the RG, the NS and the AS is done through *IP traffic* (i.e., the IP Space). 

If we could have end-to-end communication between the IoT ED and its back elements by either compressing or fragmenting the X.509 digital certificate, then we could use the PKIX for end-to-end LoRaWAN communication. 

Standardisation Organisations (SDOs) and research are working on either compressing or fragmenting the X.509 digital certificate, so that it could be used in constrained networks and EDs such as in the LoRaWAN use-case.  Currently **we do not have a solution** to extend the PKI to the RF space (As shown in the [*Figure 1*](/Figures/LoRaWAN_Key_Distribution-1.png)). It is a work in process as part of our [PIVOT](https://pivot-project.info/) project.

### Proposed solution for the IP space

For secure ED onboarding, the interface between the back-end network elements (the NS, JS and the AS) in the **IP space** (As shown in the [*Figure 1*](/Figures/LoRaWAN_Key_Distribution-1.png)) should be mutually authenticated (i.e., both the client and the server authenticate each other), as per the LoRaWAN backend specifications. Nevertheless, how mutual authentication should be done is left to the implementer’s choice and is not normative.
