# Protein Structure Prediction Tutorial

This tutorial describes how to predict the 3D structure of a small protein using the BV-BRC Protein Structure Prediction Service. The example uses **crambin**, a 46-amino-acid plant seed protein that is commonly used as a simple test case because it is well characterized and typically produces results quickly.

## Tutorial Overview
In this tutorial, you will:

1. Sign in to BV-BRC.
2. Upload a FASTA file with the crambin sequence to your workspace.
3. Submit a prediction job using the **Auto** tool selector.
4. Wait for the job to complete.
5. Open the result, interpret the confidence metrics, and download the structure.

## Prerequisites

- A free BV-BRC account ([register here](https://www.bv-brc.org/register/))
- A web browser

As with other BV-BRC services, you do **not** need specialized hardware or locally installed software. All analyses are performed on BV-BRC servers and can be accessed through the BV-BRC web or command line interfaces.

> **Running locally?** If you're running a local copy of the BV-BRC web UI, open `http://localhost:3000/app/PredictStructure` instead of the production URL below. The form, the workspace, and the result viewer behave identically — the job runs on the real BV-BRC backend either way.

## Background — A Brief Introduction to Protein Structure Prediction

Protein structure prediction introduces concepts that may be unfamiliar to users who are new to protein biology. This tutorial focuses on the biological interpretation of the results and on using the Protein Structure Prediction Service. Additional background on the underlying prediction methods is available in the Protein Folding Primer (companion document).

Crambin is a hydrophobic 46-residue protein from the *Crambe abyssinica* seed. Its experimental structure (PDB ID `1CRN`) has been solved at near-atomic resolution, providing a reference for comparison. The sequence:

```
TTCCPSIVARSNFNVCRLPGTPEALCATYTGCIIIPGATCPGDYAN
```

It contains three disulfide bonds (Cys3–Cys40, Cys4–Cys32, Cys16–Cys26) and forms a compact α-helix / β-sheet fold. Because of its small size and well-characterized structure, crambin is commonly used as an introductory example. Its short sequence allows predictions to complete quickly, making it well suited to this tutorial.



## Step 1 — Sign in

Open <https://www.bv-brc.org> in your browser. Click **Sign In** in the top-right corner.

Sign in with your BV-BRC credentials.

## Step 2 — Upload the crambin FASTA

Save the following as `crambin.fasta` on your computer:

```
>1CRN|Crambin|46aa
TTCCPSIVARSNFNVCRLPGTPEALCATYTGCIIIPGATCPGDYAN
```

In BV-BRC:

1. Click **Workspaces** → **My Workspace** in the top nav.
2. Navigate into the folder you want to use (or create a new one called `tutorial-crambin`).
3. Click **Upload** in the green Action Bar on the right.
4. Choose **FASTA Sequence File**, select `crambin.fasta`, and click **Start Upload**.

The file appears in the folder once upload completes.

![Workspace folder showing crambin.fasta uploaded](./images/02_workspace_upload.png "Workspace folder showing crambin.fasta uploaded")

## Step 3 — Open the Protein Structure Prediction service

From the main menu, choose **Services** → **Protein Tools** → **Protein Structure Prediction**.

Or go directly to: <https://www.bv-brc.org/app/PredictStructure>

You should see the input form:

![Empty Protein Structure Prediction form](./images/03_form_empty.png "Empty Protein Structure Prediction form")

The fields are described in detail in the [Quick Reference](/quick_references/services/predict_structure_service). This tutorial uses only the basic settings required to run the example.

## Step 4 — Fill in the input form

| Field | Value |
|---|---|
| **Prediction Tool** | `Auto` |
| **Protein** | click the workspace selector, choose `crambin.fasta` |
| **DNA / RNA** | leave empty |
| **Ligands** | leave empty |
| **SMILES** | leave empty |
| **MSA File** | leave empty |
| **Output Folder** | click the folder selector, choose `tutorial-crambin` |
| **Job Name** | type `crambin-auto` (the field is pre-filled with `PredictStructure-<timestamp>` — overwrite it) |

A submission creates a **job** with the name you enter. The job appears in your workspace as an object at `<Output Folder>/<Job Name>`. In this tutorial, that path is `tutorial-crambin/crambin-auto`. The object contains the job parameters, status, logs, and the prediction results.

The *Result location* bar directly below the Job Name field previews this path as you type. The form performs live validation, and the **Submit** button becomes enabled once you have specified an output folder, a job name, at least one biomolecular input, and, for Boltz, OpenFold, or Chai, an MSA file.

![Form filled with example values showing the Result location preview](./images/03b_form_filled.png "Form filled with example values showing the Result location preview")

> **Why `Auto`?** With only a protein and no MSA, the auto-selector chooses **ESMFold**, which is the only engine in this example that can run without an MSA on a single sequence (see the [tool selector decision tree](/quick_references/services/predict_structure_service#prediction-tool)). If you select `boltz` or `chai` without uploading an MSA, the submission fails with a policy error.

## Step 5 — Submit

Click **Submit** at the bottom of the form. A confirmation toast appears in the lower-left corner, and the new job appears in **My Jobs** in the top-right user menu.

![Job submission confirmation toast](./images/04_submit_toast.png "Job submission confirmation toast")

## Step 6 — Wait

For this example, ESMFold typically completes a crambin prediction in about 15–60 seconds on a GPU, plus queue time. Refresh the Jobs list or open the job to view its current status:

| Status | Meaning |
|---|---|
| `queued` | Waiting for a worker |
| `in_progress` | Running |
| `completed` | Output is in your workspace |
| `failed` | Something went wrong; click into the job to see stderr |

## Step 7 — Open the result

Click the completed job. The workspace object (`tutorial-crambin/crambin-auto`) opens with the following layout (for the full reference, see [Quick Reference → Output Results](/quick_references/services/predict_structure_service#output-results)):

```
crambin-auto/
├── results.json
├── report.html             ← interactive 3D viewer
├── inputs/
│   └── query.fasta
├── predictions/
│   ├── rank_1.pdb          ← the predicted structure
│   └── rank_1.cif
├── reports/
│   ├── confidence.json
│   ├── plddt.csv
│   └── pae.png
└── metadata/
    ├── tool.json
    └── runtime.json
```

### View the structure

Click `report.html` and choose **View** from the Action Bar. A 3D viewer opens in 3Dmol.js and shows crambin folded into its characteristic small α/β structure. Three disulfide bridges are visible if you toggle the **sticks** representation for cysteines.

![3Dmol.js view of crambin from report.html with three disulfides visible](./images/05_crambin_3d.png "3Dmol.js view of crambin from report.html with three disulfides visible")

### Read the confidence

Open `reports/confidence.json` (or look at the panel beneath the 3D viewer in `report.html`):

```json
{
  "tool": "esmfold",
  "plddt_mean": 87.4,
  "plddt_min": 71.2,
  "ptm": 0.81,
  "model_confidence": 0.83
}
```

Interpreting these:

- `plddt_mean = 87.4` — high confidence. A value above 80 on a 46-residue monomer indicates that ESMFold is confident across the whole chain.
- `plddt_min = 71.2` — even the lowest-scoring residue remains in the confident range (>70), with no obvious disordered tails.
- `ptm = 0.81` — the overall fold is highly likely to be correct.

If you compare the rank-1 PDB to the experimental `1CRN` structure, for example by using TM-align, the TM-score should be above 0.9. This indicates that ESMFold reproduces crambin essentially perfectly. As noted above, this is not intended as a stress test; it confirms that the workflow is functioning end to end.

### Download the structure

From the Action Bar, choose **Download** for `predictions/rank_1.pdb` to save it locally. Open it in [PyMOL](https://pymol.org/), [ChimeraX](https://www.cgl.ucsf.edu/chimerax/), [Mol\*](https://molstar.org/viewer/), or any other PDB viewer.

## What to try next

| Variation | What to change | What you'll learn |
|---|---|---|
| **Same protein, different tool** | Pick `boltz` and upload `crambin.a3m` (a pre-computed MSA — examples ship with the test data) | How a richer MSA affects diffusion-tool confidence |
| **Multi-chain complex** | Submit a multi-record FASTA (chains A and B in one file). Crambin doesn't naturally dimerize — try a known dimer like the Cro repressor instead | Multimer prediction and `ipTM` interpretation |
| **Protein + ligand** | Add `ligand: ATP` and pick `boltz` (with an MSA) | Co-folding with a small molecule, PoseBusters-style |
| **Compare engines side-by-side** | Run the CWL workflow `multi-tool-comparison.cwl` from the [PredictStructureApp repo](https://github.com/CEPI-dxkb/PredictStructureApp) | Quantitative head-to-head on your own targets |

## Troubleshooting

### `No inputs supplied`

Submit was attempted with the Protein, DNA, RNA, ligand, and SMILES fields all empty. Provide at least one input.

### `Invalid ligand CCD code 'ABCD'`

CCD codes are 1–3 alphanumeric characters. Use `ATP`, not `ATPase`.

### `Boltz / OpenFold / Chai require an MSA`

Set **MSA Source** to *Precomputed MSA from Workspace* and upload an `.a3m`, `.sto`, or `.pqt` file, or set it to *Use MSA Server or Service* to have BV-BRC compute the MSA with ColabFold. **Auto** and **ESMFold** do not require an MSA.

### Job is queued for a long time

GPU partitions are shared. ESMFold jobs have low resource requests and usually start within minutes; Boltz / OpenFold / Chai may wait longer. AlphaFold 2 jobs can wait significantly longer because they hold a GPU for an hour or more.

### Result viewer doesn't render

`report.html` loads 3Dmol.js from `https://3dmol.org/build/3Dmol-min.js` (a CDN, not an embedded script). If the viewer appears blank:

- **Network can't reach 3dmol.org** — check connectivity, corporate proxy / firewall rules, or a captive portal.
- **Ad-blocker or content-script extension** is blocking the CDN — try a private/incognito window or disable extensions for the workspace domain.
- **Strict Content-Security-Policy** in the browser is rejecting the script — open the developer console (Cmd-Opt-J / Ctrl-Shift-J) and look for `Refused to load the script` or `Blocked by CSP` errors.

The underlying structure file (`predictions/rank_1.pdb`) still downloads and opens in PyMOL, ChimeraX, or any local viewer if you'd rather skip the embedded viewer entirely.

## See also

- [Quick Reference](/quick_references/services/predict_structure_service) — every form field documented
- [Recipes](/tutorial/predict_structure/predict_structure_recipes) — patterns for multi-chain complexes, ligands, MSAs, reruns
- [Protein Structure Prediction Service](https://www.bv-brc.org/app/PredictStructure) — open the form
