# loan
Run the test flows for all tasks with `daml start`.
## Design decisions and assumptions
### General
- When testing we try not to misuse `submitMulti` espcially for the core flows as it does not emulate real world multisig authorisation.
- For most contracts we tend to pass its `ContractId` as fields and arugments of templates and choices as they enfore lookups for the latest contract data when the choices are triggered. The only exception here is `Token` which is just represented by a pair of key values with no other data that can be updated, other than the fact that it can be archived. We therefore have the option of passing in its data or key values instead, as this would also allow us to enforce template level constraiants which a `ContractId` couldn't as it would first require a lookup within a choice. 
### Task 1
Pretty straight forward [Propose and Accept Pattern](https://docs.daml.com/daml/patterns/propose-accept.html).
### Task 2
- Non-account based.
- `Token` template represents the asset and `Holding` would be the equivalent of an UTXO of a token.
- Disbursement via minting is only restricted to tokens issued by the bank.
- All other tokens would require existing ownership of holdings of the particular token, i.e. the bank cannot mint these out of thin air.
- Missing limit implies no limit.
### Task 3
- Depends on `Tokens` from task 2 as the requirements for tokens and holdings did not change.
- Unlike `LoanLimit` which is created by the bank and only needs to be accessed by the bank during loan approvals, `RepaymentRestriction` is created by the bank and has to be accessed by borrowers during repayment. Consideration is therefore needed as to when and how individual borrowers can be included as observers.
- The remaining required repayment may fall below the min repayment restriction. In cases where the max payment option fall below the min repayment restriction we should allow repayment without restrictions.
- Missing restriction implies no restriction.
