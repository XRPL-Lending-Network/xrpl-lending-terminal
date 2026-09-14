# Development Status

_Last updated: 2026-09-07._

## Network

Development of the XRPL Lending Terminal currently targets the
**XRPL Lending Devnet**, where the `SingleAssetVault` and `LendingProtocol`
amendments are enabled.

The Lending Protocol is **not available on XRPL Mainnet** at the time of
writing. Availability on Mainnet depends on the amendment process and is
outside the control of XRPL Lending Network. Nothing in this repository should
be read as a claim of Mainnet availability. The current amendment status can be
checked on the [XRPL known amendments](https://xrpl.org/resources/known-amendments) page.

## Specification status

| Specification | Status |
|---|---|
| [XLS-65 Single Asset Vault](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0065-single-asset-vault) | Draft |
| [XLS-66 Lending Protocol](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0066-lending-protocol) | Draft |

Because both specifications are Draft, ledger entry fields, flags, transaction
types and their semantics **may change**. The Terminal is developed against
the current implementation on the Lending Devnet, and this repository will be
updated when the public data model changes as a result.

## What is being built

The pipeline described in [architecture.md](architecture.md) and the coverage
listed in [protocol-coverage.md](protocol-coverage.md) are the current
development target. Progress notes will be added here as coverage on the
Lending Devnet is confirmed.

## What is public and what is private

**Public (this repository)**

- Architecture at the level of stages and boundaries
- Public data model and its mapping to ledger fields
- Protocol coverage
- Sanitized examples of the shape of vault, broker and loan data
- Diagrams

**Private**

- Production indexer, ingestion workers and storage
- Historical reconstruction and production normalization logic
- Analytics calculations
- Internal APIs, deployment and infrastructure
- Borrower, credit, underwriting, verification, prime market and Community
  Protection Fund components of the XRPL Lending Network product
