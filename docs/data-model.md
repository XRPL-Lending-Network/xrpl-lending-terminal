# Data Model

This page describes the public shape of the lending data the Terminal works
with. It is organised around the three protocol entities and the events that
change them. Field names below are the ledger field names from XLS-65 and
XLS-66 so that the mapping to the ledger stays obvious.

Two rules apply throughout:

1. **Direct ledger data** is read from ledger entries and transactions. The
   Terminal structures it but does not change its meaning.
2. **Derived metrics** are computed by the Terminal and are always labelled as
   such. Their definitions are not part of this public repository.

Sanitized examples of the general shape are in [../examples/](../examples/).

## Entities

### Vault (XLS-65)

One vault holds one asset (XRP, a trust line token, or an MPT) on behalf of
many depositors. Depositors receive share tokens issued by the vault's
pseudo-account.

**Direct ledger data**

| Ledger field | Meaning |
|---|---|
| `Owner` | Account that created and controls the vault |
| `Account` | Vault pseudo-account that holds the assets and issues shares |
| `Asset` | The vault's single asset |
| `AssetsTotal` | Total value of the vault, including assets currently lent out |
| `AssetsAvailable` | Assets currently held and available for withdrawal |
| `AssetsMaximum` | Deposit cap (0 means no cap) |
| `LossUnrealized` | Potential loss registered through loan impairment, not yet realized |
| `ShareMPTID` | Identifier of the share token issuance |
| `WithdrawalPolicy` | Withdrawal strategy used by the vault |
| `Scale` | Decimal scaling between asset and shares |
| `Data` | Optional owner-supplied metadata (up to 256 bytes) |
| `Flags` | `lsfVaultPrivate` marks a private, credential-gated vault |

**Derived by the Terminal (examples)**

- Assets deployed in loans
- Utilization
- Remaining deposit capacity
- Change history of the above

### Loan Broker (XLS-66)

A broker is linked to exactly one vault, borrows from it to fund loans, and
optionally posts first-loss capital ("cover") that absorbs losses before
depositors do.

**Direct ledger data**

| Ledger field | Meaning |
|---|---|
| `Owner` | Account that operates the broker |
| `Account` | Broker pseudo-account |
| `VaultID` | The vault the broker draws on |
| `DebtTotal` | Principal plus interest the broker currently owes the vault |
| `DebtMaximum` | Debt ceiling (0 means unlimited) |
| `CoverAvailable` | First-loss capital currently deposited |
| `CoverRateMinimum` | Required cover as a fraction of debt |
| `CoverRateLiquidation` | Fraction of required cover liquidated per default |
| `ManagementFeeRate` | Broker's fee on loan interest |
| `OwnerCount` | Number of Loan objects held by the broker |
| `LoanSequence` | Counter used to number the broker's loans |
| `Data` | Optional operator-supplied metadata |

**Derived by the Terminal (examples)**

- Remaining lending capacity under `DebtMaximum` and vault liquidity
- Cover ratio versus `CoverRateMinimum`
- Number and value of loans by status
- Change history of the above

### Loan (XLS-66)

A fixed-term loan between a broker and a borrower with a fixed payment
schedule.

**Direct ledger data**

| Ledger field | Meaning |
|---|---|
| `LoanBrokerID` | The broker that issued the loan |
| `Borrower` | Borrowing account |
| `LoanSequence` | Loan number within the broker |
| `PrincipalOutstanding` | Principal still owed |
| `TotalValueOutstanding` | Remaining scheduled principal, interest and management fee |
| `ManagementFeeOutstanding` | Broker fee still owed |
| `PeriodicPayment` | Amount due each interval |
| `InterestRate`, `LateInterestRate`, `CloseInterestRate`, `OverpaymentInterestRate` | Rate terms |
| `LoanOriginationFee`, `LoanServiceFee`, `LatePaymentFee`, `ClosePaymentFee`, `OverpaymentFee` | Fee terms |
| `StartDate`, `PaymentInterval`, `GracePeriod` | Schedule terms |
| `PreviousPaymentDueDate`, `NextPaymentDueDate`, `PaymentRemaining` | Schedule position |
| `LoanScale` | Rounding precision |
| `Flags` | `lsfLoanImpaired`, `lsfLoanDefault`, `lsfLoanOverpayment` |

**Derived by the Terminal (examples)**

- Loan status combining flags, schedule and balances. Three things are kept
  apart: **overdue** means `NextPaymentDueDate` has passed without payment;
  **default eligible** means the `GracePeriod` after it has also passed;
  **impaired** and **defaulted** are ledger flags set through `LoanManage`.
  Time passing alone never sets a flag, so a loan can be overdue with no flags.
- Time to next payment and to default eligibility
- Payment history and on-time versus late behaviour
- Remaining schedule value

## Events

Every change to an entity is caused by a transaction. The Terminal classifies
lending transactions into events and links each event to the entities it
affected.

| Transaction | Event | Affects |
|---|---|---|
| `VaultCreate` | vault created | Vault |
| `VaultSet` | vault updated | Vault |
| `VaultDelete` | vault deleted | Vault |
| `VaultDeposit` | deposit | Vault, share holder |
| `VaultWithdraw` | withdrawal | Vault, share holder |
| `VaultClawback` | clawback by issuer | Vault, share holder |
| `LoanBrokerSet` | broker created or updated | Loan Broker |
| `LoanBrokerDelete` | broker deleted | Loan Broker |
| `LoanBrokerCoverDeposit` | cover deposited | Loan Broker |
| `LoanBrokerCoverWithdraw` | cover withdrawn | Loan Broker |
| `LoanBrokerCoverClawback` | cover clawed back by issuer | Loan Broker |
| `LoanSet` | loan originated, principal transferred from the vault to the borrower | Loan, Loan Broker, Vault |
| `LoanPay` | repayment (scheduled, late, full early, or overpayment) | Loan, Loan Broker, Vault |
| `LoanManage` | impairment set or cleared, or default | Loan, Loan Broker, Vault |
| `LoanDelete` | loan closed after full repayment or default | Loan, Loan Broker |

Transaction metadata (`AffectedNodes`) is the authoritative record of what
each event changed. Normalization uses it rather than re-deriving effects.

## Relationships

```
Vault 1 ──< LoanBroker 1 ──< Loan
  │
  └── share token (MPTokenIssuance) ──< share holders
```

- A vault can fund many brokers. A broker draws on exactly one vault.
- A broker can issue many loans. A loan belongs to exactly one broker.
- A vault has exactly one share token issuance.

## Units and encodings

Ledger encodings the Terminal normalizes:

| Ledger representation | Terminal representation |
|---|---|
| Rates in tenths of a basis point (`InterestRate`, `CoverRateMinimum`, ...) | Decimal fractions (`7000` becomes `0.07`, shown as 7%) |
| Timestamps in seconds since the Ripple epoch (2000-01-01) | UTC timestamps |
| `Number` amounts as strings | Decimal amounts in the vault's asset |
| `Data` as hex | Decoded metadata where it is valid UTF-8 |
| `ShareMPTID` | Linked share token issuance |

The ledger omits fields whose value equals their default. The Terminal treats
a missing numeric field as its default rather than as unknown, and parses
`Number` amounts exactly.

## What is not in this model

The public model stops at protocol entities, events and the categories of
derived metrics. It does not include the Terminal's internal identifiers,
storage layout, metric definitions, or any borrower or credit analytics.
