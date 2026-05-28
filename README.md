# aip-conformance

Scaffold repository for conformance fixtures and reference verifiers for the
[Agent Identity Protocol (AIP) v1.0.0-draft](https://github.com/opena2a-org/agent-identity-protocol).

**Status: v0.1 — scaffold only. No fixtures shipped yet.**

This repository exists to publicly surface an open architectural question
about what AIP conformance fixtures should look like. Sibling repos
[`atx-conformance`](https://github.com/opena2a-org/atx-conformance) (8
fixtures) and [`atp-conformance`](https://github.com/opena2a-org/atp-conformance)
(4 fixtures at v0.1) already ship byte-stable fixtures with two reference
verifiers each. Closing parity for AIP requires answering the question
below first.

License: Apache 2.0.

## Why no fixtures yet

The A2A coordination map issue
[`a2aproject/A2A#1885`](https://github.com/a2aproject/A2A/issues/1885)
publicly named the gap: criterion (c) on the OpenA2A maturity bar asks for
peer-cosigned conformance fixtures comparable to A2A-IDF's
`aim-did-rfc9421/*` set. ATX and ATP have natural byte-stable artifacts
(credentials, signed proofs, STH); AIP mostly does not.

Most of AIP is RUNTIME behavior: fine-grained authorization enforcement,
capability grants, audit-log emission, drift detection, challenge-response
verification. None of those produce a single byte-stable artifact that a
second-party verifier can walk offline.

Four candidate AIP artifacts surfaced during scoping. Of these, three
collapse onto existing fixture suites and one is genuinely novel:

| Candidate | AIP section | Verdict |
|---|---|---|
| W3C Verifiable Credential `AgentTrustCredential` | §6.4 | **Overlaps ATX v1.0.** ATX is the agent-specific VC that ATP §4.6 carves out. Shipping an AIP-VC fixture would duplicate `atx-conformance` without adding novel coverage. |
| Signed `/authorize` response | §5 | **Collapses onto ATP trust proof.** Same canonical signing form, same Ed25519/hybrid path. Covered by `atp-conformance/fixtures/trust-proof-*`. |
| Trust score certificate over the 9-factor breakdown | §6 | **Collapses onto ATX `trustScore` field.** The 9-factor computation is already cited in `atx-conformance/README.md` and the value is already in every ATX credential. |
| Signed capability grant artifact | §4 | **Partially novel.** Underspecified canonical form in v1.0.0-draft; would need spec work before fixtures can pin a wire format. |
| Challenge-response transcript | §5.1 | **Novel.** Challenge + response are deterministic given a fixed clock and a fixed nonce. Not covered by ATX or ATP. Strong candidate for the first AIP-specific fixture suite. |

## Open question (CHIEF-CA Decision 3, awaiting user resolution)

Options for v0.2:

**Option 3-A: Cross-link `atx-conformance` as the AIP §6.4 VC fixture.**
This repository would ship a README that points at `atx-conformance` and
explains how to read each ATX fixture as an AIP §6.4 conformance vector.
No new fixtures shipped here; the maturity-bar (c) claim is satisfied
transitively via the atx-conformance cosignatures.

- Pros: minimal duplication; lowest cost; honest about what AIP §6.4 is.
- Cons: leaves the AIP coordination-map entry pointing at a repo that's
  semantically about ATX. Could confuse readers.

**Option 3-B: Ship AIP §5.1 challenge-response transcript fixtures.**
Build a small suite (3-4 fixtures) of deterministic challenge-response
transcripts:

- `challenge-response-valid` — agent signs the challenge correctly
- `challenge-response-wrong-key` — response signed by a non-bound key
- `challenge-response-stale-challenge` — challenge older than the verifier's allowed window
- `challenge-response-replay` — same nonce reused (requires verifier state)

These are byte-stable given pinned clock + pinned nonce + pinned keypair.
This is the genuinely AIP-novel surface that neither ATX nor ATP covers.

- Pros: novel AIP coverage; clean (c)-bar satisfaction; gives second
  implementers something AIP-specific to walk.
- Cons: more work; requires defining a canonical challenge-response wire
  format if AIP-SPEC §5.1 underspecifies anything (likely a small spec PR).

**Option 3-C: Both.** Ship the cross-link AND the challenge-response
fixtures. Most complete coverage; highest cost.

User decision required before v0.2. File an issue on this repository when
ready, or raise it on
[`a2aproject/A2A#1885`](https://github.com/a2aproject/A2A/issues/1885).

## Sibling repositories

| Repo | Spec | Status |
|---|---|---|
| [`atx-conformance`](https://github.com/opena2a-org/atx-conformance) | ATX v1.0 credential schema | 8 fixtures, 2 verifiers, MANIFEST pinned |
| [`atp-conformance`](https://github.com/opena2a-org/atp-conformance) | ATP v1.0.0-rc1 protocol | 4 fixtures (discovery, trust-proof × 2, STH), 2 verifiers, MANIFEST pinned |
| `aip-conformance` (this repo) | AIP v1.0.0-draft identity protocol | Scaffold only at v0.1 |

## References

- [AIP-SPEC.md](https://github.com/opena2a-org/agent-identity-protocol/blob/main/AIP-SPEC.md) — the spec under test
- [AIP §5.1 Challenge-Response Protocol](https://github.com/opena2a-org/agent-identity-protocol/blob/main/AIP-SPEC.md#51-challenge-response-protocol) — the novel fixture surface under Option 3-B
- [`a2aproject/A2A#1885`](https://github.com/a2aproject/A2A/issues/1885) — the public maturity-bar claim this work delivers against
- [`atx-conformance`](https://github.com/opena2a-org/atx-conformance) — the pattern aip-conformance fixtures (if shipped) would mirror

## License

Apache 2.0, see [`LICENSE`](./LICENSE).
