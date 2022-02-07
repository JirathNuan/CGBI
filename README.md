## Genomics Data Analysis Pipeline

### Scope

TBW


### Prerequisites

This pipeline run based on the Conda environment (see https://docs.conda.io/en/latest/miniconda.html to install miniconda), all packages and dependencies to install are in `environment.yml` file. Installing packages by

```
conda create -f environment.yml
```

### Usage

All mandatory data are already deposited in this repository, including pre-existing phylogeny in newick file format. When the new samples arrive, firstly, we have to create the alignment file from FASTA sequences using Pangolin with `--usher` to use UShER model instead of default pangoLEARN to classify lineage (see [UShER's doc](https://usher-wiki.readthedocs.io/en/latest/UShER.html#methodology) to see how UShER classifies lineage), and use `--alignment` to let pangolin provide alignment output file. 

```
pangolin -t `nproc` --usher --alignment <your_sequences.fasta>
```

This will generate 2 output files, including pangolin classification result (lineage_report.csv) and alignment output file (sequences.aln.fasta). Then, convert alignment fasta to vcf file using `faToVcf` tool.

```
faToVcf sequences.aln.fasta output.vcf
```

Then, Place new samples to the existing tree using `usher` tool. This requires VCF file that we have generated, and a mutation annotated tree, that is the pre-existing tree protobuf (.pb) file. 
```
usher --vcf output.vcf --load-mutation-annotated-tree global_phylo.pb
```

The usher command generated 3 output files including `mutation-paths.txt`, `placement_stats.tsv`, and `final-tree.nh` that further use for visualization. To do so, use add-in `matUtils extract` to convert newick tree to .json file for visualize using [Auspice](https://auspice.us/)

```
matUtils extract -i data/global_tree.pb -j global_tree.json
```

The result in `json` file format can open with [Auspice web platform](https://auspice.us/), or visuallize locally using the following command,

```
auspice view --datasetDir .
```

