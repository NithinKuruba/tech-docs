
# Verifiable Credentials

## Introduction

How do we fix the issue of trust on the Internet? In the last chapter, we talked about a number of challenges with the current state of identity on the Internet. We also talked about how we’ve (sort of) handled trust in the past with paper credentials. In this chapter, we’ll talk about solutions that are becoming available that will add that missing layer of trust. To do that, we will move our discussion from paper credentials to verifiable credentials—credentials we can use (and trust!) online. In doing so, we introduce the important concepts of self-sovereign identity (SSI) and trust over IP (ToIP). Lastly, we will explore the technology that enables verifiable credentials (for example, decentralized identifiers (DIDs) and agents), and how all of this together adds a layer of trust to the Internet.

A credential is (formally) an attestation of qualification, competence, or authority issued to an entity by a third-party with a relevant or de facto authority or assumed competence to do so. Examples of paper versions of credentials are a driver’s license, passport, and university degree. Organizations also have credentials, such as their Certificate of Incorporation, a business license, and other permits and licenses they get from regulators to operate. What if we could get credentials from the same sources as our paper credentials and use them online in a fully trusted manner both for the holder of the credential and the person or organization verifying the credential? Something digital that we can trust much more than just scanned pieces of paper.

The Hyperledger identity projects, Indy, Aries, AnonCreds and Ursa are specifications, tools, and libraries that allow for the development of independent digital identities rooted on blockchains or other distributed ledgers. They help bring about the possibility of building applications with a solid digital foundation of trust by enabling the verifiable credentials model. The verifiable credentials model brings us to a deeper dive into the first of the Hyperledger identity projects, AnonCreds, short for "Anonymous Credentials."

## The Verifiable Credentials Model

There are three separate interactions that occur in the verifiable credential model—steps quite similar to paper credential interactions.

First, the issuer prepares for the issuance of credentials by publishing some cryptographic public keys and (as needed) other data about the credentials to be issued into the Verifiable Data Registry (VDR). A Hyperledger Indy instance is an example of a VDR. In the paper world, this "setup" might be a very formal step for some documents (a passport or a driver’s license), rooted in government legislation, and in other cases, it is informal—an organization just starts issuing paper documents to clients.
Next, the issuer issues a credential to the holder. The issuer might issue millions of the same type of credential to millions of holders. A holder might receive many verifiable credentials from many different issuers. Before issuing a credential, an issuer will carry out some process to confirm that the holder is eligible to receive a credential and to determine what data to put into the credential’s claims.
From time to time, verifiers request a presentation from the holder, and the holder creates and presents the proof to the verifier. The presentation may be generated from one or more credentials held by the holder. The verifier collects from the VDR(s) data published by the issuer(s) and cryptographically verifies the presentation from the holder. Note that the issuers are not involved in this interaction.
We won’t get into this right now, but in the interests of full disclosure, there is one more operation supported for some credential types—revocation of credentials by the issuer.

For those not familiar with what is cryptographic signing, the following is a high-level summary. An issuer wants to share some information such that later, others can know for certain it was produced by the issuer and has not been altered. At the lowest levels, this is done by math operations, as follows:

The issuer creates a pair of numbers that are mathematically linked, a private key, and its corresponding public key. The issuer keeps the private key to itself (crucial!!) and publishes the public key so anyone that needs it can get it. The issuer might publish it on a VDR, such as an instance of Hyperledger Indy.
The issuer runs an operation that takes as inputs the information it wants to sign and the private key, and produces a long number—the signature. The signature is unique to the information and the private key.
Later, a verifier gets the information that was signed, access to the public key, and the signature.
The verifier retrieves the public key, and runs a verification process that takes as input the information that was signed, the signature, and the public key.
The process returns "verified!" if the signature is found to match or "false" if anything goes wrong, such as the information that was signed had been altered, or the public key is not the one linked to the private key that was used.
The attributes of the cryptography—the private/public keys and the processes to produce and verify the signature—are such that:

it is exceedingly unlikely that anyone could produce the signature without knowing the private key,
it is exceedingly unlikely that anyone could alter the information and still produce the same signature, and
it is exceedingly unlikely that the signature would be verified using the public key, if it was not the signature produced from the information to be signed.
As we’ll see with good cryptographic routines such as is found in Ursa, "exceedingly unlikely" means that it wouldn’t happen in billions of tries taking hundreds of years.

