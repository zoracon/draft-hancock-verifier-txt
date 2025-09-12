---
title: "A File Format to Assist Verifier Accountability"
abbrev: "Verifier.txt File Format"
category: info

docname: draft-hancock-verifier-txt-latest-latest-latest
submissiontype: "independent"
v: 3
area: AREA
workgroup: 
keyword:
 - digital id

author:
 -
    fullname: Alexis Hancock
    organization: Electronic Frontier Foundation
    email: alexis@eff.org
...

--- abstract

This document proposes a standard file format, `verifier.txt`, for digital identity verifiers to declare themselves, purpose for verification, data handling policies, and commitment to privacy-preserving practices. Inspired by `robots.txt` and `security.txt`, this file aims to provide transparency and establish a baseline of trust for individuals presenting digital credentials and for wallet applications or browsers mediating these interactions. The goal is to address concerns regarding verifier abuse in the digital ID landscape.

--- middle

# Introduction

The increasing adoption of digital identification, such as Mobile Driver's Licenses (mDLs), presents significant privacy and equity challenges. Verifiers (relying parties) can request personal information without clear policies on what data is collected, how it's stored, or if it's shared, leading to potential surveillance and misuse.

To foster trust and accountability, mechanisms are needed to ensure verifiers clearly communicate their practices. This RFC proposes `verifier.txt` as a machine-readable and human-readable standard that digital identity verifiers can publish to make their policies explicit, enabling informed consent from users and facilitating automated checks by wallet applications and browsers.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

- Verifier: An entity (e.g., website, application, physical establishment) that requests and verifies digital identity credentials from a Holder
- Holder: An individual who possesses and presents a digital identity credential
- Issuer: An entity (e.g., government agency, employer) that issues digital identity credentials
- Digital ID: A digital representation of an identity credential, such as an mDL or JWT
- Selective Disclosure (SD): The ability for a Holder to choose which specific pieces of information from a credential to reveal to a Verifier
- Zero-Knowledge Proof (ZKP): A cryptographic method allowing a Holder to prove possession of an attribute (e.g., "age over 18") without revealing any other information, including the exact attribute value (e.g., date of birth)
- Unlinkability: The property that prevents a Verifier from linking multiple presentations from the same Holder, and ideally prevents collusion between Issuers and Verifiers from tracking a Holder across sessions.

# The `verifier.txt` File Format

