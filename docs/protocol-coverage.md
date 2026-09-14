# Protocol Coverage

This page lists what the XRPL Lending Terminal is being developed to cover, and
how each protocol feature maps onto ledger objects, transactions and Terminal
concepts. It tracks the Draft specifications
[XLS-65](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0065-single-asset-vault)
and [XLS-66](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0066-lending-protocol).

Legend: **target** means part of the current development target. See
[development-status.md](development-status.md).

## Single Asset Vaults (XLS-65)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Vault state | `Vault` entry | Vault state, vault history | target |
| Vault creation and configuration | `VaultCreate`, `VaultSet`, `VaultDelete` | Vault lifecycle events | target |
| Deposits and withdrawals | `VaultDeposit`, `VaultWithdraw` | Depositor activity, liquidity changes | target |
| Issuer clawback | `VaultClawback` | Clawback events | target |
| Share tokens | `ShareMPTID`, `MPTokenIssuance` | Share supply, share holders | target |
| Private vaults | `lsfVaultPrivate`, permissioned domain on the share issuance | Vault access type | target |
| Unrealized loss | `LossUnrealized` | Vault loss exposure | target |

## Loan Brokers (XLS-66)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Broker state | `LoanBroker` entry | Broker state, broker history | target |
| Broker creation and configuration | `LoanBrokerSet`, `LoanBrokerDelete` | Broker lifecycle events | target |
| Debt tracking | `DebtTotal`, `DebtMaximum` | Outstanding debt, lending capacity | target |
| Management fees | `LoanBroker.ManagementFeeRate`, `Loan.ManagementFeeOutstanding` | Fee terms and fee balances | target |
| Loan count | `OwnerCount`, `LoanSequence` | Loan book size | target |

## First-loss capital / cover (XLS-66)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Cover balance | `CoverAvailable` | First-loss cover | target |
| Cover requirements | `CoverRateMinimum`, `CoverRateLiquidation` | Cover ratio, cover sufficiency | target |
| Cover deposits and withdrawals | `LoanBrokerCoverDeposit`, `LoanBrokerCoverWithdraw` | Cover activity | target |
| Cover clawback | `LoanBrokerCoverClawback` | Cover clawback events | target |
| Cover liquidation on default | `LoanManage` with default, resulting `LoanBroker` and `Vault` changes | Realized loss, cover consumed | target |

## Loans (XLS-66)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Loan state | `Loan` entry | Loan state, loan history | target |
| Loan origination | `LoanSet` | Origination events, principal transferred to the borrower | target |
| Loan terms | rate, fee and schedule fields on `Loan` | Loan terms | target |
| Outstanding balances | `PrincipalOutstanding`, `TotalValueOutstanding` | Outstanding debt per loan | target |
| Payment schedule | `StartDate`, `PaymentInterval`, `NextPaymentDueDate`, `PaymentRemaining`, `GracePeriod` | Schedule, time to due date | target |
| Loan closure | `LoanDelete` | Loan closed | target |

## Loan repayments (XLS-66)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Scheduled payment | `LoanPay` | Repayment activity | target |
| Late payment | `LoanPay` with `tfLoanLatePayment` | Late repayment | target |
| Early full repayment | `LoanPay` with `tfLoanFullPayment` | Early close | target |
| Overpayment | `LoanPay` with `tfLoanOverpayment`, `lsfLoanOverpayment` | Overpayment activity | target |

## Impairment and default state (XLS-66)

| Feature | Ledger source | Terminal concept | Status |
|---|---|---|---|
| Impairment | `LoanManage` with `tfLoanImpair`, `lsfLoanImpaired`, vault `LossUnrealized` | Impaired loans, unrealized loss | target |
| Un-impairment | `LoanManage` with `tfLoanUnimpair`, or automatic on payment | Impairment cleared | target |
| Default | `LoanManage` with `tfLoanDefault`, `lsfLoanDefault` | Defaulted loans, realized loss | target |

## Relevant lending transactions

All of the following are within the ingestion target:

`VaultCreate`, `VaultSet`, `VaultDelete`, `VaultDeposit`, `VaultWithdraw`,
`VaultClawback`, `LoanBrokerSet`, `LoanBrokerDelete`, `LoanBrokerCoverDeposit`,
`LoanBrokerCoverWithdraw`, `LoanBrokerCoverClawback`, `LoanSet`, `LoanDelete`,
`LoanManage`, `LoanPay`.

Transactions that indirectly affect lending objects (for example MPT
transactions on share tokens, or trust line and freeze changes on the vault
asset) are tracked to the extent needed to keep vault and share data correct.

## Out of scope for this repository

Borrower and credit analytics, underwriting, network verification, prime
market logic and the Community Protection Fund are separate parts of the
XRPL Lending Network product and are not described here.
