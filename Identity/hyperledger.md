# Hyperledger Indy, Aries, AnonCreds, and Ursa

## Indy

- The code was contributed by Sovrin Foundation
- Indy is a public and permissioned distributed ledger built for de-centralized identity use cases
- It includes components for building agents that enable publishing DIDs
- It was initially an all-in-one solution but later it was compressed down to be just a ledger and tools for managing ledger and the client libs for interacting with ledgers (reading and writing)
- Uses of cryptography offered by Indy
  - generation of public/private key pairs
  - data encryption and decryption
  - data signing and verifying
  - data hash generation and verification
  - zero-knowledge proof (ZKP) technology, including issuing and revoking ZKP credentials and generating and verifying ZKPs
- Ursa takes the "raw" cryptographic algorithms and packages them up so that they can be embedded in, for example, Indy and Aries and used safely

### Indy Clients

The first iteration of Indy client software is in the indy-sdk repository. The indy-sdk contains all of the software in one package to interact with Indy as an SSI agent. This includes:

- A set of calls for interacting with the ledger, supporting reading and writing Indy transactions to both use and operate the network.
- A set of utilities supporting common functions, such as DID handling, and various cryptographic operations.
- A secure storage element (called indy-wallet) that is both a key management service as well as a repository for all of the information collected by an SSI agent (connections, DIDs, keypairs, verifiable credentials, protocol state, etc.).
- A complete implementation of AnonCreds supporting issuing, holding, proving and verifying AnonCreds verifiable credentials.
- Software to handle the exchange of verifiable credentials amongst the participants.

The second iteration of Indy client software was implemented to modularize the capabilities, splitting them up from a monolithic component and moving them to where they belong in the Trust over IP stack. From the list above:

- The first two items remain as part of the Indy project in repositories indy-vdr (ledger interactions) and indy-shared-rs (common utilities).
- A new secure storage element was derived from the “indy-wallet” called Aries Askar (repository aries-askar) in the Hyperledger Aries project. Askar supports the storage and processing of Indy operations, but adds support for using the other ledgers and key types that are needed by Aries implementations.
- The AnonCreds verifiable credential implementation is now in the AnonCreds project.
- All software related to verifiable credential exchange has been moved into Aries.
- The second iteration of Indy client software has had two impacts. First, it limits the Indy client to what is needed for interacting with Indy networks. As well, by breaking up the first generation monolith into its component parts, significant improvements have been uncovered in terms of raw speed and sustained performance under load.