Note that description covers just the primitive "signing" operation. There are several other similar cryptographic primitives used in the issuing of verifiable credentials and in verifying presentations produced from such credentials.

For identity, verifiable credentials are digital, cryptographically-signed data attributes issued by authorities that you can use to prove you are you. The data is the same as their paper equivalent, but it is the cryptographic signing that makes them so useful online. Verifiers aren’t just physically looking at the document (or worse, the scan of a document) to determine who issued it, whether it was issued to you, and if it has been tampered with. As a result, the problem of whether the verifier trusts the holder more or less goes away. The holder is not self-asserting data, and cannot forge the cryptography protecting the verifiable credentials. As a result, the data can be accepted as accurate.

A holder will generate a presentation that will be checked by the verifier

However, there remains a challenge with a verifiable credential—and it's actually the same thing with a paper credential: Do you trust the issuer and the process by which the issuer decided to issue the credential?

To trust the information in a credential it is necessary that the verifier trusts the issuer and that the issuer’s pre-issue processes were carried out with integrity. For an official government-issued identity document (driver’s license, passport, etc.) that might be an easy decision. In many cases the verifier will require the holder to present credentials only from issuers known to the verifier.

But what about use cases when the issuer is one of thousands (or millions!), such as a university transcript? For example, when you verify a presentation from Alice generated from a credential issued by Faber College that shows Alice has received a degree, you will know that Faber did indeed issue the credential to Alice, that Alice did not tamper with it, and it has not been revoked. But is Faber College an authorized (and by whom?) degree-issuing entity or an unregulated diploma mill? And as a result, does the verified data show that Alice legitimately earned a degree through hard work and proving her knowledge, or did she just pay Faber $100 to be given a degree? These same questions come up for both paper and verifiable credentials. However, with verifiable credentials some interesting options become available for automating the research into the "should we trust the issuer?" question. We’ll talk more about this topic later in the course.

You will often find that in digital identity circles, the terms credential and claim seem to be used interchangeably. Claims are assertions about a person (or business).

For example:

"The name of the holder is Bob. He was born on June 18, 2000."

or,

"Cloud Compass Computing, Inc. was incorporated in 2011."

A credential is made up of a set of individual claims. For example, in the case of a driver’s license, the claims would be a person’s name, address, height, weight, hair color, driver’s license number, and so on. Throughout this course, we’ll use claim to mean a data element within a credential, and a credential to be made up of a set of claims.

### Claims versus Credential

In all uses of a verifiable credential, what is issued is a credential. However, when the signed data from a credential is presented, in some implementations the entire credential is proven, while in others (including in Hyperledger AnonCreds), the claims within the credential are proven individually. This enables an increase in the privacy of the holder, as they can share only some of the claims in the credentials—just enough for the business purpose to be accomplished. No need to show a bartender the address on your driver’s license just to prove you are old enough to buy a drink! AnonCreds has even more privacy preserving capabilities that we will cover in Chapter 6—a lot more than any other kind of verifiable credential.

Another pair of terms that might seem to be used interchangeably are proof and presentation. A proof is evidence of the claim (for example, a birth certificate, passport or driver’s license, or in the case of a business, incorporation papers). In the early days of the Hyperledger Indy project, the term "proof" was used when talking about a verifier requesting verifiable (signed) claims from a holder and the holder providing those claims to the verifier. Later, "presentation" was selected to mean essentially the same thing by the W3C Credentials Community Group and it is used in the W3C Verifiable Credentials data model standard. This was done because in the W3C verifiable credential data model, the "proof" is the generic term for the cryptographic signatures across any data—for example, the signature across the data in a credential or in a presentation.

As such, we mostly use "presentation" in this course to refer to the verifiable data received by a verifier from a holder. However, at times we may use "proof" as well. For example, we’ll often use the phrase "present a proof," which sounds a little better than "present a presentation." For developers, recognizing both terms in context is important because the underlying AnonCreds, Indy, and Aries source code uses both terms as well.

So what does the availability of verifiable credentials mean? Well, in the past, you have probably shared identity attributes online as typed in (e.g., self-asserted) text, such as your name, address or government ID, or perhaps shared a paper credential you were given by taking a picture of it and uploading the picture to an online service. Verifiable credentials are very different because of the cryptography used to secure the data such that once verified, and you trust the issuer, the information can be trusted to be authentic. That is really powerful!

