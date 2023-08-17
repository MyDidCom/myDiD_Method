# myDiD Method Specification
## Introduction
myDiD is a decentralized identifier (DID) method that enables creation of DIDs and associated DID Documents on EVM blockchains.
Decentralized identifiers are a new type of identifier that allow individuals and organizations to create globally unique identifiers that they fully own and control, independent of any centralized registry or authority. DIDs resolve to DID Documents that contain metadata related to the identifier such as cryptographic keys and service endpoints.

myDiD aims to leverage the security and decentralization properties of EVM-based blockchains while enabling interoperability with other DID methods via the W3C DID standards.
## myDiD Identifiers
myDiD identifiers have the following structure:


```
did:myDiD:base58EncodedPublicKey
```

The 58-character identifier is generated from the public key used to create the DID on the Ethereum blockchain. Specifically, the identifier is the base58 encoding of the 20 byte Ethereum address derived from the public key.

Some examples of valid myDiD identifiers:

```
did:myDiD:zr389Hiajsikam23jdsFK38FJ28dsj
did:myDiD:z8FHgusdu2hdaF82maF28Fj2F8uF2m
did:myDiD:z9idjF38je83mfmFj28FiiwjSksjdn
```
The base58 encoding makes myDiD identifiers human-readable while still carrying the full public key information required to resolve the identifier.


## DID Documents
A myDiD DID Document contains information related to the DID such as public keys, service endpoints, Capability, etc.

myDiD uses two types of DID Documents:
### On-chain DID Documents
Stored directly on the blockchain via the myDiD smart contract. 
Contains essential data such as the DID, controllers, and primary public keys. 

Example:
```
{
   "context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/v1"
   ],
   "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
   "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
   "authentication": [
      {
         "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Auth_1",
         "type": "EcdsaSecp256k1VerificationKey2019",
         "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
         "publicKeyMultibase": "zC9ByQ8aJs8vrNXyDhPHHNNMSHPcaSgNpjjsBYpMMjsTdS"
      }
   ],
   "assertionMethod": [
      {
         "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Assr_1",
         "type": "EcdsaSecp256k1RecoveryMethod2020",
         "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
         "blockchainAccountId": "eip155:01:0x7174143ac405967a32FA7656BF6efDF4Ba52574D"
      }
   ],
   "service": [
      {
         "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#Profile",
         "type": "Public Profile",
         "serviceEnpoint": "https://ipfs/Qm..."
      }
   ]
}
```
### Off-chain DID Documents
Les DID Documents off-chain sont générés dynamiquement à partir de la clé publique contenue dans l'identifiant DID.
Ils sont retournés par le resolveur myDiD si aucune donnée enregistrée on-chain n'est trouvée pour ce DID.

Exemple :
```
{
    "@context": [
                    "https://www.w3.org/ns/did/v1",
                    "https://w3id.org/security/v1"
    ],
    "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
    "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
    "authentication": [
        {
            "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#AUTH_1",
            "type": "EcdsaSecp256k1VerificationKey2019",
            "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
            "publicKeyMultibase": "z26TcxBC6RXfhYJw64tN9cofSKzk6NoV7xWaKMwEdG7LFi"
        }
    ],
    "assertionMethod": [
        {
            "id": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe#ASSR_1",
            "type": "EcdsaSecp256k1RecoveryMethod2020",
            "controller": "did:myDiD:zhSRenUwHt39nmDDNkJinJUZQCzcs8pBF9iNaaAiyPrLe",
            "blockchainAccountId": "eip155:01:0x7174143ac405967a32FA7656BF6efDF4Ba52574D"
        }
    ]
}
```
# DID Document Operations
## Creating a DID
* Generate key pair
* Publish public key to myDiD smart contract
* DID identifier created based on Ethereum address of public key
* Initialize DID Document on-chain

## Updating a DID Document
* Send signed transactions to myDiD smart contract
* Emit events for off-chain updates
* Updates appended to DID Document history
## Revoking / Deleting a DID Document
* Revoke DID Document by setting the status to "revoked"
* Delete a DID by sending delete transaction to smart contract
# DID Resolution
To resolve a myDiD DID identifier:

* Extract Ethereum address from identifier
* Query myDiD smart contract to fetch on-chain DID Document
* Retrieve off-chain DID Document using URI reference
* Return merged view of on-chain and off-chain DID contents
# Implementation
myDiD is implemented as:

* Solidity smart contract deployed on EVM Blockchain
* NodJs reference resolver
* React webapp for managing DIDs
# Conformance
myDiD is compliant with the Decentralized Identifier specification from W3C.
