# Flyest

Flyest assembles and polishes bacterial genomes while preserving the original
workflow order:

- **Flyer** uses Oxford Nanopore (ONT) reads only.
- **Flyest** uses ONT reads for assembly and adds Illumina paired-end polishing.

Both workflows run Flye, contig filtering, dnaapler rotation, Medaka, Berokka,
the modified `clean.py`, Pilon, final dnaapler reorientation, and quick coverage
QC. Flyest additionally runs Polypolish and the bundled modified POLCA workflow.

## Installation

Clone the repository and create the pinned Conda environment:

```bash
git clone https://github.com/mle8/flyest.git
cd flyest
conda env create -f environment.yml
conda activate flyest
```

The modified POLCA workflow uses the bundled `binaries/ufasta` executable. The
current binary is for Linux.

## Medaka model

Download an appropriate Medaka model separately. Pass the path to its model
archive to `-m/--mod`; Flyest does not download a model automatically. The
pipeline exits before starting if the supplied file does not exist.

For example, a model argument has this form:

```text
-m /path/to/medaka_model.tar.gz
```

Choose a model appropriate for the basecaller and sequencing chemistry used to
produce the ONT reads.

## Flyer: ONT-only workflow

```bash
./scripts/flyer_pipeline.sh \
    -i ont.fastq.gz \
    -m /path/to/medaka_model.tar.gz \
    -o sample_flyer \
    -s SAMPLE \
    -t 8
```

## Flyest: ONT and Illumina workflow

```bash
./scripts/flyest_pipeline.sh \
    -i ont.fastq.gz \
    -1 illumina_R1.fastq.gz \
    -2 illumina_R2.fastq.gz \
    -m /path/to/medaka_model.tar.gz \
    -o sample_flyest \
    -s SAMPLE \
    -t 8
```

Input reads may be gzip-compressed. The output directory must not already
exist. Run either script with `--help` for filtering, Flye metagenome mode,
quick-QC, and other options.

Quick QC reports contig lengths and ONT read-depth statistics. Disable it with
`--no-qc` if these supplementary metrics are not needed.

## Implementation notes

`scripts/clean.py` is adapted from Circlator 1.5.5. The modified
`scripts/polca_mod.sh` and `scripts/fix_consensus_from_vcf.pl` are derived from
the MaSuRCA 4.1.0 POLCA workflow.

`scripts/flyest_qc.sh` is an experimental, unsupported script and is not part of
the installation or supported assembly workflow.

## Author

[William Shropshire](https://twitter.com/The_Real_Shrops)