For example:

Instead of typing in your name, address and government ID, you provide a presentation of that information from verifiable credentials issued to you by an authority trusted by the verifier. This saves you typing in the data, and the verifier can automatically accept the claims in the presentation (if they trust the issuer) without any further checking, saving them money and time.
Anyone who knows your government identifier would not be able to impersonate you on a site that collects that identifier using verifiable credentials because the information they know does not come from a verifiable credential issued by the government to you.
A doctor can be issued a professional accreditation credential from the relevant authority (e.g., the College of Physicians and Surgeons) and those claims can be verified (and trusted) by medical facilities in real-time. Should the doctor lose his or her accreditation, the credential can be revoked, which would be immediately in effect. This would hold true for any credentialled profession be it lawyers, engineers, nurses, tradespeople, real estate agents, and so on.
Examples are endless. Any place that self-asserted data and paper documents are relied upon for important decisions, verifiable credentials are a better, faster, cheaper option.

The verifiable credential model has other important ramifications:

Your verifiable credentials are issued to you, stored in your digital wallet, and you decide when and where you want to use them. That improves your privacy—you are in control of when and with whom you share your information.
Verifiable presentation data is proven without needing to call back to the issuer. Just as when you use your paper driver’s license to prove your age without the verifier calling back to the government, there is no need to call back to the government when you use the verifiable credential version of your driver’s license. Instead of getting the data directly from the issuer, the data from the issuer comes from the holder, and the cryptographic material to verify the authenticity of the data comes from the Verifiable Data Registries, or VDR (often, a blockchain). This reduces the number of integrations that have to be implemented between issuers and verifiers. Imagine if every company needed to integrate with every degree-issuing institution to see if a job applicant’s claim of a degree was valid? That’s a lot of interfaces! There are still challenges in sharing the data, such as knowing what a grade of "A" means on one transcript vs. a grade of "92" on another, but the mechanisms are much better.
Verifiable credentials go beyond identity to enable the digitization of almost any paper-based verification process. For example, regulatory processes that are currently implemented with manually verified paper submissions can be reworked to use verifiable credentials that make the process faster and much, much cheaper!
Verifiable credentials are really the heart of this course, so we’ll spend Chapter 6 diving into AnonCreds, other kinds of verifiable credentials, and the different capabilities provided by each kind.

### What is self sovereign identity?

Self-sovereign identity (SSI) is a term you may have heard in connection with the Hyperledger identity projects, Indy, Aries, and Ursa. Together, these Hyperledger capabilities can be the basis for accomplishing SSI. So what is SSI—and what isn’t?

SSI is the idea that your data is given to you, you control that data including when and how it is provided to others, and that when it is shared, it is done so in a secure way. With SSI, there is no central authority holding your data that passes it on to others upon request. And because of the underlying cryptography and blockchain technology, SSI means that you can present claims about your identity and others can verify those claims with cryptographic certainty.

SSI is not about you self-asserting information about yourself and demanding that everyone else accept it as fact. Recall that the verifiable credentials you receive and present to verifiers come from others—those with authority to issue such data, such as the government, universities, professional organizations, and so on. The usefulness of the verifiable credentials relies on the trust verifiers have in the credential issuers. If you want, you can issue credentials to yourself ("I am the King of England"), but verifiers probably won’t trust you as an issuer!

The Sovrin Foundation is a private-sector, international non-profit that was established to govern and operate the Sovrin Identity Network, a public, permissioned distributed ledger purpose built (based on Hyperledger Indy) for identity. The mission of the Sovrin Foundation is to create the Internet’s long-missing trust layer and provide a global public utility for digital identity to people, organizations, and things. In addition to the mechanics of operating a global instance of Hyperledger Indy, Sovrin has brought together many leaders and deep thinkers from the self-sovereign identity community to collaborate on a series of foundational principles and governance and glossary documents. From that work, the Sovrin Foundation has published these 12 principles of self-sovereign identity:

 

Table: 12 Principles of Self-Sovereign Identity
Principle

What It Means

Representation

An SSI ecosystem shall provide the means for any entity—human, legal, natural, physical, or digital—to be represented by any number of digital identities.
Interoperability

