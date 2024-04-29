# Hyperledger Indy, Aries, AnonCreds, and Ursa

## Indy

- The code was contributed by Sovrin Foundation
- Indy is a public and permissioned distributed ledger built for de-centralized identity use cases
- It includes components for building agents that enable publishing DIDs
- It was initially an all-in-one solution but later it was compressed down to be just a ledger and tools for managing ledger and the client libs for interacting with ledgers (reading and writing)

## Ursa

- All the crypto code in Indy was migrated to Ursa so it can be used in other hyperledger projects
- Ursa gives those experts a place to work together to build and package cryptographic primitives—combinations of low-level cryptography elements that only experts should build, test, and make available to "ordinary developers."

## AnonCreds

- It's a ZKP-based verifiable credential mechanism that was migrated from within Indy to a standalone project

## Aries

- It's the agent part of the hyperledger SSI stack
- The core of Aries is a set of protocol specifications to enable secure, DID-based messaging between agents (using DIDComm) and a set of higher-level protocols for using that messaging to accomplish business goals, such as issuing, presenting, and verifying credentials

## References

- https://github.com/cloudcompass/ToIPLabs/blob/main/docs/LFS172x/running-aries-browser-lab.md
