
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