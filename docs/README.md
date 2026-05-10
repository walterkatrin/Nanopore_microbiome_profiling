## Workflow

NanoClass2 runs in two stages: first benchmark all tools on a subset of the
data to pick the best performer(s), then run only the selected tool(s) on
the full dataset.

---

### Stage 1 — Tool selection

Benchmarking all 11 classifiers on the full dataset is expensive. Tests on
datasets of varying complexity show that **100 reads from 3 representative
samples** is enough to assess relative tool performance.

**1. Configure subsampling** in `config.yaml`:

```yaml
subsample:
   skip:        false
   samplesize:  100
```

Use spaces only — no tabs.

**2. List the subset samples** in `samples.csv`:

```
run,sample,barcode,path
stage1,sample1,,input/barcode06.passed.fastq.gz
stage1,sample2,,input/barcode09.fastq.gz
```

Field rules:

| Column   | Notes                                                              |
|----------|--------------------------------------------------------------------|
| `run`    | label for outputs and reports; letters and numbers only            |
| `sample` | unique sample ID; concatenate multi-file samples first             |
| `barcode`| leave empty — only demultiplexed data is accepted                  |
| `path`   | path to the gzipped FASTQ                                          |

If treatments differ in expected community structure, include at least one
sample per treatment.

**3. Run from the NanoClass directory:**

```bash
snakemake --use-conda --cores <ncores>
```

**4. Generate the report:**

```bash
snakemake --report report/NanoClass-stage1.zip
```

Unzip and open `NanoClass-stage1/report.html`. Three plots drive the
tool-selection decision:

- **Classification** — taxonomic bar plots; useful if the expected
  composition is known
- **Precision** — deviation of each tool from the majority-consensus
  classification per taxonomic level (note: precision ≠ accuracy)
- **Runtime** — wall-clock per tool; often the tiebreaker when accuracy is
  comparable

---

### Stage 2 — Full classification

**1. Restrict to the selected tool(s)** at the top of `config.yaml`:

```yaml
methods: ["kraken","minimap"]
```

**2. Disable subsampling** (or raise `samplesize` if reads differ between
samples):

```yaml
subsample:
   skip:        true
   samplesize:  100
```

**3. Add all remaining samples** to `samples.csv` with `run` set to
`stage2`.

**4. Run and report** as in stage 1:

```bash
snakemake --use-conda --cores <ncores>
snakemake --report report/NanoClass-stage2.zip
```

Precision plots are omitted when fewer than three tools are run — consensus
across two tools is not informative.

---

### Outputs for downstream analysis

| File                                                | Content                                       |
|-----------------------------------------------------|-----------------------------------------------|
| `classification/<run>/<tool>/<sample>.<tool>.otumat`| absolute abundances per taxon                 |
| `classification/<run>/<tool>/<sample>.<tool>.taxmat`| taxonomy across 6 levels (Domain → Genus)     |
| `classification/<run>/<tool>/<sample>.<tool>.taxlist`| per-read 6-level classification              |

Per-sample abundance tables can be joined with R's `merge` for
cross-sample analysis.
