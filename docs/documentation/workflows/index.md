# Workflow Documentation: 

## KU GDSC developed workflows: 

1. [Microbial RNA-seq: `microbial_rnaseq`](microbial_rnaseq.md)  


### Quick Start for KU Users

!!! example "An example run script to run the [microbial RNAseq pipeline](https://github.com/KU-GDSC/workflows){:target="\_blank"}"
    ```bash
    #!/bin/bash
    #SBATCH --job-name=microbial_rnaseq
    #SBATCH --mail-type=END,FAIL
    #SBATCH --mail-user=<USERNAME>@ku.edu
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task 1
    #SBATCH --time=24:00:00
    #SBATCH --partition=sjmac
    #SBATCH --mem=10G
    #SBATCH --output=%x_%A.out

    module use --append /panfs/pfs.local/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true

    nextflow run ku-gdsc/workflows \
        -r main \
        --workflow microbial_rnaseq \
        --fasta /PATH/TO/FASTA \
        --gff /PATH/TO/GFF \
        --sample_folder /PATH/TO/FASTQ/FILES \
        --pubdir /PATH/TO/OUTPUT/RESULTS \
        -w /PATH/TO/WORK/DIRECTORY \
        --strandedness reverse_stranded \
        -profile gdsc \
        -resume
    ```
**Notes:** 

1. This example runs the microbial RNA-seq workflow. It requires the specification of a reference FASTA, a GFF3, and a folder of FASTQ files (not provided here). See the [full workflow documentation for additional detail](microbial_rnaseq.md)

1. For all pipelines, running the flag `--help` (e.g, `~/nextflow <PATH>/<TO>/main.nf --workflow microbial_rnaseq --help`) will print help documentation for that pipeline and quit.
