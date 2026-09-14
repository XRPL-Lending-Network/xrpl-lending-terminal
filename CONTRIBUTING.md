# Contributing

This repository is documentation and example data. There is no code to build
or test, so a contribution here is almost always a correction.

## What is useful

- A field name, flag or transaction type that does not match the ledger.
  Names in the docs and examples are meant to be exactly what `rippled` serves,
  such as `AssetsTotal`, `LoanBrokerCoverDeposit` or `lsfLoanImpaired`, and not
  paraphrases of them.
- A statement that contradicts XLS-65 or XLS-66. The specifications are the
  normative source; where the docs disagree with them, the docs are wrong.
- An example value that could not occur on the ledger, for instance an
  `AssetsAvailable` larger than `AssetsTotal`.
- Broken links and unclear wording.

## What does not belong here

The production Terminal is private. Feature requests for the Terminal, access
to its implementation, and questions about how its metrics are computed are out
of scope for this repository.

Problems with the Lending Protocol itself go upstream. XLS-65 and XLS-66 are
discussed in [XRPL-Standards](https://github.com/XRPLF/XRPL-Standards), and
`rippled` bugs belong in [its issue tracker](https://github.com/XRPLF/rippled/issues).

## Example data

Everything in `examples/` is synthetic, and it has to stay that way. Account
addresses follow the visibly fake pattern already in use, for example
`rVaultOwnerExampleXXXXXXXXXXXXXXXX`, and must never be real accounts. Ledger
object IDs are patterned hex values rather than IDs copied from a network.

The three files reference each other. A `VaultID` or `LoanBrokerID` changed in
one file has to change everywhere it appears.

The image in `assets/` is generated from these files. Please do not edit it by
hand; if a change to `examples/` affects it, mention that in the pull request
and a maintainer will regenerate it.

## Pull requests

Start the title with a type: `docs:` for documentation and examples, `fix:` for
a factual correction, `chore:` for anything else. One correction per pull
request is easiest to review.

Commits should be signed. GitHub's guide to
[commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification)
covers the setup.

## Security

Do not open public issues for security problems. See [SECURITY.md](SECURITY.md).
