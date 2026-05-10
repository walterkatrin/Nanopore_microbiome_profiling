# Microbiome profiling with NanoClass2

NanoClass2 is a taxonomic meta-classifier for meta-barcoding data generated
with Oxford Nanopore Technology. A single Snakemake command installs all
programs and databases and runs ten popular classification tools in
parallel, making it straightforward to evaluate which one performs best on
a given dataset.

## Why a meta-classifier

While MinION sequencing covers entire marker genes, error rates remain
substantially higher than those of short-read platforms. This creates three
challenges for taxonomic classification:

- **Accuracy** — tools designed for low-error short reads can struggle with
  noisy long reads
- **Computational efficiency** — high error rates require per-read
  annotation rather than prior clustering into ASV/OTU tables
- **Novelty** — no single tool fits all use cases, and gold standards for
  long-read taxonomy are still emerging

Running ten classifiers side by side turns "which tool should I use?" into
an empirical question rather than a guess.

## Requirements

- Conda (package manager)
- Snakemake (workflow manager)

A reasonably powerful desktop is sufficient — most classifiers finish in
minutes to hours.

## Installation

Clone the repository:

```bash
git clone https://github.com/ejongepier/NanoClass
```

Or download the ZIP archive from
<https://github.com/ejongepier/NanoClass>.

---

## Attribution

NanoClass2 was originally developed by **Evelien Jongepier**
([github.com/ejongepier/NanoClass](https://github.com/ejongepier/NanoClass)).
The protocol in this repository is a lightly modified adaptation of that
work.
