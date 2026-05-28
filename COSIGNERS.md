# Cosigners

This repository's cosignature shape is different from the
`COSIGNERS.md` in the sibling
[`atx-conformance`](https://github.com/opena2a-standards/atx-conformance)
and
[`atp-conformance`](https://github.com/opena2a-org/atp-conformance)
repos, because v0.1 does not ship fixtures (see the README's *Resolution
(Decision 3, 2026-05-27)* section for why). The cosignature target
evolves between v0.1 and v0.2:

## v0.1 (current) — cross-link attestation

At v0.1 a cosigner attests to the **cross-link claim**: that AIP §6.4
(VC `AgentTrustCredential`) conformance is correctly delegated to
`atx-conformance`. There is no `MANIFEST.sha256` yet because there are
no fixtures yet.

To cosign v0.1:

1. Read this repository's `README.md` end-to-end.
2. Clone [`atx-conformance`](https://github.com/opena2a-standards/atx-conformance)
   at the commit referenced in its `MANIFEST.sha256` and run both reference
   verifiers (`8 pass, 0 fail` from each). This step attests that the
   §6.4 fixtures the cross-link points at actually verify.
3. Produce a Sigstore keyless cosign signature over a tarball of this
   repository's tree state (`README.md`, `LICENSE`, `COSIGNERS.md`) at the
   commit being cosigned. The signature attests that the cross-link claim
   was read and accepted at a specific commit.

```bash
# Clone this repo
git clone https://github.com/opena2a-org/aip-conformance
cd aip-conformance

# Independently verify the cross-link target
git clone https://github.com/opena2a-standards/atx-conformance ../atx-conformance
(cd ../atx-conformance/verifiers/go && go run . ../../fixtures)
(cd ../atx-conformance/verifiers/python && pip install -r requirements.txt && python verify.py ../../fixtures)

# Build the v0.1 cosignature tarball
git archive --format=tar.gz --output=aip-conformance-v0.1-tree.tar.gz HEAD

# Sigstore keyless cosign over the tarball
cosign sign-blob aip-conformance-v0.1-tree.tar.gz \
    --output-signature aip-conformance-v0.1-tree.tar.gz.sig \
    --output-certificate aip-conformance-v0.1-tree.tar.gz.crt

# Open a PR that:
#   - Adds your cosignature + certificate under .sigstore/<your-org>/
#   - Appends an entry to the v0.1 registry below
```

## v0.2 (in flight) — `MANIFEST.sha256` attestation

When v0.2 lands with §5.1 challenge-response fixtures + Go/Python
verifiers + `MANIFEST.sha256`, the cosignature target moves to
`MANIFEST.sha256` — the same shape as the `COSIGNERS.md` files in
[`atx-conformance`](https://github.com/opena2a-standards/atx-conformance)
and
[`atp-conformance`](https://github.com/opena2a-org/atp-conformance).
v0.1 cosignatures do NOT transfer forward — v0.2 needs its own
cosignatures against its own `MANIFEST.sha256`.

## Cosignature registry

### v0.1 — cross-link attestation

| Cosigner | Commit SHA | atx-conformance verifier run | Sigstore artifact | Date |
|---|---|---|---|---|
| opena2a-org (self-cosigned, v0.1 baseline) | _set on first cosignature_ | `go=8 pass, 0 fail; python=8 pass, 0 fail @ atx-conformance HEAD` | _set on first cosignature_ | _set on first cosignature_ |

### v0.2 — `MANIFEST.sha256` attestation

| Cosigner | Commit SHA | Go verifier | Python verifier | Sigstore artifact | Date |
|---|---|---|---|---|---|
| _(no entries yet; v0.2 not shipped)_ | | | | | |

Self-cosignature exists to anchor the baseline; second-party signatures
are what close A2A#1885 criterion (c) for AIP. The (c) claim is not
treated as closed for AIP until at least one independent (non-OpenA2A)
cosignature lands on the v0.2 `MANIFEST.sha256`.
