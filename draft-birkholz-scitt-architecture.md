---
title: An Architecture for Trustworthy Digital Supply Chain Transparency Services
abbrev: SCITT Architecture
docname: draft-birkholz-scitt-architecture-latest
stand_alone: true
ipr: trust200902
area: Security
wg: TBD
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- ins: A. Delignat-Lavaud
  name: Antoine Delignat-Lavaud
  organization: Microsoft
  email: antdl@microsoft.com
  country: UK
- ins: C. Fournet
  name: Cedric Fournet
  organization: Microsoft
  email: fournet@microsoft.com
  country: UK

normative:
  RFC8152: COSE

informative:
  I-D.draft-birkholz-scitt-receipts:

--- abstract

A transparent and authentic ledger service in support of a supply chain's integrity, transparency, and trust requires all peers that contribute to the ledgers operations to be trustworthy and authentic.

In this document, the supply chain context is illustrated using problem statements, requirements are derived from use case definitions, and architectural constituents are specified and illustrated in usage scenarios.

The resulting architecture is intended to enable multi-layer interoperability to produce and leverage believable trust assertions while maintaining a minimal adoption threshold.

--- middle

# Introduction

The need for an understandable, scalable and resilient system that provides trustworthy transparency for various kinds of existing and emerging supply chains is a global one.
This memo specifies an architecture for Transparency Service (TS) to systematically protect supply chains for digital artifacts.

Supply Chain Integrity, Transparency and Trust (SCITT) involves two complementary security guarantees:

1. artifacts must be authenticated by their issuers; and
2. an artifact's release must be recorded in a secure, append-only ledger, so that their provenance and release history can be independently reviewed.

Transparency in the context of supply chains is always a well-scoped quality for each instance of a TS.
Transparency does not imply being transparent to everybody, unconditionally.
A TS always limits the entities that have the authority to release new claims to the TS.
Analogously, a TS typically limits the entities to which transparency into released claims is granted.
Nevertheless, it is of great import to provide global interoperability for a all TS instances as the composition and configuration of involved supply chain entities and their system components is ever changing and always in flux.

A TS provides visibility into claims produced by supply chain entities and their sub-systems.
These claims are called Digital Supply Chain Artifacts (DSCA, typically simply referred to as artifacts in the remainder of this memo).
More importantly, a TS vouches for specific and well-defined metadata about these artifacts, including "when was the artifact recorded by the TS", "who issued the artifact to the TS", or "what type of artifacts are stored in the TS".
In fact, a artifacts itself can be opaque to the TS, if so desired.
It is the metadata that must always be transparent and that must warrant trust. That metadata includes distinct details and believable trustworthiness characteristics about the distinguishable system components that compose TS instances as well as their operations involving artifacts.

These trustworthiness assertions provide an essential basis for holding issuers accountable for the artifacts they release and (more generally) principals accountable for the claims they make about such artifacts.

The TS specified in this architecture caters two types of audiences:

1. artifact issuers: entities, stakeholders, and users involved in supply chain interactions that need to release artifacts to a definable set of peers; and
2. artifact consumers: entities, stakeholders, and users involved in supply chain interactions that need to access, validate, and trust artifacts.

Artifacts issuers rely on being discoverable and represented as the responsible parties for released artifacts by the TS in a believable manner.
Analogously, artifact consumers rely on verifiable trustworthiness assertions associated with artifacts and their processing in a believable manner.
If trust can be put into the operations that record artifacts in a secure, append-only ledger via an online operation, the same trust can be put into a corresponding receipt that is the result of these online operations issued by the TS and that can be validated in offline operations.

