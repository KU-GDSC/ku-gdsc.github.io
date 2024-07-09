# Quick Start Guides

## High Performance Computing at KU

The Center for Research Computing has detailed guides to working on their research computing environments, which can be found [on their website](https://docs.crc.ku.edu/quick-start/){:target="\_blank"}.

### Accessing the cluster

**ssh** Log in to a secure shell on a remote computer or server

!!! note "Log on to the cluster"

    ```bash
    ssh USERNAME@hpc.crc.ku.edu
    ```

### Copying files to/from the cluster

**scp** Copy files between your computer and the server, or vice versa.

!!! note "Copy a file from your present working directory on your computer to your home directory on the cluster"

    ```bash
    scp myfile.txt USERNAME@hpc.crc.ku.edu:/home/USERNAME
    ```

!!! note "Copy a file from your home directory on the cluster to your present working directory on your computer"

    ```bash
    scp USERNAME@hpc.crc.ku.edu:/home/USERNAME/a_file.txt ./
    ```

## Nextflow Quick Start

We have prepared installed programs on the KU computing cluster to assist users in running Nextflow pipelines, which are accessible to users affiliated with the [Genomic Data Science Core](#ku-genomic-data-science-core-users) or with the [Center for Genomics](#ku-center-for-genomics-users). However, Nextflow is intended to make workflows portable and can be used in many environments, and we provide some [general guidance for getting started for non-KU users](#other-users).

### KU Genomic Data Science Core users

Include the following lines with the job submission script to use the pre-built Nextflow modules 

!!! note ""
    ```bash
    module use --append /kuhpc/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true
    ```

!!! example "An example run script to run the [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="\_blank"}"
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

    module use --append /kuhpc/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true

    nextflow run nf-core/rnaseq \
        -c /kuhpc/work/sjmac/observer/profiles/gdsc.config
        --input sample_sheet.csv \
        --genome BDGP6 \
        --outdir output \
        --aligner star_rsem \
        --pseudo_aligner salmon \
        -resume
    ```

### KU Center for Genomics users

Include the following lines with the job submission script to use the pre-built Nextflow modules 


!!! note ""
    ```bash
    module use --append /kuhpc/work/kucg/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true
    ```

The GDSC workflows can be downloaded using Nextflow with the following command


!!! example "An example run script to run the [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="\_blank"}"
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

    module use --append /kuhpc/work/sjmac/observer/modules
    module load nextflow

    NXF_ANSI_SUMMARY=true
    NXF_ANSI_LOG=true

    nextflow run nf-core/rnaseq \
        -c /kuhpc/work/kucg/observer/profiles/kucg.config
        --input sample_sheet.csv \
        --genome BDGP6 \
        --outdir output \
        --aligner star_rsem \
        --pseudo_aligner salmon \
        -resume
    ```
### Other users

1. Install Nextflow following the directions [on the Nextflow website](https://www.nextflow.io/docs/latest/getstarted.html){:target="\_blank"}
    - Note: Nextflow can be installed in any location, but for the purposes of this documentation it is assumed to be located in the home directory (e.g. `~/nextflow`)

2. Run a small test job

To test that everything is working correctly, you can run a simple test program with the command:

!!! note ""

    ```bash
    ~/nextflow run hello
    ```
  
If everything is loaded properly, you should output that resembles the following:

!!! note ""

    ```
    N E X T F L O W  ~  version 23.04.3
    Launching `https://github.com/nextflow-io/hello` [confident_picasso] DSL2 - revision: 1d71f857bb [master]
    executor >  local (4)
    [5a/e0c8dc] process > sayHello (2) [100%] 4 of 4 ✔
    Hello world!
  
    Bonjour world!
  
    Hola world!
  
    Ciao world!
    ```

3. Download the Nextflow pipeline of interest

!!! note "Download the [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="\_blank"}"
    ```bash
    ~/nextflow pull nf-core/rnaseq
    ```

4. Run the Nextflow pipeline of interest

!!! note "Example run command for [nf-core RNAseq pipeline](https://nf-co.re/rnaseq){:target="\_blank"}"
    ```bash
    ~/nextflow run nf-core/rnaseq \
        --input sample_sheet.csv \
        --genome BDGP6 \
        --outdir output \
        --aligner star_rsem \
        --psudo_aligner salmon \
        --resume
    ```
