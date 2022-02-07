## Genomics Data Analysis Pipeline

### Concept

This pipeline containing a set of program for analyze SARS-CoV-2 genomes, 
1. [Pangolin](https://github.com/cov-lineages/pangolin) performs multiple alignment, calling mutation proiles, and assign lineage to SARS-CoV-2 genome.

2. [UShER](https://github.com/yatisht/usher) creates a mutation-annotated tree (MAT) by calling mutation points from the aligned sequences, and will be used as a pre-existing tree to load samples into it.

3. [Auspice](https://github.com/nextstrain/auspice) receives input in .json format to visualize as a phylogenetic tree.


### Prerequisites

This pipeline is run based on the Conda environment (see https://docs.conda.io/en/latest/miniconda.html to install miniconda), all packages and dependencies to install are in `environment.yml` file. Installing packages by

```
conda create -f environment.yml
```

### Usage

All mandatory data are already deposited in this repository, including pre-existing phylogeny in newick file format. When the new samples arrive, firstly, we have to create a alignment file from FASTA sequences using Pangolin with `--usher` parameter to use UShER model instead of default pangoLEARN to classify lineage (see [UShER's doc](https://usher-wiki.readthedocs.io/en/latest/UShER.html#methodology) to see how lineages are classified), and use `--alignment` parameter to let pangolin provide alignment output file. 

```
pangolin -t `nproc` --usher --alignment <your_sequences.fasta>
```

This will generate 2 output files, including pangolin classification result `lineage_report.csv` that provides prediction statistics and alignment output file `sequences.aln.fasta`. Then, convert alignment fasta to vcf file using `faToVcf` tool.

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

The output result in `json` file format can open with [Auspice web platform](https://auspice.us/), or visuallize locally using the following command,

```
auspice view --datasetDir .
```

You can adjust more parameters related to use auspice from https://github.com/nextstrain/auspice.
