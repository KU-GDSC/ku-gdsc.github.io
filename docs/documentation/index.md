# Genomic Data Science Workflows

This page hosts the documentation for workflows developed by the GDSC, which are accessible in this [GitHub repository](https://github.com/KU-GDSC/workflows).

These workflows are implemented in [Nextflow](https://www.nextflow.io), a
scripting language designed to facilitate reproducibility and to simplify
running multi-step and multi-sample jobs by aggregating results. To ensure
the portability of these workflows we encapsulate most of the required
programs in software containers, using platforms such as [Docker](https://www.docker.com) and [Singularity](https://sylabs.io/singularity). Containers that
we have created for these workflows are accessible in our
[container repository](https://quay.io/organization/gdsc).

We have developed software modules so that KU users with access to the
[Center for Research Computing cluster](https://docs.crc.ku.edu/#support) will
have access to all the necessary programs to run these pipelines on that HPCC
environment. The setup for external users may require additional support. 
These pipelines were written to be portable, but have been designed and tested
on a SLURM HPCC environment that uses the [Singularity](https://sylabs.io/singularity)
container system.

## Workflow Documentation

1. [Microbial RNA-seq](workflow-microbial-rnaseq.md)
1. [Lexogen QuantPool-Seq](workflow-quantseq.md)
1. [RAD-seq Variant Calling](workflow-rad_variants.md)

## Nextflow Quick Start for GDSC users

Include the following lines with the job submission script to use the pre-built Nextflow modules 

!!! note ""
    ```bash
    module use --append /panfs/pfs.local/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true
    ```

The GDSC workflows can be downloaded using Nextflow with the following command

!!! note ""
    ```bash
    nextflow pull KU-GDSC/workflows
    ```

!!! example "An example run script to run the [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="_blank"}"
    ```bash
    #!/bin/bash
    #SBATCH --job-name=nf_core_rnaseq
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

    nextflow run nf-core/rnaseq \
        -c /panfs/pfs.local/work/sjmac/observer/profiles/gdsc.config
        --input sample_sheet.csv \
        --genome BDGP6 \
        --outdir output \
        --aligner star_rsem \
        --pseudo_aligner salmon \
        --save_reference \
        -resume
    ```

## Nextflow quick start for KUCG users

Include the following lines with the job submission script to use the pre-built Nextflow modules 


!!! note ""
    ```bash
    module use --append /panfs/pfs.local/work/kucg/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true
    ```

The GDSC workflows can be downloaded using Nextflow with the following command

!!! note ""
    ```bash
    nextflow pull KU-GDSC/workflows
    ```

!!! example "An example run script to run the [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="_blank"}"
    ```bash
    #!/bin/bash
    #SBATCH --job-name=nf_core_rnaseq
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

    nextflow run nf-core/rnaseq \
        -c /panfs/pfs.local/work/kucg/observer/profiles/kucg.config
        --input sample_sheet.csv \
        --genome BDGP6 \
        --outdir output \
        --aligner star_rsem \
        --pseudo_aligner salmon \
        --save_reference \
        -resume
    ```
