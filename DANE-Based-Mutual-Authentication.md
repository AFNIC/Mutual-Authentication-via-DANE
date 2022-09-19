This section focuses on solving the private CA issue. The objective is to have a similar set up like the PKIX in the web communication in LoRaWAN without the necessity of having the root CA being stored in the trusted store of each LoRaWAN.

## Using DNS based PKI for solving the private CA issue

First step in [Figure 4](Figures/Web-Communication-CA.png) is DNS resolution. A brief overview of DNS is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/2.DNS.md). The DNS resolution process by default doesn’t have  security features like in the case of web communication with TLS. The possibility to provide origin authentication of DNS data, data integrity and authenticated denial of existence for the DNS resolution is provided by DNS Security Protocols (DNSSEC). Introdcution to DNSSEC terminology is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/3.DNSSEC.md) and how DNSSEC enables a secure trusted chain is provided [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DNSSEC-Primer.md). Need for DNS Authentication of Named Entities (DANE) in a Web based PKIX is explained [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/DANE-Primer.md), and a brief tutorial about DANE is provided [here](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/blob/main/5.DANE.md).

For DANE to augment the security in the existing PKIX model, the domain’s DNS zone should be DNSSEC signed, and the DNS resolution validated using DNSSEC. DANE protocol enables the domain owner to pinpoint the CA certificate the browser should use to validate the certificate obtained from the web server, thus reducing the attack probability. 

Hence, a DNS infrastructure with its security extensions such as DANE with DNSSEC could be used as a PKI complimenting the PKIX. 

### How DANE enables mutual authentication for LoRaWAN using self-signed certificates?

