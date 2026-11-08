> **Historical preliminary handoff evidence.** These six bundles record the earlier September 22,
> 2026 S08 handoff, before recovery qualification and independent finalization. Their bytes, names,
> hashes, and locations are preserved. Statements below that Evidence is Partial describe that
> earlier observation, not the final program state. See the
> [current evidence guide](../product/Evidence-Model.md). The final ten accepted bundles are
> recorded in the linked independent finalization; these six earlier bundles are not relabeled as
> that final matrix. The bundle binaries are retained with the source repository of record and are
> not carried in this public tree.

# S08 durable qualification evidence

These compact bundles preserve the September 22, 2026 S08 qualification records after their
GitHub Actions copies expire. They contain only canonical evidence, retained diagnostics, file
digests, and restore metadata; they contain no credentials or target source checkout.

Restore any bundle from this directory without contacting GitHub:

```text
python -P -m supportability_gate.qualification_bundle restore --bundle <bundle-name>.zip
```

The command validates the archive member set and digests, revalidates `standard-results.json` with
the production validators, verifies its run identity and decision, and validates release context
when present. The workflow separately ran this command in the pinned
`ubuntu@sha256:496754492fb28b4d3049432f2ca787449331e23fb14f0dd3fffea86bf5a93eb4`
container with networking disabled.

| Bundle | Decision/profile | Bound source | Bytes | SHA-256 |
|---|---|---|---:|---|
| `s08-python-protected-pass.zip` | PASS / Python | PR #261 head `a9a1b1d18f909fe643a6d93fdb70983286435c58`, run `35731537978` attempt 2 | 28,868 | `5ff7dea2a0153ceef85fee4dd4b11c8adafcd960a36f93d46b2e929b5c5899e7` |
| `s08-python-consumer-technical.zip` | TECHNICAL_FAILURE / Python | `consumer-python` PR #222 head `009d922caaf67ac37a291ba61a5deb30e05826a5`, run `35732891266` | 25,594 | `6c114f1b911f0214715b07e99a1325190156dd248c72fbf79d8585a887ab4638` |
| `s08-typescript-consumer-technical.zip` | TECHNICAL_FAILURE / TypeScript | `consumer-typescript` PR #77 head `bff9d4c37597610b4a1154f9b7bbbd7670e34d7f`, run `35732900588` | 15,764 | `4169702bf777cf2517b6b08d595fd4690e28281d70569bfd7804afaa200fb874` |
| `s08-mixed-consumer-technical.zip` | TECHNICAL_FAILURE / mixed | `consumer-mixed` PR #686 head `9c2b6f7e11cf173daa1ef9d902264c9dc103446f`, run `35732909506` | 69,273 | `ac4669c8e6f5a8abc86a866e208d7fcfbcc18b8c715b664f144420fa46e6f231` |
| `s08-policy-poison-with-technical-failure.zip` | TECHNICAL_FAILURE / Python | PR #262 head `7a70e3df015aa8e3b280042c7f947970f8df5d9e`, run `35732920559` | 22,514 | `4b28b66d22541ca46d2a8ebaab310813cd99cfd6b41dcf743cc9de35912674a0` |
| `s08-provenance-technical-failure.zip` | TECHNICAL_FAILURE / Python | PR #263 head `cb7b915f083dc3badef8eab05bd934ef5c111a53`, run `35732929387` | 21,310 | `96d0399271d95ba8744a36c3cc012ec0b24206c1303debc0e7bc1b1b19cdea49` |

The protected PASS bundle also embeds and digest-binds its pass-context record of the Python and
lock identities, immutable restore image, complete relevant ruleset snapshots, delivery
identities, source-test references, Actions expiry, and known runtime limitations.

The consumer filenames state their actual result. Fresh TypeScript and mixed-profile runs were not
clean positive qualifications: their current target locks could not authenticate a
platform-optional dependency. The fresh Python consumer could not write inside its current hosted
quality sandbox. The policy-poison canary recorded
`INSUFFICIENT_REVIEW_EVIDENCE:human_review.naming`, but an independent malformed-characterization
technical error controlled the overall decision, so it is not claimed as a qualifying pure policy
BLOCK. S08 does not authorize product-specific target repairs or a report-only PASS conversion.
Every canary PR is closed unmerged and its branch is deleted.

Measured protected-run observations are descriptive, not a new service-level objective:

| Run | Wall seconds | Summed job seconds | Evidence job seconds | Evidence artifact bytes |
|---|---:|---:|---:|---:|
| S07 transition baseline `35718759818` | 142 | 259 | 32 | 22,320 |
| S08 implementation `35729508341` attempt 3 | 150 | 258 | 29 | 37,543 |
| S08 directory fix `35731537978` attempt 2 | 149 | 260 | 30 | 25,726 |
| Fresh Python consumer `35732891266` | 170 | 332 | 40 | 50,603 |
| Fresh TypeScript consumer `35732900588` | 127 | 200 | 26 | 29,684 |
| Fresh mixed consumer `35732909506` | 167 | 357 | 33 | 138,827 |

The protected PASS used Python `3.12.14`, lock SHA-256
`fa152bc59237a214f1f4057bd00d2da37eea21dafa44a1cfa96d39959ca61a60`, Node major selector
`24`, and runner label `ubuntu-24.04`. GitHub did not retain exact Node patch or host-image revision
in canonical evidence, so moving-runtime replay remains an explicit limitation. No retained
concurrent-head reproduction demonstrated obsolete-head waste; cancellation is therefore deferred
and no required-check behavior was changed.