The `verifier.txt` file is a plain text file encoded in UTF-8, consisting of "Field: Value" pairs, with each pair on a new line. Comments can be indicated by a hash symbol (#) at the beginning of a line. Fields are case-insensitive.

# Required Fields

The following fields MUST be present in a `verifier.txt` file:

## Verifier-Name

`Verifier-Name` contains the official, legal name of the Verifier entity.

Example:

- `Verifier-Name: Example Retail Inc.`
    
## Contact

`Contact` contains an email address or URL for inquiries, support, and abuse reporting. This should be actively monitored.

Examples:

- `Contact: mailto:privacy@example.com or Contact: https://example.com/privacy-inquiries`
    
## Purpose-of-Verification

`Purpose-of-Verification` contains a clear and concise statement describing the explicit purpose for requesting digital ID information. This helps prevent "mission creep" and ensures transparency about why data is needed

Examples:

- `Purpose-of-Verification: To verify age for access to adult content.`
- `Purpose-of-Verification: To confirm identity for account creation and legal compliance.`
- `Purpose-of-Verification: To verify eligibility for employer-provided benefits.`
        
## Requested-Attributes

`Requested-Attributes` contains a comma-separated list of the specific attributes the Verifier may request. Each attribute SHOULD be accompanied by a notation indicating whether it is requested via Selective Disclosure (SD), Zero-Knowledge Proof (ZKP), or as a Full-Disclosure (FD) value (plain text). 

Examples:

- `Requested-Attributes: age_over_21 (ZKP), family_name (SD), email_domain (SD), driving_privileges (FD)`
- `Requested-Attributes: age_over_18 (ZKP)`
        
## Data-Retention-Policy

`Data-Retention-Policy` contains a URL linking to the Verifier's comprehensive data retention policy, or a brief summary of how long collected data is kept and for what purpose. This addresses the concern that verifiers might store collected information indefinitely

Examples:

- `Data-Retention-Policy: https://example.com/privacy#data-retention`

Summary Examples:

- `Data-Retention-Policy: Age verification logs retained for 90 days for audit purposes. No explicit personal identifiers stored.`
    
## Data-Sharing-Policy

`Data-Sharing-Policy` contains a URL linking to the Verifier's comprehensive data sharing policy, or a brief summary of whether and how collected data is shared with third parties (e.g., data brokers, other businesses, government agencies). This directly confronts the risk of data pooling and sale of user dossiers

Examples:

- `Data-Sharing-Policy: https://example.com/privacy#data-sharing`

Summary Example:

- `Data-Sharing-Policy: No personal information shared with third parties for marketing. Required data may be shared with regulatory bodies for compliance audits.`
    
## Unlinkability-Support

`Unlinkability-Support` contains a statement declaring the Verifier's commitment to supporting and enforcing unlinkable presentations, explicitly stating that presentations will not be used to track users across sessions or collude with Issuers.
This is critical for preventing pervasive tracking

Example:

- `Unlinkability-Support: This verifier supports unlinkable presentations and will not attempt to link user sessions.`
    
## Abuse-Reporting-Mechanism

`Abuse-Reporting-Mechanism` contains a URL or email address specifically dedicated to reporting misuse or abuse of digital ID requests by the Verifier

Example:

- `Abuse-Reporting-Mechanism: mailto:abuse@example.com`
  
# Optional Fields

The following fields are OPTIONAL but RECOMMENDED:

## Verifier-ID-Registry

`Verifier-ID-Registry` contains a URL pointing to the Verifier's entry in a recognized Verifier Registry if their jurisdiction mandated a registry.

Examples:

- `Verifier-ID-Registry: https://verifier-registry.example.org/id/example-retail`
  
## Auditing-Policy

`Auditing-Policy` contains a URL to details about any independent audits performed on the Verifier's data handling and privacy compliance.

Example:

- `Auditing-Policy: https://example.com/security#audits`
  
## Privacy-Preserving-Methods

`Privacy-Preserving-Methods` contains a comma-separated list of specific privacy-enhancing technologies (e.g., ZKP systems, homomorphic encryption) used in the verification process

Example:

- `Privacy-Preserving-Methods: Groth16 ZKPs, Pedersen Commitments`
  
## Data-Minimization-Statement

`Data-Minimization-Statement` contains a statement asserting the Verifier's commitment to the principle of data minimization, collecting only the minimum necessary information required for the stated purpose

Example: 

- `Data-Minimization-Statement: We adhere strictly to data minimization principles, requesting only essential attributes.`
  
## Consent-Mechanism

`Consent-Mechanism` contains a description or URL detailing how user consent for data presentation and processing is obtained and managed.

Example:

- `Consent-Mechanism: Explicit user consent obtained via in-app prompt and digital wallet interface before data presentation.`
  
# Placement and Access

The `verifier.txt` file MUST be placed in the / (root) directory of the Verifier's web server. It SHOULD also be accessible via the `.well-known` URI for greater discoverability, e.g., `https://example.com/.well-known/verifier.txt`.

# Processing Rules

Wallet applications, browsers, and other client-side agents SHOULD attempt to retrieve `verifier.txt` from the domain associated with the Verifier. If the file is found, its declarations SHOULD be presented to the user in a clear, understandable manner (e.g., within the digital wallet interface) before a credential presentation is initiated. Clients SHOULD flag or prevent interactions with Verifiers that fail to provide a `verifier.txt` file or whose policies contradict user preferences or best practices.
    
# Security Considerations

Authenticity: The authenticity of `verifier.txt` relies on the security of the Verifier's web server. Compromise of the server could lead to a malicious `verifier.txt` being served. Future iterations could explore digital signing of the `verifier.txt` file.

Spoofing: A malicious actor could attempt to spoof a `verifier.txt` file on their own domain to mislead users. This risk is mitigated by tying the `verifier.txt` to the actual domain of the relying party requesting the credential.

Completeness: While `verifier.txt` aims for transparency, it cannot guarantee that a malicious Verifier will adhere to its stated policies. It serves as a declaration of intent and a basis for accountability.

# Privacy Considerations

`verifier.txt` enhances privacy by providing transparency about Verifier practices, allowing users to make informed decisions about sharing their data.

- It promotes data minimization and unlinkability, directly addressing major privacy concerns with digital ID systems
- It does not, however, mitigate all privacy risks, such as the collection of other observable Personally Identifiable Information (PII) like IP addresses, or potential for browser fingerprinting
- These issues require broader regulatory solutions.
- The mere existence of a record in a Verifier’s database, even without explicit personal identifiers, can carry privacy implications (e.g., a record of viewing age-restricted content)
- `Data-Retention-Policy` and `Data-Sharing-Policy` fields are crucial here.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