An SSI ecosystem shall enable digital identity data for an entity to be represented, exchanged, secured, protected, and verified interoperably using open, public, and royalty-free standards.
Decentralization

An SSI ecosystem shall not require reliance on a centralized system to represent, control, or verify an entity’s digital identity data.
Delegation

An SSI ecosystem shall empower entities who have natural, human, or legal rights in relation to their identity ("Identity Rights Holders") to control usage of their digital identity data and exert this control by employing and/or delegating to agents and guardians of their choice, including individuals, organizations, devices, and software.
Participation

An SSI ecosystem shall not require an identity rights holder to participate.
Equity and Inclusion

An SSI ecosystem shall not exclude or discriminate against identity rights holders within its governance scope.
Usability, Accessibility, and Consistency

An SSI ecosystem shall maximize usability and accessibility of agents and other SSI components for identity rights holders, including consistency of user experience.
Portability

An SSI ecosystem shall not restrict the ability of identity rights holders to move or transfer a copy of their digital identity data to the agents or systems of their choice.
Security

An SSI ecosystem shall empower identity rights holders to secure their digital identity data at rest and in motion, to control their own identifiers and encryption keys, and to employ end-to-end encryption for all interactions.
Verifiability and Authenticity

An SSI ecosystem shall empower identity rights holders to provide verifiable proof of the authenticity of their digital identity data.
Privacy and Minimal Disclosure

An SSI ecosystem shall empower identity rights holders to protect the privacy of their digital identity data and to share the minimum digital identity data required for any particular interaction.
Transparency

An SSI ecosystem shall empower identity rights holders and all other stakeholders to easily access and verify information necessary to understand the incentives, rules, policies, and algorithms under which agents and other components of SSI ecosystems operate.
 

A number of these principles are technical in nature and are enabled by Indy, Aries, AnonCreds, and Ursa. Some are about how those technical projects are managed—for example, transparency by using open source software based on well-defined specifications. Others are about how the technology is used, and while Indy, Aries, AnonCreds, and Ursa focus on making it easy for entities to implement SSI solutions, governance—such as the European Union’s General Data Protection Regulation (GDPR)—is still necessary. We’ll talk about governance a lot in this course.

### What are decentralized identifiers?

While verifiable credentials are an important component of SSI, a thing called a decentralized identifier (DID) is a key enabler for verifiable credentials. DIDs are a new type of identifier that is now a W3C (World Wide Web Consortium) standard. As we discussed, the verifiable credential model often relies on a decentralized ecosystem in order to work. Such an ecosystem is brought about with DIDs and software agents (discussed later in the chapter).

DIDs are a special kind of identifier that are created by their owner, independent of any central authority. Per the DID specification, a DID looks like the following and is similar to an HTTP address but used differently.

What does a DID look like?

 

An Example of a DID Updated

An example of a DID
Licensed under CC BY 4.0

 

NOTE: This section gets a little technical, but don’t worry, we’ll go through this and then come back to what DIDs mean from a business perspective.

DIDs are:

A new type of uniform resource identifier (URI).
Created by anyone at any time.
Globally unique.
Highly available.
Cryptographically verifiable.
What does all that mean? Let’s go through each of those attributes in turn:

Like the URLs (universal resource locator) that we are familiar with (such as the one for this web page that is in the address field of your browser), DIDs can be resolved. When we pass a valid DID to a piece of software called a DID Resolver, it works like a browser given a URL, resolving the identifier (URL or DID) to find and return a document. However, instead of a browser returning a web page, a DID Resolver returns a DID Document (DIDDoc). A DIDDoc contains JSON data whose format is defined in the DID specification. We’ll talk about what’s in a DIDDoc in a moment.
A DID is an identifier that can be created by anyone, not just a central service such as a government, an email provider, or a Certificate Authority (CA). People, organizations, and things can all create, publish, and/or share their own DIDs. Further, in some cases, the controller of the DID (usually the creator) can update the DIDDoc—change the contents returned when resolving the DID. That ability can be extremely important in certain situations.
That DIDs are globally unique means that properly generated, each DID is unique across the globe. The "properly generated" caveat means that if you follow the rules for creating a specific type of DID, it will be globally unique. That’s the type of detail that Hyperledger Indy takes care of for you.
Highly available means that DIDs should be resolvable even if some servers are down. Since DIDs are often resolved by reading from a distributed ledger, and there can be many copies of a given distributed ledger on servers across the world, DIDs on ledgers are generally highly available. Put another way, DIDs should not be susceptible to a central implementation with a single source of failure.
Cryptographically verified means that the control of a DID can be verified by asking the DID controller (the person, organization, or thing that created the DID) to prove their control over the DID by having them prove control of a private key that is related to a public key in the DIDDoc.
This last point brings us to what is in the DIDDoc. Notably, a DIDDoc contains (usually) public keys whose private keys are held by the entity that controls the DID, and (often) service endpoints that enable communication with that entity. This means that with a DID, you can:

