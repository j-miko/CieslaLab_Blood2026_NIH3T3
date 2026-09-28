Supplemental code for [Liudkovska et al. 2026](https://doi.org/10.1182/blood.2025032484). This repository provides step-by-step guidance to reproduce the CLIP data in the MS.
The workflow is divided into two stages (cluster and desktop) according to computational demand, however it can run entirely on as single machine. 
All steps require git and conda installed.
Genome and GTF used: GENCODE M38

# Cluster processing
## Getting started

### Clone this repository
```
git clone git@github.com:j-miko/CieslaLab_Blood2026_NIH3T3.git
```

### Prepare STAR index
Provide paths to genome and GTF files in the command below.

```
conda env create -f envs/processing.yml
conda activate processing
nohup STAR --runThreadN 30 --runMode genomeGenerate --genomeDir M38_STAR_index/ --genomeFastaFiles path/to/genome.fasta --sjdbGTFfile /path/to/annotation.gtf --limitGenomeGenerateRAM 33524399488 &
```
**NOTE:** Adjust --runThreadN (number of CPU threads to use) and --limitGenomeGenerateRAM (available RAM) to your systems capabilities.

**NOTE:** The STAR index can be saved to any location (--genomeDir).



### Create and activate conda environment (you can use mamba instead)
```
conda env create -f envs/snakemake.yml
conda activate snakemake
```
### Run Snakemake file to process raw files 

**IMPORTANT:** You need to specify the path to your STAR index in the Snakefile. Set ```STAR_INDEX``` to an absolute path to your index, e.g. ```STAR_INDEX = "/home/user/seq_references/M38/M38_STAR_index/" ```

Try a dry run first:
```
snakemake -c64 --use-conda -n
```
**NOTE:** -c determines number of CPUs to use.

If no errors are reported you can start the run proper:
```
snakemake -c64 --use-conda
```

OR if using slurm:
```
snakemake  -c64 --use-conda --slurm -j12
```
**NOTE:** The first run of the Snakemake file will initialize new conda environments. This may take several minutes.

After the run finishes continue to the analysis steps below. If you're performing the analysis stage on a different machine (e.g. a desktop after running the pipeline on a cluster), copy the whole repository folder there, including the output files produced.

# Analysis using Jupyter notebooks

## Create and activate jupyter environment
```
conda env create -f envs/jupyter-trxtools.yml -n jupyter-trxtools
conda activate jupyter-trxtools
```


## Open Jupyter Lab and run notebooks
```
jupyter lab .
```

Afterwards open the subsequent notebooks in Jupyter and run them to perform the analysis.

# Authors
Jan Mikołajczyk, Tomasz W. Turowski