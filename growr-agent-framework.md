# Growr Agent Framework

## Terms and Actors

### Self Sovereign Financial Identity - SSFI 
A decentralized digital identity with collection of verifiable credentials in its possession and control, based on W3C’s DID and VC standards, owned and managed by the user.

#### SSFI Holder
Physical individual or legal entity that is in rightful possession of SSFI and uses various VCs to access different financial growth services.

### Agents
All kind of actors that can provide, use or verify SSFI data.

#### Grower SSFI Agent dApp - GSA dApp or Custodial app - Front End
A wallet-like app that is operated by SSFI Holder and is used to build and operate the SSFI, get access to Growr services operated by different PGAs, get access to fair loans on Growr and to financial health building tools. (Peseta/Demo-SSFI-Agent). Local storage is used to store VCs and RSK DataVault for backup.

#### Public Growr Agent - PGA
Entities that have coherent API with the Growr API for one of the roles - Issuer, Verifier, Risk Assessor, etc. All those are build with the Growr Agent SDK. Their API is acknowledged, discoverable and accessible via the Growr Agent Gateway (GAG). They operate with SSFI data of the Holder. The PGA can have different Personas.

##### PGA Issuer
Issues credentials based on the Holder's data from different 3rd party sources.

#####  PGA Risk Assessor
Grants access to loan ponds based on SSFI VC data.

#### Individual Growr Agent - IGA
_Growr v........_
This kind of agents are entitled by SSFI Holders (GSA dApp users) and are used to access and operate SSFI by communicating with PGAs and on behalf of the SSFI Holder. They operate under their own identity, but controlled by the SSFI Holder by his GSA dApp.


### Growr Agent Gateway - GAG

A thin discovery service that is creating dynamic schema. Any Distributor can deploy a GAG and setup broker list for the IPs of the nodes of the PGAs that would be included in the Distributor's network. In that way, a Distributor can have his own App and add to the GAG only Issuers and Risk Assessors that he would like to use for his customer base.

Communication should be :
- GraphQL schema
	-  "stitched" schema - available through single endpoint - Different PGAs must have different names of queries/mutations/subscriptions. Maybe organized in Domains.
	OR
	-  single unified API with pathfinder functionality, based on PGA DID and/or VC types, pond addresses of those that the PGA is assigned to, etc.

This will allow service discovery using dynamic schema that won't require update at the user app for accessing new PGAs. as well as CRUD over request/response model and subscriptions for messaging or other event driven communication.
The GAG should be used by Growr Individual Agents and SSFI Agent/Wallets to collect and present VCs by Issuers and Risk Assessors, issue endorsement VCs.

### Schema repository & Trusted PGA and Core Protocol Registries
_schemas currently are in GitHub repository are included in Growr Agent SDK  as submodule_
_Trusted registries and Pond factories currently are in Environmental variables_
- GraphQL API for CRU operations with schemas available for acknowledged PGAs (Schemas s)
- Schema types registry of DID addresses and URIs of PGAs that operate with those schemas. (Issuers and Risk Assessors)
- Dynamic config loading for trusted protocol registries
- DID Authorization of the API access*
	All registries, since they should be public, will operate on chain, and the schema hosting is currently in Github, but they should be stored on decentralysed storage (IPFS or some other service)
 

## Building blocks

### MoleculerJS

https://moleculer.services/

Moleculer is a fast, modern and powerful microservices framework for [Node.js](https://nodejs.org/en/). It helps you to build efficient, reliable & scalable services. Moleculer provides many features for building and managing your microservices.

All PGAs, IGA, GAG and Schema & Trusted registries repository should be developed as MoleculerJS services.

All logging, tracing and audit logs gathering should be implemented with the Moleculer ready modules as much as possible.

### Growr Agent SDK

Common SDK library that is used to enable all DID and VC related operations in the different agents: IWA, IGA, PGA - Wrapper of RIF Identity/Veramo/Impervious/Whatnot
	- DID (Generate, Sign, Verify signature) 
	- VC (issue, verify, connect with schema library)
	- Decentralized Storage (login, store, retrieve) related functions
	- Communication with other Agents
		- * GraphQL + subscriptions 
		- Moleculer mesh over TCP will be used for communication on this Iteration of the framework
		- DIDComm will be added on the following iteration that will provide further decentralized model.

Should create and return new Agent which has identity, can issue and verify VCs, can access Ponds and Trust registries.
		
### Persona controller 
_create boilerplate for two personas: Issuer, Risk Assessor, with Interfaces_
The business logic that gives the agent "personality". Growr Agent SDK is used for all SSFI related operations. The controller, depending on the Persona, should: 
- Provide connection and authentication with reliable and adequate data sources for SSFI building (Issuer)
- Manage VC Issuing process according to GAG Schema (Issuer)
- Evaluate and validate SSFI holder credentials for the Pond the holder has requested. (Risk Assessor)
- Present minimum required data for 3rd party risk assessment, based on the Holder's SSFI. (Risk Assessor)
- Connect with Verification and Trust Registries on different chains. (both Personas)
- Follow API Interface specification by Growr

#### PGAs by persona

##### PGA Issuer boilerplate
- Growr Agent SDK
	- Identity
	- CreateRequest
	- IssueVC
- GraphQL server, exposing Issuer API schema (createRequest, issueVC)
- Data storage for VC request process
- Connection with trust registries to validate that SSFI Holder is not banned from service - _TBA_

##### PGA Risk Assessor boilerplate
- Growr Agent SDK
	- Identity
	- Web3
	- verifyCredentials
- GraphQL server, exposing Risk Assessor API schema
- Connection with Trust Registries to grant access to protocol or validate that SSFI Holder is not banned from service _TBA_

## Architecture diagram

Currently the communication between GSA dApp and the GAG is by GraphQL API. GraphQL allows for Schema discovery from client perspective and Schema Stitching from Server perspective to achieve flexibility and decentralization at some stage.
For future iterations IGA will be added, the GA SDK will be used in the GSA dApp. Also all communication between agents should be implemented in message pattern over DIDComm or other encrypted and secured channels. Schema repository and Trusted PGA registries should be implement as Growr Trust framework services. Core protocol will be extended further as well.

### Mid term non-custodial architecture

[diagram](./diagrams/growr-agent-framework-mid-stage-non-custodial.drawio.svg)


### Mid term custodial architecture

_TBA_

### Fully decentralised architecture

[diagram](./diagrams/growr-agent-framework-fully-decentralised.drawio.svg)
## Growr Agent SDK Specs
### External dependencies
* [rif-identity](https://github.com/rsksmart/rif-identity.js)
* @rsksmart/ethr-did
*  _TBA_
### SSFI Function interface
*  _TBA_

#### DID function interface
_@rsksmart/ethr-did wrapper_

* createIdentity(privateKey, networkName): DID
* getAccountFromDid(did): String (account) _vc-verifier/getAddressFromDid_
* verifyDid(nonce, msg): _check in vc-issuer/ecrecover.js_
* Create DID address (mnemonic) + createIdentity

#### VC function interface
* private verifyVerifiableJwt(jwt, ethSign): JWTVerified (jesse-did-jwt)
* private parseCredential
* getCredentials(did, vps): Credentials[]
* verifyCredentials(pondAddress, userCredentials): _check with Pond.verifyCredentials ABI_
* createRequest(did, type, subject)
* private createVC(did, subject, template): VC
* issueVC(did, subject, type, template): VC

### Web3 interface
#### Core Protocol function interface
* private Pond.verifyCredentials
* private Pond.getCriteriaNames
* private VerificationRegistry.registerVerification

#### Wallet operations
* private connectNetwork => provider, wallet 