Resolve a DID to get a DIDDoc.
Within the DIDDoc, find a public key and (optionally) an endpoint for the entity that controls the DID.
Use the endpoint to send a message to that entity.
In the message, ask them for proof they have the private key related to the public key.
- Since often a public key in the DIDDoc is used to encrypt the message sent, just being able to respond to the message is "proof" of control of the DID. Without the private key they could not have decrypted the message.
- What the message might ask is that the controller of the DID provide a verifiable credential presentation about who they are.
Receive and verify the proof.

DIDs are identifiers that you create and share under your control, which is a core tenant of SSI. Most identifiers that you use today are created or controlled by someone else:

your government ID
your phone number or email address
your account at a particular website.
If a central authority decides to take that identification away from you, they can remove your ability to use it. Since you created a DID and hold the private key(s) for it, only you can "delete" the DID. A DID (and DIDDoc) on a blockchain can never be removed, so "deleting" a DID really means no longer responding to requests for proof of control of the DID. Technically, you can update the DIDDoc to delete the public key(s), but assuming earlier versions of the DIDDoc are on a blockchain, those earlier versions are still available.

There are few easy, trusted ways for you to prove your control over commonly used identifiers today. From the list of identifiers mentioned on the previous page, the only ones that you can prove control over are your email address and phone number, and those have become prime targets for hackers, often by attacking the central authority that gave you the identifier. The association of public/private keypairs enables a more secure mechanism for proving control of a DID.

As we’ve talked about, one of the attributes proven with a verifiable credential presentation is who was the issuer. In fact, what is proven is not the full identity of the issuer, just an identifier for the issuer, usually a published DID. However, because of the characteristics of DIDs, that’s enough to either know or determine the identity of the issuer. The verifier might "just know" the DID by having a hard-coded list of trusted issuers (trusted DIDs) in the verifier agent. Alternatively, the verifier might look up the DID in an external list of trusted issuers (commonly known as a "Trust Registry"). Or, using the issuer’s DID, the verifier can retrieve the DIDDoc for the DID, use the service endpoint in it to connect to the issuer, and ask for identity data about the issuer. Of course, in this case, "asking" means "give me a verifiable presentation about your authority to issue credentials."

One way or another, we have to find a way to determine if we can trust the data we receive!

One of the problems with the common identifiers that we use today is that it is often necessary to reuse them. On almost every site we visit, we provide an email address so that the site can send us notifications. That means that many sites can share information about us just by correlating our email address. Since we create our own DIDs and choose how we want to share them, we needn’t have just one DID, but rather one DID for every service to which we connect. The expectation is that you will have many DIDs—one for each relationship you have on the Internet. Note that both sides of a relationship provide a DID for the other to use to communicate with them, as shown below.

 

DID Relationships Updated

DID relationships
Licensed under CC BY 4.0

 

Just as today when you first register with a site, you create an account user ID and password; with DIDs, you will create and share a new DID (and DIDDoc) with the site. When you return to the site, providing the DID and proving that you control the DID are sufficient for the service to know who you are. Hey, a way to eliminate user IDs and passwords!

As described earlier, a DIDDoc contains public keys and service endpoints for the entity that controls that DID. This enables a powerful capability: a secure, end-to-end encrypted messaging mechanism. Given a DID and its corresponding DIDDoc, an entity can encrypt a message for the controller of the DID (using the public key in the DIDDoc) and send it to the designated service endpoint. Upon receipt, the DID’s controller can decrypt (using the corresponding private key) and process the message. We’ll cover this capability a lot more in later chapters, including the fact that with this capability, there is no need to share an email address to receive notifications from a service. The service can just use the messaging capability inherent in the use of DIDs.

Let’s look at the types of DIDs for a minute because there are important differences between public and private (pairwise) DIDs.

 

