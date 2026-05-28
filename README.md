# aip-conformance

Scaffold repository for conformance fixtures and reference verifiers for the
[Agent Identity Protocol (AIP) v1.0.0-draft](https://github.com/opena2a-org/agent-identity-protocol).

**Status: v0.1 — scaffold + cross-link. AIP §6.4 (VC `AgentTrustCredential`)
conformance is covered transitively via `atx-conformance`; AIP §5.1
challenge-response transcript fixtures are in flight as a separate
workstream (Decision 3-C resolved 2026-05-27).**

This repository carries the AIP-side framing of the OpenA2A conformance
work. Sibling repos
[`atx-conformance`](https://github.com/opena2a-standards/atx-conformance)
(8 fixtures) and
[`atp-conformance`](https://github.com/opena2a-org/atp-conformance)
(4 fixtures at v0.1) already ship byte-stable fixtures with two reference
verifiers each. AIP's coverage shape is documented below.

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

## Resolution (Decision 3, 2026-05-27)

**Chosen path: 3-C (cross-link + new §5.1 fixtures).**

The three options that had been on the table:

| Option | Shape | Outcome |
|---|---|---|
| 3-A | Cross-link `atx-conformance` for §6.4; no new fixtures in this repo. | Rejected as standalone — too weak for the AIP-native §5.1 surface. |
| 3-B | Ship AIP §5.1 challenge-response fixtures only; no §6.4 cross-link. | Strictly dominated by 3-C — same fixture-build cost, missing one README paragraph. |
| 3-C | Cross-link `atx-conformance` for §6.4 AND ship a §5.1 challenge-response fixture suite. | **Selected.** §6.4 reuses the ATX fixtures without duplication; §5.1 gets its own dedicated suite as the AIP-native protocol surface. |

What this means for v0.1 (live in this repo today) vs v0.2 (in flight):

- **v0.1 (live):** Repository carries the cross-link to
  [`atx-conformance`](https://github.com/opena2a-standards/atx-conformance)
  for §6.4 (VC `AgentTrustCredential`) conformance. No `fixtures/`,
  `verifiers/`, or `MANIFEST.sha256` shipped at v0.1 because the §6.4
  artifact is the ATX credential — the byte-stable VC fixture for §6.4
  lives at `atx-conformance/fixtures/baseline-valid.json` and the seven
  ACCEPT/REJECT siblings around it.
- **v0.2 (in flight):** A dedicated AIP §5.1 challenge-response transcript
  fixture suite is being built as a separate workstream. Expected shape
  mirrors `atp-conformance`: deterministic JSON bytes given pinned clock
  + pinned nonce + pinned keypair, Go and Python reference verifiers,
  `MANIFEST.sha256` pinning. Likely fixtures: `challenge-response-valid`,
  `challenge-response-wrong-key`, `challenge-response-stale-challenge`,
  `challenge-response-replay`.

The maturity-bar (c) claim on
[`a2aproject/A2A#1885`](https://github.com/a2aproject/A2A/issues/1885) is
not treated as closed for AIP until BOTH (a) the v0.2 §5.1 fixture suite
ships AND (b) at least one independent (non-OpenA2A) Sigstore cosignature
lands.

## Sibling repositories

| Repo | Spec | Status |
|---|---|---|
| [`atx-conformance`](https://github.com/opena2a-standards/atx-conformance) | ATX v1.0 credential schema | 8 fixtures, 2 verifiers, MANIFEST pinned |
| [`atp-conformance`](https://github.com/opena2a-org/atp-conformance) | ATP v1.0.0-rc1 protocol | 4 fixtures (discovery, trust-proof baseline, trust-proof hybrid, Signed Tree Head), 2 verifiers, MANIFEST pinned |
| `aip-conformance` (this repo) | AIP v1.0.0-draft identity protocol | v0.1: cross-link to `atx-conformance` (covers §6.4); v0.2 in flight: §5.1 challenge-response transcript fixtures |

## References

- [AIP-SPEC.md](https://github.com/opena2a-org/agent-identity-protocol/blob/main/AIP-SPEC.md) — the spec under test
- [AIP §5.1 Challenge-Response Protocol](https://github.com/opena2a-org/agent-identity-protocol/blob/main/AIP-SPEC.md#51-challenge-response-protocol) — the novel fixture surface under Option 3-B
- [`a2aproject/A2A#1885`](https://github.com/a2aproject/A2A/issues/1885) — the public maturity-bar claim this work delivers against
- [`atx-conformance`](https://github.com/opena2a-standards/atx-conformance) — the suite that covers AIP §6.4 transitively; the pattern v0.2 §5.1 fixtures will mirror
- [`atp-conformance`](https://github.com/opena2a-org/atp-conformance) — the closest pattern for v0.2 §5.1 fixtures (deterministic JSON + Go/Python verifier pair + `MANIFEST.sha256`)

## License

Apache 2.0, see [`LICENSE`](./LICENSE).
