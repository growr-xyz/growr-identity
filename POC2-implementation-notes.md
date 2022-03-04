
1. Use metamask - No need to create user wallet
2. Create DID Declarative details (like) in rif-identity-manager
3. Login to Peseta with rLogin
4. Request VCs from issuer with bank credentials
	1.  should use bank API to populate user [data](https://github.com/growr-xyz/growr-on-chain-backend/blob/dev/model/user.js) and create VC
5.  Request credentials verification to disburst loan. 

## Pesta Login with metamask

In react app: https://bit.ly/rlogin-sample-app-login
also here: https://github.com/rsksmart/rLogin-essentials


```mermaid
sequenceDiagram

participant User as User/Wallet
participant Peseta

User -->> Peseta: Click Login
Peseta -->> User: Pop up WalletConnect
User -->> User: Select wallet and address
User -->> Peseta: Send address
```


## Peseta Login with selective disclosure from App

Looks like we have option to request VCs out of the box with rLogin only with backend. For pond discovery we either move it after login and selective disclosure of the desired credentials or we have to find a way to select credentials we would like to present before the getOffer request from Peseta to SC.

```mermaid
sequenceDiagram

participant User as User/Wallet
participant Peseta
participant Backend
participant Data Vault
participant SCR as Smart Contract Registry

User ->> Peseta: Click Login
activate User
Peseta ->> User: Pop up WalletConnect
User ->> User: Select wallet and address
User -->> Peseta: Send address
Peseta -->> Backend: /request-sign-up (did)
Backend -->> Peseta: Chalange + selective disclosure (VCs)
alt if sdr
Peseta ->> User: Dislpay requested credentials
User ->> User: Accept sharing crednetials
User -->> Peseta: Signed Agreement
Peseta ->> Data Vault: request credentials
Data Vault ->> Peseta: credentials
end
Peseta ->> User: Request to sign SDR Presentation
User ->> User: Sign presentation
User ->> Peseta: send JWT
Backend ->> SCR: Add Verification record to the registry
Peseta ->> Backend: /sign-up jwt
Backend ->> Peseta: access and refresh tokens 
```

