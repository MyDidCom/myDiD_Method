# myDid Method Specification
## Introduction
myDid is a decentralized identifier (DID) method that enables creation of DIDs and associated DID Documents on EVM blockchains.
Decentralized identifiers are a new type of identifier that allow individuals and organizations to create globally unique identifiers that they fully own and control, independent of any centralized registry or authority. DIDs resolve to DID Documents that contain metadata related to the identifier such as cryptographic keys and service endpoints.

myDid aims to leverage the security and decentralization properties of EVM-based blockchains while enabling interoperability with other DID methods via the W3C DID standards.
## myDid Identifiers
myDid identifiers have the following structure:


```
did:mydid:base58EncodedPublicKey
```

The 58-character identifier is generated from the public key used to create the DID on the Ethereum blockchain. Specifically, the identifier is the base58 encoding of the 20 byte Ethereum address derived from the public key.

Some examples of valid myDid identifiers:

```
did:mydid:zr389Hiajsikam23jdsFK38FJ28dsj
did:mydid:z8FHgusdu2hdaF82maF28Fj2F8uF2m
did:mydid:z9idjF38je83mfmFj28FiiwjSksjdn
```
The base58 encoding makes myDid identifiers human-readable while still carrying the full public key information required to resolve the identifier.


## DID Documents
A myDid DID Document contains information related to the DID such as public keys, service endpoints, Capability, etc.

myDid uses two types of DID Documents:
### On-chain DID Documents
Stored directly on the blockchain via the myDid smart contract. 
Contains essential data such as the DID, controllers, and primary public keys. 

Example:
```
{
   "context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/v1"
   ],
   "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
   "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
   "authentication": [
      {
         "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Auth_1",
         "type": "EcdsaSecp256k1VerificationKey2019",
         "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
         "publicKeyMultibase": "zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTdS"
      }
   ],
   "assertionMethod": [
      {
         "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Assr_1",
         "type": "EcdsaSecp256k1RecoveryMethod2020",
         "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
         "blockchainAccountId": "eip155:01:0x7174143ac405967a32FA7656BF6efDF4Ba52574D"
      }
   ],
   "service": [
      {
         "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Profile",
         "type": "Public Profile",
         "serviceEnpoint": "https://ipfs/Qm..."
      }
   ]
}
```
### Off-chain DID Documents
Les DID Documents off-chain sont générés dynamiquement à partir de la clé publique contenue dans l'identifiant DID.
Ils sont retournés par le resolveur myDid si aucune donnée enregistrée on-chain n'est trouvée pour ce DID.

Exemple :
```
{
    "@context": [
                    "https://www.w3.org/ns/did/v1",
                    "https://w3id.org/security/v1"
    ],
    "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
    "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
    "authentication": [
        {
            "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#AUTH_1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
            "publicKeyMultibase": "z26TcxBC6RXfhYJw64tN9cofSKzk6NoV7xWaKMwEdG7LFi"
        }
    ],
    "assertionMethod": [
        {
            "id": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#ASSR_1",
            "type": "EcdsaSecp256k1RecoveryMethod2020",
            "controller": "did:mydid:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
            "blockchainAccountId": "eip155:01:0x7174143ac405967a32FA7656BF6efDF4Ba52574D"
        }
    ]
}
```
# DID Document Operations
## Creating a DID
* Generate key pair
* Publish public key to myDid smart contract
* DID identifier created based on Ethereum address of public key
* Initialize DID Document on-chain

## Updating a DID Document
* Send signed transactions to myDid smart contract
* Emit events for off-chain updates
* Updates appended to DID Document history
## Revoking / Deleting a DID Document
* Revoke DID Document by setting the status to "revoked"
* Delete a DID by sending delete transaction to smart contract
# DID Resolution
To resolve a myDid DID identifier:

* Extract Ethereum address from identifier
* Query myDid smart contract to fetch on-chain DID Document
* Retrieve off-chain DID Document using URI reference
* Return merged view of on-chain and off-chain DID contents
# Implementation
myDid is implemented as:

* Solidity smart contract deployed on EVM Blockchain
* NodJs reference resolver
* React webapp for managing DIDs

# Security Considerations
Types of Attacks and Mitigations
## Eavesdropping
Given that all data are public and stored on the blockchain, the risk of unauthorized data interception (eavesdropping) is mitigated as the data is not sensitive by design.

## Replay Attacks
Replay attacks are mitigated through the use of nonces in the transactions, ensuring uniqueness and validity.

## Message Insertion, Deletion, Modification
Only the DID controller can initiate these actions and they require proof of signature, making unauthorized insertions, deletions, or modifications virtually impossible.

## Denial of Service (DoS)
The decentralized nature of blockchain significantly limits the impact of DoS attacks. Rate controls and request limitations can further mitigate this risk.

## Residual Risks
The remain residual risks are the ones associated with any EVM (Ethereum Virtual Machine) based solution: if the associated blockchain is compromised, the solution can become vulnerable.

## Integrity Protection and Update Authentication
All operations require a signature from the DID controller, providing both authentication and integrity protection.

## Authentication Mechanisms
The security of the authentication method relies on digital signatures generated from the controller's public keys.

## Policy for Unique Assignment
DIDs are uniquely generated based on the Ethereum address derived from the public key. Attempts to create an already-existing DID will fail at the smart contract level, ensuring unique assignment.

# Privacy Considerations
## Surveillance
Since myDiD operates on public blockchains, transactions and operations are visible to anyone monitoring the network. However, the only public information is the public profile of issuers. Personal data are only exchanged off-chain, the risk of harmful surveillance is limited.

## Stored Data Compromise
All data on the blockchain is intended to be public, but a compromise of the underlying blockchain could pose integrity risks. Since sensitive information is not stored, the risk is confined to data accuracy and not confidentiality.

## Unsolicited Traffic
The nature of decentralized application mitigates the risk of unsollicited traffic. On the user side, through the application th user is only exposed with requests following its own actions. 

## Misattribution
Since DIDs are generated based on public keys, the risk of misattribution is low. However, if a private key is compromised, it could lead to incorrect associations.

## Disclosure
Disclosures are transparent and occur at the discretion of the DID controller, who chooses what information to include in DID documents.

## Exclusion
Since the blockchain is a public ledger, there is no option for exclusion or "right to be forgotten." Once a transaction is added to the blockchain, it becomes a part of the immutable record. However, you can mark an identity as "to be ignored".

# Conformance
myDid is compliant with the Decentralized Identifier specification from W3C.