The TS specified in this architecture can be implemented by various different types of services in various types of languages provided via various variants of API layouts.
The global interoperability enabled and guaranteed by the TS is enabled via core components (architectural constituents) that come with prescriptive requirements (that are typically hidden away from the user audience via APIs later).
The core components are based on the Concise Signing and Encryption standard specified in {{-COSE}}, which is used to sign released artifacts and to build and maintain a merkle tree that functions as the append-only ledger for artifacts.
The format and verification process for ledger-based transparency receipts are described in [Counter-Signing Receipts](https://ietf-scitt.github.io/draft-birkholz-scitt-receipts/draft-birkholz-scitt-receipts.html)

## Requirements Notation

{::boilerplate bcp14-tagged}

# Use Cases



# Terminology

The terms defined in this section have special meaning in the context of Supply Chain Integrity, Transparency, and Trust throughout this document. When used in text, the corresponding terms are capitalized. To ensure readability, only a core set of terms is included in this section.[^1]

[^1]: This list is a bucket today and subject to churn.
{: source="Henk"}

Artifact:

: the physical or non-physical thing that is moving along the supply chain.

Claim:

: a non-repudiable statement about an artifact encoded as a COSE payload provided by the issuer.

Envelope:

: a container that includes the metadata added to a Statement, making it a Claim. Effectively, a COSE envelope with headers, signature, and potentially the statement included as the COSE payload.

Feed:

: An identifier of an Artifact for a series of Transparent Claims. Effectively, a new COSE header attribute that contains a name given to the Artifact given by the Issuer.

Issuer:

: originator of named statements, signing statements, thereby refining them to a Claim, and issuing/sending Claims to the Transparency Service.

Ledger:

: a COSE-based merkle tree allowing for append-only operations to add Claims

Profile:

: tbd

Receipt:

: a COSE countersignature that is a proof of inclusion specific to a Claim in a ledger.

Transparent Claim:

: a Claim processed by the Transparency Service, added to the ledger, and augmented by a Receipt.

Transparency Service:

: the thing that operates on the ledger, including auditing and querying
via trusted operations

{: #mybody}

# High-level architecture

## Principals

### Issuers and claims

Claims are non-repudiable statements made by issuers. In SCITT, many claims are made by authors, reviewers and distributors of digital artifacts, including source and binary packages, firmware, audit reports, etc.

### Transparency services

 Operating the ledger, its verifiable data structure, plus auxiliary storage/index. We use ledger to refer to the current (final) contents registered at the transparency service.

### Verifiers
 including users, and anyone else

## What is transparency

- Claims can be issued and endorsed by principals (issuers).

- Signed Claims can be counter-signed by SCITT ledgers.

An artifact (e.g. a firmware binary) is transparent if it comes with valid transparent signed claims.

- The signature proves the artifact has been issued by the signer, with the associated metadata.

- The receipt proves this artifact and associated metadata has been recorded by the ledger.

Transparency does not prevent dishonest or compromised issuers, but it holds them accountable:
any artifact that may be used to target a particular user that checks for receipts must have been recorded in the tamper-proof ledger, and will be subject to scrutiny and auditing by other parties.

Anyone with access to the ledger can independenly verify the ledger consistency and review the list of claims registered by its issuers.

Reputable issuers are thus incentivized to carefully review their artifacts before signing them.

### Ledgers and Receipts

A ledger is a consistent, append-only, publicly available record of entries.

A receipt is an offline, universally-verifiable proof that an entry has been recorded in the ledger.

Receipts do not expire, but it is possible to append new entries that subsume older entries.


## Use cases (informative, briefly discussed or entirely omitted)

> ADL: probably mode to appendix - no application should be discussed in ID body
> The plan is to keep application details to this subsection.

- Public SBOM ledger
  > No need to separate firmware?.
  - from source code to binaries
  - can we keep track of the provenance of software artifacts?
  - can we share the cost of reviewing software across the industry?

- Confidential Computing
  - how can clients that connect to a CC service for the first time validate their attestation reports?
  - how to automatically patch a CC service? Trust, but audit later.
  - automated software updates
  - in particular for implementing SCITT services.

# SCITT Transparency Service

SCITT aims to support many different implementations of transparency ledgers, as long as they satisfy a set of requirements that aim to limit the trust that participants need to have in their operators. Unlike Certificate Transparency, we do not assume that the honestly of transparency services is enforced by external audit, as this approach does not scale to the large amount of instances necessary to protect the software supply chain at scale.

## Service Identity and Keying

> Details TBD, e.g. discovery, rekeying, revocation, delegation (e.g. to replicas); this may  require its own subsection, or left underspecified as ledger-specific details.

## Receipt Issuance

Compact, universally verifiable proof that claims are registered in the ledger.

Enabling offline verification of registration in the ledger. Signed with key that chains to service identity

## Ledger Security Requirements

The main requirements for the transparency service are

### Attestability of service identity

Enabling remote authentication of the hardware platforms and software TCB that run the transparency service.

  Hardware attestation report, binding a public key for receipt verification to the long-term transparency service identity.

  RATS? proof-of-work?

### Consistency
Everyone with access to the ledger sees the same sequence of registered entries.

### Finality
Ledger is append-only; once registered, its entries cannot be modified or deleted (although they may be superceded by nwer entries).

### Replayability

> Can it cover client authentication and DID resolution?

### Auditing

### Governance and Bootstrapping

  > Their specification is out of scope: we rely on ledger-specific, out-of-band protocols for it.

## Caching / Query service

Untrusted. Replicated. Indexed.

> This service could extract (read) receipts from the ledger.

# SCITT COSE Envelopes

**Format of signed claims / envelope**

> Some headers that may be missing:
  - feed (enabling separation of ids for issuers and artifacts)#
  - svn (enabling versioning and rollback protection)
  - cty (contents type/format descriptor)
  - timestamp or serial number

>  Envelopes MAY include additional protected and unprotected headers.

## Issuer Identity

SCITT issuers are identified using DID, which provides a flexible, decentralized identity framework.

MUST support at least did:web for bootstrapping identities from domain ownership via https certificates.

The transparency service MUST resolve the issuer DID before registering their artifacts.

The transparency service SHOULD publicly record a transcript of the DID resolution at the time of registration.

The service can cache and re-use DID resolution.
- Evidence capture?
- What does it mean in terms of transcript?

> The rest is based on an earlier syntactic spec.

Digital supply chain artifacts are heterogeneous and originated from sources using various formats and encodings Large scale ledger services in support of supply chain authenticity and transparency require a simply and well-supported signing envelope that is easy to use and interoperates with the semantic of the ledger services. In this document, a COSE profile is defined that limits the potential use of a COSE envelope to the requirements of such a supply chain ledger, leveraging solutions and principles from the Concise Signing and Encryption (COSE) space.

### Why?

COSE is generic, every application needs to constrain it to their own use cases, in this case the SCITT use cases.

##  SCITT COSE profile


[TODO] describe semantics of `iss` and how it can be used together with `kid` to identity a verification method in a resolved DID document

A SCITT COSE message is a tagged COSE_Sign1 message.

The protected header must contain the following registered parameters:

- alg (label: `1`): Asymmetric signature algorithm as integer, for example `-35` for ECDSA with SHA-384, see [COSE Algorithms registry](https://www.iana.org/assignments/cose/cose.xhtml)
- payload type (label: `3`): Media type of payload as string, for example `application/spdx+json`
- issuer (label: `TBD`, to be registered): DID (Decentralized Identifier, see [W3C Candidate Recommendation](https://www.w3.org/TR/did-core/)) of the signer as string, for example `did:web:example.com`

Depending on the DID method the protected header must contain one or more of the following registered parameters:

- kid (label: `4`): Key identifier as a UTF-8 string encoded as a byte string, required for `did:web`
- x5c (label: `33`): X.509 certificate chain (including Root CA certificate), required for TBD


The unprotected header may contain the following parameters:

- receipts (label: `TBD`, to be registered): Array of SCITT receipts, defined in TBD

In CDDL ([RFC 8610](https://datatracker.ietf.org/doc/html/rfc8610)) notation, the envelope is defined as follows:

```
SCITT_Envelope = COSE_Sign1_Tagged

COSE_Sign1_Tagged = #6.18(COSE_Sign1)

COSE_Sign1 = [
  protected : bstr .cbor Protected_Header,
  unprotected : Unprotected_Header,
  payload : bstr,
  signature : bstr
]

Protected_Header = {
  ; mandatory
  1 => int               ; algorithm identifier
  3 => tstr              ; payload type
  258 => tstr            ; DID of issuer

  ; mandatory depending on DID method
  ? 4 => bstr            ; key identifier
  ? 33 => COSE_X509      ; X.509 certificate chain, excluding Root CA certificate

  ; optional experimental parameters
  ? -65537 => tstr       ; Feed
  ? -65538 => uint       ; SVN
}

Unprotected_Header = {
   ? 259 => SCITT_Receipt / [+ SCITT_Receipt]
}

COSE_X509 = bstr / [ 2*certs: bstr ]
```

# Protocols

## Issuing Signed Claims about an Artifact.

Issuance itself is just signing with a key currently associated with the issuer DID.

### SCITT is agnostic to claim types and formats (?)

> Are we going to specify formats for envelope payloads, aka "sets of claims" ? How are the types and formats below authenticated? We considered a "cty" protected header for that purpose.

Claim types include:
- SBOMs
- Malware scans
- Human audits
- Policies (= parameterized claims, with subject as input parameter)

Claim formats include:
- JSON-SPDX
- CBOR-SPDX
- SWID
- CoSWID
- CycloneDX

## Registering Signed Claims.

The same signed envelope can be independently registered in multiple ledgers.

Registation steps in the ledger

1. Client authentication.

   So far, implementation-specific, and unrelated to the issuer identity.

2. Issuer identification: binding the claimed issuer identity to their envelope signing key. See resolution below.

2. Envelope validation.

   This involves verifying the headers, but not the payload.
3. Envelope signature verification.
4. Commit to the ledger.
5. Issue receipt.

   This ordering matters, so that the ledger can back up any receipt.

## Verifying Transparent Signed Claims

Trusted input for receipt verification: the identity
and the public signature-verification key of the transparency service.

These may be included in the verifier's trusted configuration, or determined by a trusted policy.

Verification steps:

1. Verify receipt (see other draft)
2. Verify issuer signature.
3. Freshness/revocation?
4. Validate format of the envelope contents.

> Steps 2 and 3 are still TBD.

Once verified, the claims together with their authenticated issuer and transparent ledger identities can be used as input to an authorization policy.

# Federation

We explain how multiple, independent transparency services can be composed to distribute supply chains without a single transparency authority trusted by all parties.

> Mostly out of scope for the first drafts? We should make sure our architecture supports it.

Multiple SCITT instances, governed and operated by different organizations.

For example,
- a small, simple SCITT instance may keep track specifically of the software used for operating SCITT services.
- an air-gapped data center may operate its own SCITT ledger to retain full control and auditing of its software supplies.

How?
- Policy-based. Within an organization, local verifiers contact an authoritative SCITT that records the latest policies associated with classes of artifacts; these policies indicate which issuers and ledgers are trusted for verifying transparent signed claims for these artifacts.

- Other federation mechanisms?

We'd like to attach multiple receipts to the same signed claims, each receipt endorsing the issuer signature and a subset of prior receipts. This involves down-stream ledgers verifying and recording these receipts before issuing their own receipts.


# SCITT REST API

> We may omit most of the details, or put it in an appendix.

## Messages


### Register Signed Claims

#### Request

~~~
POST <Base URL>/entries
~~~

Body: SCITT COSE_Sign1 message

#### Response

One of the following:

- HTTP Status `201` - Registration was tentatively successful pending service consensus.
- HTTP Status `400` - Registration was unsuccessful.
  - Error code `AwaitingDIDResolutionTryLater`
  - Error code `InvalidInput`

[TODO] Use 5xx for AwaitingDIDResolutionTryLater

The `201` response contains the `x-ms-ccf-transaction-id`  HTTP header which can be used to retrieve the Registration Receipt with the given transaction ID. [TODO] this has to be made generic

[TODO] probably a bad idea to define a new header, or is it ok? can we register a new one? https://www.iana.org/assignments/http-fields/http-fields.xhtml

The `400` response has a `Content-Type: application/json` header and a body containing details about the error:

```json
{
  "error": {
    "code": "<error code>",
    "message": "<message>"
  }
}
```

`AwaitingDIDResolutionTryLater` means the service does not have an up-to-date DID document of the DID referenced in the Signed Claims but is performing or will perform a DID resolution after which the client may retry the request. The response may contain the HTTP header `Retry-After` to inform the client about the expected wait time.

`InvalidInput` means either the Signed Claims message is syntactically malformed, violates the signing profile (e.g. signing algorithm), or has an invalid signature relative to the currently resolved DID document.

### Retrieve Registration Receipt

#### Request

~~~
GET <Base URL>/entries/<Transaction ID>/receipt
~~~

#### Response

One of the following:

- HTTP Status `200` - Registration was successful and the receipt is returned.
- HTTP Status `400` - Transaction exists but does not correspond to a Registration Request.
  - Error code `TransactionMismatch`
- HTTP Status `404` - Transaction is pending, unknown, or invalid.
  - Error code `TransactionPendingOrUnknown`
  - Error code `TransactionInvalid`

The `200` response contains the SCITT_Receipt in the body.

The `400` and `404` responses return the error details as described earlier.

The retrieved receipt may be embedded in the corresponding COSE_Sign1 document in the unprotected header, see TBD.

[TODO] There's also the `GET <Base URL>/entries/<Transaction ID>` endpoint which returns the submitted COSE_Sign1 with the receipt already embedded. Is this useful?


# Privacy Considerations

Privacy Considerations

# Security Considerations

Security Considerations

# IANA Considerations

See Body {{mybody}}.

--- back

# Attic

Not ready to throw these texts into the trash bin yet.
<<<<<<< HEAD
<<<<<<< HEAD



=======
>>>>>>> first paragaphs for next meeting, more to come before then
=======
>>>>>>> d5d14210d810abc27f9183ea695a2e5c51f775d0