Public DID

Public DID
Licensed under CC BY 4.0

 

This demonstration goes over the following information about public and private DIDs. The pictures are a bit complicated!

A public DID is one that is intended to be widely available—visible to anyone that comes across the DID. Since anyone can both resolve the DID and contact the owner, if the DID is used many times, it is correlatable. In this case, correlation isn’t a bad thing. A company or government, for example, will often use public DIDs. A prime example of a public DID is the DID of a verifiable credential issuer. Since the holder of the credential may present the credential to anyone, the identity (via the public DID) of the issuer must be part of what the verifier learns from the presentation. That way, the verifier can investigate (as necessary) to decide if they trust the issuer. Public DIDs are usually put on blockchains so that they are highly available, controlled decentrally, and globally resolved.

On the other hand, an important use case of DIDs is that they enable two (or several) parties to connect with one another. In this case, it’s not important that everyone in the world be able to resolve the DIDs for the parties—just the parties themselves—and private DIDs (often called pairwise DIDs) are used. Instead of publishing the DIDs on the blockchain for anyone in the world to see, the entities create the DIDs and DIDDocs and then send both directly to the other party(ies) to hold. If they need to update the DID, such as to change the endpoint, the update is sent directly to the other party(ies) to update their copy of the data. No one other than the parties involved can see or resolve the DIDs.

 

Private DID

Private DID
Licensed under CC BY 4.0

 

Why are private DIDs important? There are two reasons:

First, since the DIDs are only intended for use by a small, specific group (usually just two), the DIDs are not shared beyond that group.
Second, writing to a blockchain is expensive and by not writing DIDs that don’t have to be public to the blockchain, the costs (resources, time, transaction fees) are reduced.
Happily, private DIDs far (far) outnumber the DIDs of verifiable credential issuers and as a result, the load on a public blockchain serving DIDs is massively reduced.

In the Aries section of this course, we’ll learn about the DID method called "did:peer," which defines the mechanism for sharing and using private, pairwise DIDs.

Let’s come back to cover how DIDs are used, including how DIDs are used with verifiable credentials. It’s not always clear!

First, in an SSI ecosystem of issuers, holders, and verifiers, such as Hyperledger Aries agents, DIDs can be used to establish secure, point-to-point messaging channels between the agents of the participants. In this use of DIDs, there are no verifiable credentials involved, just messages encrypted and sent to other participants using the information in the DIDDocs (notably, public keys and service endpoints) to allow a sender to encrypt (using the recipient’s public key) a message to the recipient and send it (using the recipient’s service endpoint). The recipient must have the associated private key to decrypt and process the message.

Second, with a verifiable credential, DIDs are used as the identifier for the issuer. The issuer DID is used to uniquely identify the issuer and is resolved to get a public key related to the DID. That public key is then used to verify the data in the verifiable credential did indeed come from the issuer—to check the cryptographic signature using the public key of the DID.

 

Uses for Public and Private DIDs

Uses for public and private DIDs
Licensed under CC BY 4.0

 

NOTE: To reiterate, all Aries agents have private pairwise DIDs for messaging. In addition, issuers have a public DID that is with the verifiable credentials they issue to holders. That DID ensures the verifier knows who issued the credential presented by a holder.

### Agents and Wallets

