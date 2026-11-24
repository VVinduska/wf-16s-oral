# wf-16s-oral

16S rRNA classification of **oral microbiome** samples (interdental plaque, saliva) for Oxford Nanopore
full-length 16S amplicons (e.g. SQK-MAB114.24, R10.4.1).

This is a modified copy of [epi2me-labs/wf-16s](https://github.com/epi2me-labs/wf-16s) **v1.6.1**
(wf-metagenomics v2.14.2). The only functional difference is the default reference database:

| | epi2me-labs/wf-16s | wf-16s-oral |
|---|---|---|
| Default `database_set` | `ncbi_16s_18s` (NCBI targeted loci) | **`eHOMD_v16.03`** (bundled in `oral_db/`) |
| Default `taxonomic_rank` | `G` (genus) | **`S` (species)** |
| Other databases | – | still selectable in the `database_set` drop-down (`ncbi_16s_18s`, `SILVA_138_1`, …) |

The original wf-16s stays installed and unchanged — use it for gut / stool samples, use this workflow for oral samples.

## Česky – použití v EPI2ME Desktop

1. **Workflows → Import workflow** → vložte `https://github.com/VVinduska/wf-16s-oral`
2. Workflow se objeví jako samostatná dlaždice „16S rRNA – oral microbiome (eHOMD)“.
3. Pro ústní vzorky vyberte jen vstupní FASTQ složku. Databáze eHOMD je výchozí, nic dalšího se nenastavuje.
   Pole `reference`, `ref2taxid` a `taxonomy` nechte prázdná.
4. Střevní vzorky dál spouštějte v původním workflow **wf-16s** jako dosud.

Doporučené volby: `output_unclassified = true` (kontrola, co databáze nepokryla).
Výchozí filtry zůstávají: `min_percent_identity = 95`, `min_ref_coverage = 90`.

## Bundled database – `oral_db/eHOMD_v16.03/`

| File | Content |
|---|---|
| `reference.fasta` | 6 600 full-length 16S rRNA reference sequences, eHOMD 16S rRNA RefSeq V16.03 (unchanged) |
| `ref2taxid.tsv` | sequence ID → taxid |
| `taxonomy.tar.gz` | NCBI-style taxdump (`nodes.dmp`, `names.dmp`) built from the eHOMD QIIME taxonomy V16.03 |
| `hmt_taxid.tsv` | taxid → species name → HMT IDs → full lineage |

Notes:
* The taxids are **custom** (not NCBI taxids). Compare results with NCBI-based runs by species name, not by taxid.
* One species node per species name; where several HMT IDs share a name (e.g. *Actinomyces oris*),
  reads are summed under that name (HMT IDs listed in `hmt_taxid.tsv`).
* Ranks: superkingdom; phylum; class; order; family; genus; species (eHOMD has no "kingdom" rank, so it shows as empty / `_none`).
* eHOMD contains oral and upper-aerodigestive-tract taxa only. Reads from non-oral organisms either remain
  unclassified (below the identity threshold) or are assigned to the closest eHOMD relative.

Source data: Human Oral Microbiome Database, https://www.homd.org — please cite HOMD as described on the HOMD website.

## Code changes against wf-16s v1.6.1

* `nextflow.config` – new `eHOMD_v16.03` entry in `database_sets` (local paths inside the workflow), set as default; `taxonomic_rank = 'S'`; manifest name.
* `nextflow_schema.json` – `eHOMD_v16.03` added to the `database_set` choices and set as default; titles.
* `wf-metagenomics/modules/local/databases.nf` – the default-database branch now also accepts **local** (bundled) reference/taxonomy paths, not only `s3://` URLs. Behaviour for the original S3 databases is unchanged.
* `wf-metagenomics` is included as plain files (not a git submodule), pinned at the commit used by wf-16s v1.6.1.

The original wf-16s documentation is in [`docs/README_wf-16s_original.md`](docs/README_wf-16s_original.md).

## Licence

This Source Code Form is subject to the terms of the Oxford Nanopore Technologies PLC. Public License, v. 1.0
(see `LICENSE`). The licence grants rights **solely for Research Purposes**. Commercial use requires a separate
agreement with Oxford Nanopore Technologies (support@nanoporetech.com).
