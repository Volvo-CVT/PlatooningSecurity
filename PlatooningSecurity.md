# PlatooningSecurity

## Platooning Security Motives
Platooning is a type of C-ITS application where the message transmission frequency might get up to 50Hz. Depending on the implementation the message size can be around 100 bytes. 
Considering 5 vehicles in the Platoon broadcasting, each vehicle receives 200 messages a second to do security operations on (e.g. verification of the signature). This means consuming 20-30 percent of the capacity of a decent HSM designed for C-ITS platform security operation provides. The HSM will not just receive the platooning messages but also other messages such as "Cooperative Awareness Messages (CAM)" and "Decentralized Environment Notification Messages (DENM)" from vehicles outside of the Platoon. MAP and SPAT messages can also be received proximate to an intersection. Due to the introduced delay of performing the signing and verification of messages but also the addition of other message types already in the system, we suggest that the platoon shall have a separate security solution, which will be outlined subsequently.

## Platooning Network Architecture
Even though platooning vehicles uses the C-ITS platform, the architecture of vehicles in the platoon are in-fact different from non-platooning C-ITS enabled vehicles. Non-platoon vehicles are running in a distributed system where all nodes talk to each other (peer-to-peer, P2P) while platooning vehicles use the same architecture but have an appointed coordinator (the lead vehicle), who will grant access to the platoon based on a predetermined set of criteria. In a platoon configuration, it is forseen that non-coordinator nodes do not have to communicate platoon messages (except routing messages). Therefore the architecture become a hybrid architecture of client-server and p2p. Thereofore, platooning communication security can be implemented dramatically more efficient than in the P2P architecture.


