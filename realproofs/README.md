# realproofs: printed twee proofs

Since 20 Nov 2026 15:04 UTC every loop keeps twee's printed proof of each goal it proves. The search runs use
`--no-proof --quiet`; a proved run is rerun with the proof on, on the same problem file, by loop.py's proof job or
exp/proofq.py in the twee repo (`hints/`). The proofs are packed here by `hints/exp/hintdata_build.py --realproofs`.

## Layout

| path | what |
|---|---|
| `<family>/<family>_NN.tar.xz` | the proofs of one run family. Each part is at most 10 MB; parts split by loop dir, then original, then goal number. Entries are `<loop dir>/<goal>.proof`, e.g. `Z6.xfer/aa1_12__goal_101.proof`. |
| `index.tsv.gz` | `goal<TAB>loop dir<TAB>tarball`: where each proof is. |
| `cond_status.tsv.gz` | for the cond and plainlive families: `goal<TAB>sweep dir<TAB>unproved siblings used<TAB>proved siblings used<TAB>kNN lemmas used` (sibling goal numbers, `-` if none). A `-` in the third column is an unconditional proof. |
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
| production | UL, XFL, GML, STEPJ, SW20/c*, ROOTS/t40, CI1-CI4 | the production loop and its side loops: transfer, derived-rule transfer, step joins, the 20 s sweep, the roots reruns; CI1-CI4 (23 Nov): proofs of the plain sweeps (twee with lemma axioms only, no hints) imported and merged into UL |
| smoke | HCT, KCT, QKC, QKT, RCT, YDT, KDS, LSM, LTD, PIS, FRS, FRT, KDA, KDB, PLS2, PLR1 and their side loops | short smoke tests of new options (1-6 workers, minutes) |
| Z7, Z7C | each with .xfer and _GML | the Z7 pair, from zero on all goals, 6 workers, 8 hours (21 Nov): Z7 = the late-phase options (`--retry-class --yield-discount 2000 --reexplore 12:0.25 --knn-ctx 10 --head-class`), 43,769 solved; Z7C = without them (the 20 Nov system) |
| Z8 | Z8, Z8.xfer, Z8_GML, Z8_GF | from zero on all goals, Z7's options + `--floor-ratio 20`, 6 workers, 6 hours (22 Nov 02:53–08:55), 42,917 solved |
| Z9 | Z9, Z9.xfer, Z9_GML | from zero on all goals, Z8's options + `--lemma-ax` in the transfer and `--knn-drift 5`, 6 workers, 8 hours (23 Nov 02:46–10:47), 44,161 solved in its main loop |
| P2Q, P2R, P2S | each with .xfer and _GML | exp49/50, half2 from zero (21 Nov): 1 s quick first runs, the control, 3 s retries |
| P2T, P2U | each with .xfer and _GML | exp52, half2 from zero, 3 hours (21 Nov): the control and `--floor-ratio 20` |
| P2V, P2W | each with .xfer and _GML | exp53, half2 from zero, 3 hours (21-22 Nov): the control and `--cp-renormalise-threshold 1000` |
| profile | PRA, PRB, PRC, PRF | short runs under a profiler (21 Nov, the dispatcher's cost) |
| exp54 | LAA, LAB | the late A/B: the 3,817 half2 goals Z7 left, in Z7's context, 4 workers, 3 hours (22 Nov): the control and `--head-time 20` |
| exp56 | GMR, GMR2, GMR3 | retries of the derived-rule hits the live loop had given up on (22 Nov): newest hits; with the source runs' lemma axioms; longer passes |
| exp57 | CW0-CW7, CW9 | twee's critical-pair weights, 300 late heads, one 20 s run each (CW9: 40 s), 22 Nov |
| exp59 | HUB80 | 24 hub roots, one 80 s run each in Z8's context (22 Nov) |
| P2X, P2Z | each with .xfer and _GML | exp55, half2 from zero, 4 workers, 6 hours (22 Nov 14:23–20:25): the control (21,592 solved) and `--knn-drift 5` (21,796) |
| exp61 | RD5 | one 5 s run each for 300 of Z8's unsolved goals in its final context (22 Nov) |
| P4A, P4B | each with .xfer and _GML | exp64, half2 from zero, 4 workers, 6 hours (23 Nov 10:52–16:53): the control and two plain levels in the cascade (`plk100s40@0`, `plk400s130@0`: lemma axioms only, no hints, no rule model) |
| cond | cond/<sweep> | the plain sweeps of 23 Nov (hints/exp/condprove.py, outside loops/): one plain twee-pg61 run per goal, with sibling goals and the kNN nearest proven goals as lemma axioms (`lemma_goal_J`, `lemma_knn_I_<goal>`). Many use unproved siblings: true (they are Prover9 proof steps) but not proved by us, see cond_status.tsv.gz. The unconditional proofs of unsolved goals were also imported into production (CI1-CI4). Some sweeps (ZQ*, ZK*, ZG*, ZL*) ran in Z9's context, not production's |
| plainlive | cond/PLNL/c<cycle><setting> | the live plain loop's cycles (abs/plainlive*.sh), every 30 minutes on the changed goals; as cond |

A `.xfer` dir holds the proofs of a run's transfer loop: a goal proved in a theory with more axioms, with the source
goal's lemmas. A `_GML` dir holds the proofs of its derived-rule transfer, from rules that failed runs derived.

## Not complete yet

At the last packing (23 Nov 19:34 UTC) the families above were complete, production and the live plain loop up to then.
Not here yet:

* exp65's arms Q5A, Q5B (half2 from zero, 23 Nov 16:55 to about 22:55), packed when they end;
* the climbers (CLHL3, the live head climber; CLA67, CLB67, exp67's paired settings), as family `climbers` when
  they end. Since 23 Nov 19:47 each climb run's proof is `<goal>.<run tag>.proof`: `s<k>` a sketch run, whose
  axioms include unproved goals (`assume_*`, true Prover9 steps), `final` or `direct` a real proof, `.weak` a run on
  a weakened goal. Before that, every run of a climb overwrote one `<goal>` file, so only the files without an
  `assume_` axiom (real proofs) are packed from then. In `index.tsv.gz` a climber row's goal is the target.

Production's goals proved before 20 Nov 15:04 (all but 37 of loops/UL's) have no printed proof here. Their proof
steps are in `../proofs/` (`--print-hints` format), and `THM_proofs.tar.gz` covers 31,980 goals.