Both the first and second generation clients are implemented as a series of modules written mostly in the programming language Rust that are compiled into "C-callable" libraries. C-callable means that the library components have a standard interface such that they can be called from a majority of languages (Java, C#, Python, JavaScript, etc.), eliminating the need for an implementation in each language. The repositories also include language-specific "wrappers" for the library. The wrappers, currently available in Python, Java, C# (.NET) and Node.js, allow the embedding of Indy components into programs written in each of those languages.

### Indy Ledger Client Architecture

Indy client functionality (`indy-sdk` and `indy-vdr`) is implemented as a series of messages that enable the client application to read from, write to, and configure an Indy network. The high-level process for interacting with the ledger is the same for each call:

- The application prepares the data for the message to be sent, e.g., the type of message and all of the parameters necessary for that type of message.
- If the message is a write or configured, the application calls cryptographic functions to sign elements of the message with a private key held by the application for those purposes. This is necessary to prove that the application has permission to execute the transaction on the network.
  - Multiple signatures from different parties may be needed depending on the roles of the parties and the impact of the message on the network. For example, if the application is not authorized to write to the ledger, the transaction must be sent to a party that is authorized to write to the ledger (called an "endorser") to sign the transaction. Once signed, either the author or the endorser can submit the transaction to the ledger (the next step).
- The message is sent to a verifier node that is part of the Indy ledger.
- The application waits until a response is received from the ledger with the result code (success or failure) and any related data.
- The ledger data relevant to participants in verifiable credential exchanges (issuers, holders, and verifiers) are covered in the next chapter - when we talk about what goes on the blockchain. Many configuration transactions alter the operation of the ledger itself, such as adding and removing verifier nodes from the network and controlling permissions about who can write what ledger transactions. We’ll not cover those further in this course but are relevant to those who want to participate in the operation of an Indy network.

The read transactions support retrieving all of the types of transactions that are stored on the ledger. Reads don’t require permissions so anyone can read data from an Indy ledger.

A surprise for many that are new to Indy is its ledger does not support any form of querying by value or discovery. For example, you can’t just make a single query on an Indy ledger to get a list of all of the DIDs on the ledger. In the common cases, the agent "knows" about the transactions of interest on the ledger either because that agent wrote the data in the first place, or because the agent received an identifier for the ledger. For example, a verifier receives the DID of the issuer in a presentation from a holder. Although you can’t query the ledger, it is a public ledger, so you can read its entire contents and if you want a query capability, you can put it in a database. How? Since all the transactions are sequenced, you can just start from one and iteratively query the ledger for all the transactions (one, two, three, four…). Once you have read them all (e.g., the ledger returns "no such transaction"), every few minutes ask for any new transactions that have arrived. It’s onerous and so several people have published websites of "ledger browsers" (Sovrin Main Net and Indyscan) that continually query the Sovrin MainNet and provide a web interface for searching. Here is a short demonstration of browsing an Indy ledger.

NOTE: Adding a querying/discovery capability that works in a similar way to the ledger browsers but that is implemented by Indy is likely not a difficult task. It’s just not risen high enough on the priority list to be implemented as a core feature.

As noted, the only capability of the second generation indy-vdr component is to provide an interface to read and write to an Indy ledger. Having it in an isolated package makes it easier for an Aries agent to use it to integrate with an Indy ledger alongside resolvers for other ledgers. indy-vdr includes in its codebase a web server wrapper that allows deployment of indy-vdr as a service for a set of Indy clients. For example, an enterprise might have Aries agents for a number of sub-organizations in the enterprise all sharing the same robust, scalable indy-vdr service to handle all of the reads and writes to Indy networks.

## Ursa

- All the crypto code in Indy was migrated to Ursa so it can be used in other hyperledger projects
- Ursa gives those experts a place to work together to build and package cryptographic primitives—combinations of low-level cryptography elements that only experts should build, test, and make available to "ordinary developers."

## AnonCreds

AnonCreds and all other verifiable credential formats use the same mechanics. The issuer publishes some cryptographic information somewhere, and uses related information to sign the credential given to the holder. When the holder shares a presentation with a verifier, the verifier retrieves the cryptographic information published by the issuer to verify the proof. One of the key features of SSI is that the verifier need not go back to the issuer to get the published information, they get it from an independent source. That’s where ledgers like Indy come in.

Hyperledger Indy provides code bases for both deploying a public distributed ledger technology (DLT) and building applications that interface with such ledgers

- It's a ZKP-based verifiable credential mechanism that was migrated from within Indy to a standalone project
- AnonCreds makes use of the most novel part of Ursa, zero-knowledge proof (ZKP) cryptography
- simplify the technology for use beyond Indy: ledger-agnostic
- Exciting features
  - Presentations are derived from their source verifiable credentials, so the holder is not providing the verifier with their entire original verifiable credential.
  - No unique identifier is given for the holder or credential just because a proof was presented.
  - A binding to prove a presentation is from the holder to which the credential was issued.
  - A binding to prove that when multiple source credentials are used for a presentation, all were issued to the same holder.
  - Ways to minimize the data shared from the source credentials.
  - A holder can prove selected attributes from a credential.
  - A holder can use "ZKP predicates" to prove an expression based on a credential attribute, such as "I’m older than 21" based on date of birth without having to share date of birth.

## Aries

As we have learned, an SSI ecosystem consists of participants that communicate with one another to issue, hold, prove and verify verifiable credentials and presentations. In doing so, those components interact with Verifiable Data Registries (often distributed ledgers/blockchains such as Indy) to publish and retrieve cryptographic keys and verifiable credential metadata. To exchange those credentials and presentations, the participants (issuers, holders, and verifiers) use agents (software) that send data directly (peer-to-peer) to one another. Agents are operated by individuals and organizations all over the world, built using open source tools, commercial products and/or custom-built, proprietary solutions. How do all those implementations work with one another? How do they find each other, connect and exchange credentials (and other data), if each one is using its own code base, written independently? How are they customized for each business use case? How do users of different languages and cultures interact?

Those are the challenges that started the Hyperledger Aries project. Aries is about collaborating to define and use common message exchange protocols, a common agent architecture, and define tests to enable organizations to independently build many kinds of SSI agents that will all work together. Although Aries began with roots in Hyperledger Indy and AnonCreds, Aries is both ledger- and verifiable credential format-agnostic, enabling the use of different ledgers and verifiable credential implementations within a single agent. And, since Hyperledger and the Linux Foundation is about building open source software, Aries includes implementations of the protocols, architecture, and tests.

- It's the agent part of the hyperledger SSI stack
- The core of Aries is a set of protocol specifications to enable secure, DID-based messaging between agents (using DIDComm) and a set of higher-level protocols for using that messaging to accomplish business goals, such as issuing, presenting, and verifying credentials
- In Aries, the "issue credential" protocol functions as an issuer agent coordinates with a holder agent to offer, be asked for and deliver a verifiable credential.

## References

- https://github.com/cloudcompass/ToIPLabs/blob/main/docs/LFS172x/running-aries-browser-lab.md
