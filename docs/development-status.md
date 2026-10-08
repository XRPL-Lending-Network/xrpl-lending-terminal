# Development Status

_Lifecycle documentation updated: 2026-10-08._

## Network

_Historical statements from the 2026-09-07 version of this document;
network activation was not revalidated for this documentation update._

The 2026-09-07 version described the development target as the
**XRPL Lending Devnet**, with the `SingleAssetVault` and `LendingProtocol`
amendments enabled. It also stated that the Lending Protocol was not
available on XRPL Mainnet at that time.

These historical statements do not establish current network availability
or activation of `LendingProtocolV1_1`. Availability depends on the amendment
process and is outside the control of XRPL Lending Network. Consult the
[XRPL known amendments](https://xrpl.org/resources/known-amendments) page
for amendment status and verify activation on the specific network before
relying on a feature.

## LendingProtocolV1_1 documentation scope

[xrpld 3.4.0](https://xrpl.org/blog/2026/xrpld-3.4.0) introduced
`LendingProtocolV1_1`, including closed-ended vault lifecycle support.
Availability on a particular network depends on amendment activation.

The public [data model](data-model.md) documents `VaultKind`,
`SubscriptionDate`, `RedemptionDate`, and the lifecycle phase derived
using the parent ledger close time. The corresponding entries in
[protocol coverage](protocol-coverage.md) remain marked as `target`.

This documentation update does not establish deployed Terminal support,
complete `LendingProtocolV1_1` compatibility, or a complete accounting
migration. Runtime coverage should be recorded here when it has been
verified on the target network.

## Specification status

| Specification | Status |
|---|---|
| [XLS-65 Single Asset Vault](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0065-single-asset-vault) | Draft |
| [XLS-66 Lending Protocol](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0066-lending-protocol) | Draft |

The specification references used for this lifecycle documentation update
are pinned to
[XRPL-Standards revision 7b89b23](https://github.com/XRPLF/XRPL-Standards/tree/7b89b2368f245fdc4e5ff090b58625b13b081f52),
where both specifications are Draft. Ledger entry fields, flags, transaction
types, and their semantics may change. The Draft labels above refer to
that revision. Future updates should check newer specification revisions
and the implementation running on the target network.

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
