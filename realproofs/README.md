# realproofs: printed twee proofs

Since 20 Nov 2026 15:04 UTC every loop keeps twee's printed proof of each goal it proves. The search runs use
`--no-proof --quiet`; a proved run is rerun with the proof on, on the same problem file, by loop.py's proof job or
exp/proofq.py in the twee repo (`hints/`). The proofs are packed here by `hints/exp/hintdata_build.py --realproofs`.

## Layout

| path | what |
|---|---|
| `<family>/<family>_NN.tar.xz` | the proofs of one run family. Each part is at most 10 MB; parts split by loop dir, then original, then goal number. Entries are `<loop dir>/<goal>.proof`, e.g. `Z6.xfer/aa1_12__goal_101.proof`. |
| `index.tsv.gz` | `goal<TAB>loop dir<TAB>tarball`: where each proof is. |
| `THM_proofs.tar.gz` | an earlier set (20 Nov), 31,980 proofs, entries `THM_proofs/<goal>.proof`. |

The archives are deterministic (sorted names, mtime 0, no owners), so a rerun of the packer re-commits only the
parts that changed.

Every proof file is twee's output ending in `RESULT: Theorem`. The axioms are the goal's AIM axioms, plus, in most
proofs, lemma axioms named after other goals, e.g. `lemma_knn_1_aa1_15__goal_73`. In the from-zero families (Z6, P2*,
smoke), whose runs start with an empty context, a lemma is a goal the same family proved earlier, so following
lemma names inside a family leads back to proofs from the AIM axioms alone. Production's lemmas are mostly goals it
proved before proof-keeping began; their proof steps are in `../proofs/`.

## Families

| family | loop dirs | what |
|---|---|---|
| Z6 | Z6, Z6.xfer, Z6_GML | from zero on all 51,784 goals, 12 workers, 8 hours (20 Nov 15:30–23:33), 44,095 solved |
| P2H, P2J, P2K | each with .xfer and _GML | exp44/45, half2 from zero, 6 workers, 5 hours (20–21 Nov): `--retry-class`; `--yield-discount 2000 --reexplore 12:0.25`; the control |
| P2N, P2O | each with .xfer and _GML | exp48, half2 from zero, 5 hours (21 Nov 04:36–09:38): P2N = both exp44/45 options, P2O = P2N + `--knn-ctx 10 --head-class` |
| P2G | P2G, P2G.xfer, P2G_GML | exp43's last minutes after proof-keeping started (117 goals) |
| production | UL, XFL, GML, STEPJ, SW20/c*, ROOTS/t40 | the production loop and its side loops: transfer, derived-rule transfer, step joins, the 20 s sweep, the roots reruns |
| smoke | HCT, KCT, QKC, QKT, RCT, YDT and their side loops | 2- to 6-worker smoke tests on sets/smoke487 (about 400 goals each) |

A `.xfer` dir holds the proofs of a run's transfer loop: a goal proved in a theory with more axioms, with the source
goal's lemmas. A `_GML` dir holds the proofs of its derived-rule transfer, from rules that failed runs derived.

## Not complete yet

The reruns of proved goals were paused at 21 Nov 10:47 to free CPU for exp49/50, with about 97,000 queued. At packing
time P2N had 20,148 of its 21,702 proofs and P2O 19,687 of 21,940. exp49/50's arms (P2Q, P2R, P2S) had none yet.
A later `--realproofs` run adds them.

Production's goals proved before 20 Nov 15:04 (all but 37 of loops/UL's) have no printed proof here. Their proof
steps are in `../proofs/` (`--print-hints` format), and `THM_proofs.tar.gz` covers 31,980 goals.