A third key component of SSI, along with verifiable credentials and DIDs, is the software that is used to process verifiable credentials and DIDs—what we’ve called your digital wallet to this point in the course. Indy, Aries, and Ursa use the term agent to mean the software that interacts with other entities (via DIDs and more, as we'll find out). For example, a person might have a mobile agent app (a Wallet!) on their smart device, while an organization might have an enterprise agent running on an enterprise server, perhaps in the cloud.

All agents (with rare exceptions) have secure storage for holding identity-related data including DIDs, keys, and verifiable credentials. As well, all agents are controlled to some degree by their owner, sometimes with direct control (and comparable high trust) and sometimes with minimal control, and far less trust. We’ll cover different uses and types of agents in a later chapter.

 

A Mobile Agent App

A Mobile Agent App
By Peter Stokyo
Licensed under CC BY 4.0

 

For those familiar with a password manager such as 1Password or LastPass, you’ll find a personal agent is quite similar—there is a name for each relationship and associated data. However, unlike password managers that use things such as your copy/paste clipboard for user IDs and passwords and screen-scrape applications and websites, agents communicate directly with other agents to accomplish identity-related tasks. As agents use messaging to communicate with one another, users will also see a similarity between agents and the messaging apps we use today, such as Telegram, Signal, and WhatsApp. Unlike those apps, agents use messaging for far more than just sending text, images and links, including for transactions around the exchange of credentials and presentations. Further, we expect agents to be the cornerstone of self-sovereign identity, which is different from the foundations of those "traditional" messaging apps. We dig much more into agents in Chapter 5 of this course.

Along with SSI, another term you will hear surrounding Indy, Aries, and Ursa is Trust over IP (ToIP). ToIP is a layered set of technical protocols to enable trust on the Internet and the parallel set of governance frameworks that define how those protocols are used. An instance of those technical protocols at layers 1-3 of the ToIP technical stack are embodied in Indy, Aries, and Ursa. ToIP includes self-sovereign identity in that it covers identity, but goes beyond that to cover any type of authentic data. Authentic data in this context is data that is not necessarily a credential (attributes about an entity) but is managed as a verifiable credential (issued, held, presented) and offers the same guarantees when presentations are proven.

ToIP is defined by the "Trust over IP Dual Stacks," as represented in this image from the Trust over IP Foundation:

 

Trust over IP (ToIP) Technology Stack

Trust over IP (ToIP) technology stack
Licensed under CC BY 4.0

 

 

You should recognize most of the components of the ToIP technology stack from what we’ve already talked about in this course. At the bottom right, the foundation of the technical stack are public ledgers that store decentralized identifiers (DIDs) and (in some cases) other data necessary for the higher layers. Above that are agents and the protocols that enable agents to establish connections and exchange messages (information). We’ll talk about agents in Chapter 5. The next layer up is the data exchange protocols layer, where verifiable credentials are issued and presented to enable trusted information flow—data cryptographically signed by the participants.

NOTE: The "Technical Trust" is enabled by cryptography at layers 1 and 2, while "Human Trust" is enabled through data exchange protocols and application ecosystems at layers 3 and 4.

Cont’d on the next page.

The governance stack parallels the technical stack by outlining the rules that the participants at each layer agree to follow to enable that layer (and those below) to work in practice. At each layer are governance frameworks that outline in human-readable form the specifications, rules, and agreements that all the participants agree on. Example ToIP governance frameworks include the following topics we’ll cover in this course:

How the readers and writers of the blockchain operate.
How the agents know the meaning of messages that form a protocol.
What the claims in credentials signify to the verifier.
By what authority an issuer is able to issue a credential.
For the purposes of this course, we will talk largely about self-sovereign identity (SSI)—it’s a big topic. Do remember as you consider the capabilities of this technology that it can be applied beyond person or organizational identity and into any domain where authentic data is important. The following are a few examples.

Many supply chain use cases can be handled using authentic data. As events occur to items being tracked (e.g., transferred from one carrier to another), verifiable credentials can be issued to capture the state of the item and event. Verifying the claims in the credential provides proof of that data later, demonstrating the provenance of the item. Food, shipping, diamonds, oil and gas and many, many more supply chains can be tracked this way.
Regulatory processes that are based on paper trails can be replaced with authentic data. Rather than relying on paper for documenting and manually verifying compliance, verifiable credentials could be generated from the source and digitally verified. Since the verifiable credentials are generated at the source and signed (immutable), they provide a reliable audit trail of events and actions.
The Government of British Columbia has demonstrated the use of authentic data in the handling of mine inspections in BC. Rather than a notebook being used for tracking mine inspections, the inspection data is digitally collected, recorded in a database and an inspection verifiable credential generated about the report that guarantees its authenticity. Should there be a need later to present the report (for example, in court), the inspection credentials can be proven, verifying the accuracy of the inspection report.
NOTE: In all of these cases, even though the use case is not centered on identity, there is an element of identity in each one. Specifically, in all cases, the identity of the credential issuer must be known to determine if they have the authority and capability to issue the credential. Often the holder must also be identified before being issued a credential. And, to complete the picture, a holder might want to know the identity of a verifier before presenting a proof.

Want to dig in a bit more to the ToIP stacks? Check out this powerful Interactive Model that the ToIP Foundation created to let you explore all of the layers of the ToIP model in both the technical and governance stacks and see some ToIP use cases in action.
