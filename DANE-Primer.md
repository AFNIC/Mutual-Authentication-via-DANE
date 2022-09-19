## DANE (DNS Authentication of Named Entities) - Augmenting the security in PKIX

### The problem of many: 
At a glance, if we look at the size of the list of CAs accepted by popular browsers such as Chrome, Firefox, Internet explorer, etc., it varies 
but is in the range of hundreds. For example, a browser such as Firefox trusts 1,482 CA Certificates (as per EFF SSL observatory in 2010) provided
by 651 organizations. Complementing the issue is that in the CA ecosystem, there is a practice of a CA providing authorization to other 
organizations or its branches to create certificates on its behalf. They are called subordinate CAs. A browser will trust the digital 
certificate produced by the subordinate CA also.

Even if one CA among the list of CAs, or its subordinates are compromised, it can generate a certificate for any domain name, which could then be 
authenticated by a browser, thereby compromising a secure web communication. For instance, two different CAs (where one is a compromised CA) can 
issue two separate certificates for the same domain, which the browser will trust. The lacunae here is that the domain owner has no way of telling
the browser which CA certificate to be used to authenticate to connect to the server of the particular domain.

## Limiting the attack surface, what are the options?:
Different techniques were proposed to reduce the attack probability in the PKIX model such as Trust on First Use (ToFU), Certificate Transparency 
(CT), Certificate Authentication and Authorization (CAA) and DANE. Of the different technologies proposed to limit the attack surface, ToFU is the
easiest to implement because it needs only a browser to install the ToFU compatible browser add- on. Perspectives and CT are a system of Notary 
services which does not completely coexist with the current PKIX model and need additional services acting as notary services. CAA is like a hack 
which does not need any modifications and, in the short term, looks like a better option for limiting the attack surface. But looking at security
from an end-to-end perspective and providing more options to the users (such as self-signed certificates), DANE ranks high.
