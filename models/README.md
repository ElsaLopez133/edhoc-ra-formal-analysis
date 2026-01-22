## 📁 Structure of this directory

```text
models/
│
├── lake-edhoc-ra.spthy             # Main SAPIC+ specification
├── lake-edhoc-ra-fix.spthy         # SAPIC+ specification with the proposed mitigation for the discovered attack
├── LAKEPropertiesPSK.splib         # Lemmas for SAPIC+
└──  Headers.splib                   # Optional attacker-capability flags
```

## 🚀 Running the model using SAPIC+

The main specification is 
```bash
lake-edhoc-ra.spthy
```
It uses applied pi-calculus for the protocol and first-order logic for lemmas.

### Running the Tamarin Model

#### ▶️ Prove all lemmas (non-interactively)

```bash
tamarin-prover --prove edhoc_psk_sapic.spthy
```
High risk: the model may get killed because of the proof search explosion.

#### ▶️ Prove a specific lemma

```bash
tamarin-prover --prove=AttestationEvidenceIntegrity lake-edhoc-ra.spthy
```

Any lemma listed in LAKEPropertiesPSK.splib can be used here.

#### ⚙️ Adding attacker capabilities

Defined in Headers.splib:
| Flag              | Meaning                                                   |
| ----------------- | --------------------------------------------------------- |
| `LeakShare`       | Ephemeral DH secret leakage                               |
| `LeakSessionKey`  | Leakage of the final session key                          |

To activate any capability:
```bash
tamarin-prover --prove=<lemma> -D<attacker-capability> lake-edhoc-ra.spthy
```

#### Interactive Mode (Optional)

Note: Only works with a GUI. If on a headless server (e.g., SSH), use automatic mode instead.
To explore traces interactively:

```bash
tamarin-prover interactive lake-edhoc-ra.spthy
```

If it fails to open a window, you're likely missing GUI/X11 support — run locally or use X11 forwarding:

```bash
ssh -X user@host
```

#### Automated running

To run all the lemmas and record runtime and proof steps:

```bash
python3 automated_run.py
```

### Running the ProVerif Model

To run with ProVerif, we first need to convert the SAPIC+ file to a ProVerif file:

```bash
tamarin-prover lake-edhoc-ra.spthy -m=proverif > lake-edhoc-ra-proverif.pv
```

To convert to ProVerif with additional flags:
```bash
tamarin-prover lake-edhoc-ra.spthy -D<flag> -m=proverif > lake-edhoc-ra-proverif.pv
```

This transforms the lemmas, expressed in firts-order-logic, into queries, expressed as trace properties.

You can then run ProVerif using

```bash
proverif lake-edhoc-ra-proverif.pv 
```

If you want to output the trace-attack, create a directory (in this case ./graphs) and use the -graph option 

```bash
proverif -graph ./graphs lake-edhoc-ra-proverif.pv
```

To output the elapsed time, do
```bash
/usr/bin/time -f "Elapsed time: %e seconds" proverif lake-edhoc-ra-proverif.pv
```
