# Architecture

This page describes the XRPL Lending Terminal at the level of stages and the
boundaries between them. It does not describe how any stage is implemented.
The production indexer, analytics engine and internal APIs are private.

## Flow

```
XRP Ledger
  → Single Asset Vaults / Loan Brokers / Loans / Transactions
    → Data Ingestion
      → Normalization
        → Market State
          → Analytics
            → XRPL Lending Terminal
```

## Stages

### 1. XRP Ledger

The source of truth. The Lending Protocol adds three ledger entry types:

| Ledger entry | Specification | Role |
|---|---|---|
| `Vault` | XLS-65 | Pools a single asset from many depositors. Issues share tokens (MPT) from a pseudo-account. |
| `LoanBroker` | XLS-66 | Draws on a vault to fund loans. Tracks total debt owed back to the vault and holds first-loss cover. |
| `Loan` | XLS-66 | A fixed-term loan agreement between a broker and a borrower, with its schedule and outstanding balances. |

Objects change only through transactions. The relevant transaction families are
`Vault*` (create, set, delete, deposit, withdraw, clawback), `LoanBroker*`
(set, delete, cover deposit, cover withdraw, cover clawback) and `Loan*`
(set, delete, manage, pay). Transaction metadata records exactly which ledger
entries each transaction created, modified or deleted.

### 2. Data Ingestion

Reads validated ledgers and extracts the lending objects and transactions they
contain.

- Input: validated ledger data from XRP Ledger nodes.
- Output: raw Vault, LoanBroker and Loan entries and the transactions, with
  metadata, that touched them.
- Boundary: ingestion does not interpret the data. It only guarantees that the
  Terminal sees every relevant ledger change, in order, without gaps.

### 3. Normalization

Maps raw ledger representations onto a stable lending data model.

- Converts ledger encodings (Ripple epoch timestamps, rates expressed in tenths
  of a basis point, `Number` amounts, hex `Data` blobs, MPT identifiers) into
  the units the Terminal uses.
- Assigns consistent identifiers so a vault, broker or loan is the same entity
  across ledgers.
- Classifies transactions into lending events (deposit, loan origination, repayment,
  cover deposit, impairment, default, and so on).
- Boundary: normalization changes representation, not meaning. Anything that
  cannot be derived directly from ledger data belongs to Analytics.

The data model is described in [data-model.md](data-model.md).

### 4. Market State

Holds the current state of every vault, broker and loan, together with the
history of how each reached that state.

- Answers "what does this object look like now" and "what did it look like at
  ledger N".
- Links objects to each other: loans to their broker, brokers to their vault,
  vaults to their share token.
- Boundary: market state contains only normalized ledger facts. It contains no
  derived metrics.

### 5. Analytics

Computes derived metrics and aggregates on top of market state.

- Per-object metrics such as utilization, lending capacity and cover ratios.
- Time series of how those metrics evolved.
- Aggregates across vaults, brokers, assets and the protocol as a whole.
- Boundary: analytics never writes back into market state. Every derived value
  is traceable to the ledger facts it was computed from.

The categories of metrics are listed in the README. The calculations
themselves are part of the private implementation.

### 6. XRPL Lending Terminal

Presents market state and analytics to users, and feeds the XRPL Lending
Network market interface.

```
XRP Ledger Lending Protocol  →  XRPL Lending Terminal  →  XRPL Lending Network market interface
```

## Design principles

- **Ledger first.** Every value shown by the Terminal is either a ledger fact
  or is derived from ledger facts in a documented way. There is no off-ledger
  source of protocol state.
- **Direct data and derived metrics are kept apart.** Users can always tell
  whether a number came from the ledger or was computed by the Terminal.
- **Track the specification.** XLS-65 and XLS-66 are Draft standards. The
  normalization layer exists partly so that specification changes are absorbed
  in one place rather than across the whole system.
- **Devnet first.** Development targets the XRPL Lending Devnet until the
  protocol is available elsewhere. See [development-status.md](development-status.md).