As explained [earlier](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Experimental-Set-Up.md#private-ca-issue), the issue with the certificate [provisioning infrastructure](Figures/CA_Provisioning_Architecture.png) using a single root CA  creates a single private PKI. This approach fails from an operational feasibility perspective as number of stakeholders are not willing to be restricted to a single CA.

The IETF [DANCE](https://datatracker.ietf.org/wg/dance/about/) (DNS Authentication of Named Clients Everywhere) WG (Working Group) seeks to make PKI- based IoT device identity universally discoverable, more broadly recognized, and less expensive to maintain by using DNS as the constraining namespace and lookup mechanism. DANCE builds on patterns established by the original DANE RFCs to enable client and sending entity certificate, public key, and trust anchor discovery. DANCE allows entities to possess a first-class identity, which, thanks to DNS, may be trusted by any application also relying on the DNS. A first- class identity is an application-independent identity.

The IETF DANCE WG is discussing two Internet drafts ([TLS extension for DANE Client](https://www.ietf.org/archive/id/draft-huque-tls-dane-clientid-06.html) and [TLS Client Authentication via DANE TLSA records](https://datatracker.ietf.org/doc/html/draft-huque-dane-client-cert-08)) based on DANE, which possibly solves the single root CA issue. For this to work, a TLS Client should have a signed DNS TLSA record (as in the [Figure](https://gitlab.rd.nic.fr/tutoriels/The-DNS-to-Reinforce-the-PKIX/-/raw/main/images/tlsa.jpg)) published in the DNS zone corresponding to its DNS name and X.509 certificate or public key.
[TLS extension for DANE Client](https://www.ietf.org/archive/id/draft-huque-tls-dane-clientid-06.html) specifies a TLS extension to convey a DANE Client Identity to a TLS server. The extension contain the client identity in the form of the DNS domain name that is expected to have a DANE TLSA record published for it as shown in the example below:

```sh
                  _3000._tcp.local.dance-tests.iot.rd.nic.fr. 1 IN   TLSA    3 1 1                              
                                                                             6F0B92D23EB7C75F4B8A25F3B6ECC7F935F0F320E20A8C43B63D83FD95C720FB
```

During the TLS handshake, the server requests a client certificate (via the ”Client Certificate Request” message). The server then extracts the DANE client identity (e.g. ```dance-tests.iot.rd.nic.fr ```) , constructs the DNS query name for the corresponding TLSA record (e.g; ``` 6F0B92D23EB7C75F4B8A25F3B6ECC7F935F0F320E20A8C43B63D83FD95C720FB ```) and authenticates the client’s certificate or public key. During mutual authentication, both the client and the server could be authenticated as shown in the below [Figure 7](/Figures/DANE_Client_Authentication.png).

<p align="center">
  <img width="350" height="400" src="https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/DANE_Client_Authentication.png">
  <br>
  <em> Fig.7 - Mutual authentication facilitated by DANE during TLS handshake </em>
</p>

DANE-based mutual authentication enables using a self- signed certificate with different Root CA’s. Thus, each institution can choose its Root CA to sign the certificates and validate dynamically based on DANE client identity.

### Implementation

In the [previous](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Experimental-Set-Up.md) experimental setup, there was only one Root CA (which was emulated by Afnic). Currently both [TSP and Afnic](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Figures/CA_Provisioning_Architecture.png) could have their own respective root CA's. The only requirement is that they need to provision in their respective DNS zone, the hash of the X.509 digital certificate as TLSA resource records. 

The two IETF drafts ([TLS extension for DANE Client](https://www.ietf.org/archive/id/draft-huque-tls-dane-clientid-06.html) and [TLS Client Authentication via DANE TLSA records](https://datatracker.ietf.org/doc/html/draft-huque-dane-client-cert-08)) are [implemented](https://gitlab.rd.nic.fr/dance/deployment). 

Certain configuration changes to the existing Chirpstack `.toml`files in the NS and JS are modified as follows:

```sh
            * network server

            [join_server]
            resolv_conf = "/etc/chirpstack-network-server/resolv.conf"

            [join_server.default]
            server="https://js.dance-paper.iot.rd.nic.fr:8003"
            tls_cert="/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-combined.pem"
            tls_key="/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-key.pem"
            dane_client_name = "_ns-client.ns.dance-paper.iot.rd.nic.fr"
```

```sh
            * join server

            [join_server]
            bind="0.0.0.0:8003"
            tls_cert="/home/lorawan/certificates/certs/application-server/join-api/server/application-server-join-api-server-combined.pem"
            tls_key="/home/lorawan/certificates/certs/application-server/join-api/server/application-server-join-api-server-key.pem"
            enable_dane = true
            resolv_conf = "/etc/chirpstack-application-server/resolv.conf"
```



Now during mutual authentication, during TLSA handshake, the X.509 digital certificate is validated as shown in the ([Figure 7](/Figures/DANE_Client_Authentication.png)). The DANE client ID based successful mutual authentication log files in the NS are below:

```sh

Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.353526026Z" level=info msg="gateway/mqtt: uplink frame received" gateway_id=00800000a0000824 uplink_id=5ba0044f-c7df-4a19-bad7-b4fd1322ca62
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.571523759Z" level=info msg="uplink: frame(s) collected" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf mtype=JoinRequest uplink_ids="[5ba0044f-c7df-4a19-bad7-b4fd1322ca62]"
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.579782666Z" level=info msg="Creating new LoRaWan client" client_name=_ns-client.ns.dance-paper.iot.rd.nic.fr tls_cert=/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-combined.pem tls_key=/home/lorawan/certificates/certs/application-server/join-api/client/application-server-join-api-client-key.pem
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.580374905Z" level=info msg="Loading DANCE http client" client_name=_ns-client.ns.dance-paper.iot.rd.nic.fr
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: 2022/09/12 14:15:12 Verifying TLSA for _8003._tcp.js.dance-paper.iot.rd.nic.fr
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.629769703Z" level=info msg="lorawan/backend: finished backend api call" message_type=JoinReq protocol_version=1.0 receiver_id=2b7e151628aed2a5 result_code=Success sender_id=000001 transaction_id=1401864848
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.630959978Z" level=info msg="sent uplink meta-data to network-controller" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf dev_eui=0000000000000006
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.632156931Z" level=info msg="device-queue flushed" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf dev_eui=0000000000000006
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.633455426Z" level=info msg="device-session saved" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf dev_addr=03c6c2de dev_eui=0000000000000006
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.657947724Z" level=info msg="device-activation created" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf dev_eui=0000000000000006 id=9717
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.661758119Z" level=info msg="device updated" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf dev_eui=0000000000000006
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.662631382Z" level=info msg="gateway/mqtt: publishing gateway command" command=down downlink_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf gateway_id=00800000a0000824 qos=0 topic=gateway/00800000a0000824/command/down
Sep 12 14:15:12 lorawan-test-ns chirpstack-network-server[2638158]: time="2022-09-12T14:15:12.665339213Z" level=info msg="storage: downlink-frame saved" ctx_id=9bcc5920-8f6e-465a-a9d1-b050a68fdfbf token=10740
```
The DANE client ID based successful mutual authentication log files in the JS are below:

```sh
Sep 12 14:11:11 lorawan-test-as chirpstack-application-server[2540444]: 2022/09/12 14:11:11 Verifying TLSA for _ns-client.ns.dance-paper.iot.rd.nic.fr
Sep 12 14:11:12 lorawan-test-as chirpstack-application-server[2540444]: time="2022-09-12T14:11:12.150631673Z" level=info msg="backend/joinserver: request received" message_type=JoinReq receiver_id=2b7e151628aed2a5 sender_id=000001 transaction_id=3049576573
Sep 12 14:11:12 lorawan-test-as chirpstack-application-server[2540444]: time="2022-09-12T14:11:12.156809536Z" level=info msg="device-keys updated" ctx_id="<nil>" dev_eui=0000000000000006
Sep 12 14:11:12 lorawan-test-as chirpstack-application-server[2540444]: time="2022-09-12T14:11:12.156952223Z" level=info msg="backend/joinserver: sending response" dev_eui=0000000000000006 message_type=JoinAns receiver_id=000001 result_code=Success sender_id=2b7e151628aed2a5 transaction_id=3049576573
```

Thus, the issue of mutual authentication is solved wherein we could have federated Root CA's and use self-signed certificates solving all the challenges of using PKIX in the IP space as mentioned [here](https://github.com/AFNIC/Mutual-Authentication-via-DANE/blob/main/Challenges-in-using-PKIX-in-IoT.md) 

## Pointer Section

 * If you want to go back to the [Readme Page]
 

 [Readme Page]: https://github.com/AFNIC/Mutual-Authentication-via-DANE
 
