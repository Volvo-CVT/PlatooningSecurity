# Platooning Security

- **Authors**: 
  - [Amin Asadi](https://github.com/aminasadi0)
  - [Eddie Garmon](https://github.com/EddieGarmon) 
- **First Draft**: 2016-2-15 
- **Last Updated**: 2016-10-03 

## Abstract



## Status of this Memo

Internet-Drafts are working documents.

Internet-Drafts are draft documents valid for a maximum of one year and may be updated, replaced, or obsoleted by other documents at any time.  It is inappropriate to use Internet-Drafts as reference material or to cite them other than as "work in progress."

This Internet-Draft will expire on Oct 03, 2017.

## Copyright Notice

Copyright (c) 2016 the persons identified as the document authors.  All rights reserved.

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

## Platooning Security Motivations
Platooning is a type of C-ITS application where the message transmission frequency might get up to 50Hz. Depending on the implementation the message size can be around 100 bytes. 
Considering 5 vehicles in the Platoon broadcasting, each vehicle receives 200 messages a second to do security operations on (e.g. verification of the signature). This means consuming 20-30 percent of the capacity of a decent HSM designed for C-ITS platform security operation provides. The HSM will not just receive the platooning messages but also other messages such as "Cooperative Awareness Messages (CAM)" and "Decentralized Environment Notification Messages (DENM)" from vehicles outside of the Platoon. MAP and SPAT messages can also be received proximate to an intersection. Due to the introduced delay of performing the signing and verification of messages but also the addition of other message types already in the system, we suggest that the platoon shall have a separate security solution, which will be outlined subsequently.

## Platooning Network Architecture
Even though platooning vehicles uses the C-ITS platform, the architecture of vehicles in the platoon are in-fact different from non-platooning C-ITS enabled vehicles. Non-platoon vehicles are running in a distributed system where all nodes talk to each other (peer-to-peer, P2P) while platooning vehicles use the same architecture but have an appointed coordinator (the lead vehicle), who will grant access to the platoon based on a predetermined set of criteria. In a platoon configuration, it is forseen that non-coordinator nodes do not have to communicate platoon messages (except routing messages). Therefore the architecture become a hybrid architecture of client-server and p2p. Thereofore, platooning communication security can be implemented dramatically more efficient than in the P2P architecture.

![image](https://cloud.githubusercontent.com/assets/3536075/19036275/1f4c1fe4-896f-11e6-8164-888983182dc3.png)
![image](https://cloud.githubusercontent.com/assets/3536075/19036296/4021fd88-896f-11e6-811b-889ab05a228f.png)

###	Hybrid Security Solution for Platooning
The problem with symmetric cryptography is mainly the key exchange operation over a large scale network while preventing the keys to be leaked to outsiders. On the other hand, the challenge with asymmetric cryptography are mainly the security overhead added to the payload and computationally slow operations. In platooning, the type of network architecture allows us to build a hybrid security solution of the two to tackle the challenges. Here is how it works:

The lead vehicle as the coordinator can be responsible for generating and exchanging a symmetric key among other vehicles in the platoon. Different scenarios might happen in the platoon with regards to communication security:
-	Joining the first vehicle to the lead vehicle to form a platoon
-	Joining more vehicles to the platoon
-	Leaving vehicles from platoon
-	Leaving the last vehicle from platoon

### Key exchange phase
In the following examples the Lead vehicle is called (A) and the platooning vehicles (B), (C) and so forth. 

1.	**First vehicle joining the lead vehicle**
 
 * Upon joining the platoon, (B) must first create a JoinRequest message. The JoinRequest message includes keyRequest which itself includes the cipher-suite that the vehicle support and timestamp (to avoid replay attack in future), a signature of the keyRequest and the current Pseudo-Certificate in use by the vehicle. B broadcasts the JoinRequest message.
 
 * (A) receives (B)’s JoinRequest. It primarily verifies (B)’s Pseudo-Certificate (PC). This involves in verifying the expiration time and CA who has signed the (B)’s PC (A must find the corresponding CA certificate in its cache, decrypt the signature on the PC and compare the content of decrypted text with PC). If the PC was correct, the public key inside it can now be used to verify the signature (B) made on the request. This again involves in decrypting the signature (B) made and comparing the content with the KeyRequest. Meanwhile the integrity of the message has been implicitly checked. This process authenticates both the message and the sender. The timestamp in the keyRequest must be checked to avoid responding to old messages. 

 *	(A) caches the  pseudo certificate to be used in the platoon security handshake. It can also skip CA certificate verification in next verification process within the platoon.

 *	(A) now generates a symmetric key or (session key) for encryption and one for HMAC (will be discussed later). AES (Advanced Encryption Standard) is one of the most well-known symmetric key encryption algorithms. AES-256 bit for key size is suitable. (A) must save these two keys in the secure memory of the HSM.

 *  Now it is time to transfer the key to (B). The key must be encrypted so it will not end up in wrong hands. (A) encrypts the session key with (B)’s public key included in (B)’s verified pseudo certificate (the encrypted message can be called envelope). To add authenticity and integrity to the broadcast message, (A) signs the encrypted session key, appends the signature and its own Pseudo-Certificate. Then message is ready to be broadcasted back to (B). No one except for (B) can decrypt the envelope since only (B) has the corresponding private key. The broadcasted message is called ResponseMessage.

 *  (B) receives the message and verifies (A)’s certificate for mutual authenticity. (B) then decrypt the signature with the (A)’s public key and compare the result with the envelope. If the comparison result was true, (B) checks the message integrity in other words it makes sure that the envelope (the encrypted message) was not altered during the transmission. 

 *  Surrounding vehicles other than (B) can do the last step. But they will end up in an encrypted message which means nothing to them therefore it will be dropped. An adversary cannot decrypt the envelope since it is encrypted with (B)’s public key so only (B) who possesses the corresponding private key can decrypt the envelope. (B) decrypts the envelope and extracts the symmetric or shared key.

 *  (B) must save the secret key into the secured memory of HSM.

 *  (B) can cache (A)’s pseudo certificate.

 *  (B) must send an Acknowledgment to (A) reporting that it received the symmetric key successfully. The Ack includes (B)’s pseudo-Id (taken from (B)’s certificate) and a timestamp encrypted with (A)’s public key. A signature is made by (B)’s private key and appended to the message. (B)’s pseudo-Id must be appended again so (A) would know which cached public key must be used to verify the signature.

 ![image](https://cloud.githubusercontent.com/assets/3536075/19036662/6cd6db26-8971-11e6-869d-69878fc1f6ea.png)

2.	**More vehicles join the platoon**

 * The lead truck has already generated and exchanged the keys with the first vehicle therefore other vehicles handshake for key exchange would be slightly different. The lead truck would only have to verify the joining vehicles’ certificate and exchange the already generated key with them. So all the vehicles would be able to group communicate with each other.

3.	**Leaving the platoon**

 * On leaving one or more vehicles from the platoon, for security resasons the lead truck must revoke the current keys in use and generate new ones. Since the vehicles’ certificates are cached in the lead truck, the lead truck will do the key exchange with each remaining vehicle with the new key. After receiving Ack from all following vehicles, the lead truck starts using the new keys in the next message. It would be needed to have a field in message header (e.g. incremented number) which shows which key is used in security operation.
 
### Secured communication phase
+ **Message Authentication Code**
 
 From now on (A) and (B) possess a shared symmetric key which can be used to encrypt the message securely and efficiently. A confidential message (encrypted) might seem to be enough in the first glance. But the message must also be secured against intentional or unintentional changes within the communication. The receiver must also be able to authenticate the message. 
 Message Authentication Code (MAC) is extremely useful in communication secured with symmetric cryptography. A MAC algorithm usually gets a symmetric key and the clear text, generates a MAC of the message which then can be appended to the clear text and transmitted to other end. It is more or less similar to the “signature” functionality in public-key cryptography.

+	**HMAC or Keyed-Hash MAC**
 
 Hash MAC (HMAC) is a specific type of MAC which uses a key and a specific type of cryptographic hash function to calculate the MAC. HMAC must not be confused with cryptographic hash functions such as SHA-1. HMAC utilizes cryptographic hash functions to generate the MAC of message for message-integrity and message-authenticity. The key used in HMAC must also be known to both parties. In the key exchange protocol (A) generated 2 keys one for encryption purpose and for HMAC.

+	**Authenticated Encryption**

 Having the HMAC (for message authenticity and integrity) and AES algorithm for confidentiality, the communication security can be performed in Authenticated Encryption mode.  There are different approaches how to and in what order to use these tools (e.g. MTE or Mac then Encrypt used in SSL/TLS). We are going to use **ETM mode** (Encrypt then MAC) implying that the message is first encrypted then the MAC is performed on the encrypted message or ciphertext. The result will be appended to the encrypted message. Thus, the algorithm would be **AES256-CBC-HMAC-SHA256**. In practice, an Initialization Vector (IV) must be prepended to the message and the ciphertext for purposes outside the scope of this document. If key1 and key2 assumed as the key for AES and HMAC respectively, sequence diagram below shows the secured communication phase in platoon.

 ![image](https://cloud.githubusercontent.com/assets/3536075/19038362/7592c806-897b-11e6-9c82-0c33114179ae.png)

