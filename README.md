# Platooning Security

## Introduction
Cooperative Intelligent Transport System (C-ITS) intends to improve safety for humans and traffic efficiency on the roads. This requires the vehicles to communicate wirelessly within which information are naturally exposed to intruders. Cyber-attacks such as intentional modification, injection of bogus information and reading out critical data while transmitted, can cause extremely fatal consequences. It also opens possibilities for adversaries to breach privacy, e.g. by location tracking. Real-time communication requirement by many C-ITS applications, limited resources on the nodes, limited bandwidth in the network where the vehicles should be assumed mostly offline nodes, makes it challenging to develop a robust secure solution for V2X communication.

## Security in C-ITS
###	Scheme
C-ITS security is a PKI based solution where the underlying public key cryptography utilizes elliptic curves to mathematically bound public and private key, therefore, it is called Elliptic Curve Cryptography (ECC). Even though ECC might be slightly less efficient in terms of performance comparing to RSA, ECC uses keys of smaller size while providing the same level of security. Shorter keys generate less security overhead for a message (which affects signature size and appended certificate size) in transmission. Less security overhead will lead to less packet loss rate (due to smaller packets), which is suitable for scenarios with limited bandwidth and real-time requirements such as V2X communication.

C-ITS security utilizes two types of certificates, one for secure communication with CA for administrational purposes (so called Long Term Certificate) and the other one to securely communicate over V2X (so called Pseudonym Certificate). The long term certificate is installed in the vehicle during production and it could last for a long period. Pseudonym Certificate (PC) does not include the real identity of the user or the vehicle bounded to its public key. It rather uses pseudonyms so the privacy of the vehicle/owner can be preserved over the V2X public communication. A single pseudonym though can be tracked by an adversary to figure out the real identity of the vehicle or owner. However, to prevent this, the vehicle is provided with more than one thousands PCs every year so it can use one for each ride. PCs are short-lived meaning that the expiration date in them is set to a short period. 

Certification Revocation Lists (CRL) are lists of identities that a CA or system administrator provides to all entities more or less periodically. While verifying a certificate, a receiver shall verify that the certificate is not revoked (canceled) by the admin or a CA. CRLs grow fast so it is not a scalable scheme to be used in V2X security. Instead, the PCs expiration date set to a relatively small value so they revoke themselves after a short period of time. Next time, the vehicle asks the admin to refill the V2X unit with new PCs, a CA can ignore the request if the vehicle must be prevented to participate in the system for any reason (e.g. stolen vehicle, other vehicles reported misbehave etc). Therefore, the short-lived PCs naturally do not need to be revoked and the distribution of large CRLs is avoided.

###	PKI scheme risk analysis for C-ITS
####	Computation Efficiency
Even if one consider all the necessary steps to make PKI as efficient as possible (using hybrid solution, caching certificates, caching verified CA certificate, etc.), PKI operations are still computationally intensive for embedded systems. HSM (Hardware Security Modules) might include a processor solely designed for specific security operations. HSM vendors barely meet minimum requirements for number of verified received messages in one second (at least 800 messages/second for day-1 applications). Schemes such as verify-on-demand (verify the message received if it is needed) can relieve the computational load on the ITS stations but it compels a cross layer communication within the software implying increased complexity.

####	Security overhead
The security overhead of the message for the PKI solution tends to be large. For a scenario where the messages are set to transmit with high frequency, packet loss rate will increase, and might endanger safety critical systems.


##	Platooning
A road train made by vehicles wirelessly coupled together all under control of a lead vehicle.

![image](https://cloud.githubusercontent.com/assets/3536075/19033412/89a0e9d0-895e-11e6-8399-412cc54b5a36.png)

### Advantages:
 - Reduces Air Drag (and therefore Fuel Consumption)
 - Increases Safety and Comfort
 - Increases Road Capacity

### [Learn more about Platooning Security] (https://github.com/Volvo-CVT/PlatooningSecurity/blob/master/PlatooningSecurity.md)

