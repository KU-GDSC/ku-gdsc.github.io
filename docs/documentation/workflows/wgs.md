# Whole-genome Sequencing Data Variant Calling

## Summary of methods 

* Index generated with [BWA](https://github.com/lh3/bwa){:target="\_blank"}
* Filter and trim reads with [fastp](https://github.com/OpenGene/fastp){:target="\_blank"}
* Strand determination with [how are we stranded here](https://pypi.org/project/how-are-we-stranded-here/){:target="\_blank"}
* Quality checking of reads with [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/){:target="\_blank"}
* Reads mapped to reference [BWA MEM](https://github.com/lh3/bwa){:target="\_blank"}
* Alignment post-processing and QC with [Picard](https://broadinstitute.github.io/picard/){:target="\_blank"}
* Variant calling with [bcftools](https://samtools.github.io/bcftools/bcftools.html){:target="\_blank"}
* Variant filtering with [GATK](https://gatk.broadinstitute.org){:target="\_blank"}
* Aggregation of QC tables using [MultiQC](https://multiqc.info){:target="\_blank"}

## Parameters

### Required parameters:

* `--pubdir` 
	* Default: `/<PATH>` 
	* Description: The directory that the saved outputs will be stored.

* `-w`
    * Default: `/<PATH>`
    * The directory that all intermediary files and nextflow processes utilize. This directory can become quite large. This should be a location on scratch space or other directory with ample storage.

* `--sample_folder` 
    * Default: `/<PATH>`
    * The path to the folder that contains all the samples to be run by the pipeline. The files in this path can also be symbolic links.

* `--fasta`
    * Default: `/<PATH>`
    * Path to the reference genome in FASTA format

* `--read_type`
    * Default: `PE`
    * Comment: Type of reads: paired end (PE) or single end (SE).

### Optional parameters:

#### General optional parameters:

* `--concat_lanes`
    * Default: `false`
    * Options: `false` and `true`. Default: `false`. If this boolean is specified, FASTQ files will be concatenated by sample. Used in cases where samples are divided across individual sequencing lanes.

* `--extension`
    * Default: `.fastq.gz`
    * Expected file extension for input read files, modify if files are not compressed or have a different form (e.g. ".fastq" or ".fq.gz")

* `--pattern`
    * Default: `*_R{1,2}*`
    * Expected R1/R2 matching pattern for paired-end read files in the `--sample_folder` path. In concert with `--extension` the default values will match files such as `sampleID.L001.R1.fastq.gz` and `sampleID.L001.R2.fastq.gz`

* `keep_intermediate`
    * Default: `false`
    * If `true` workflow will output intermediate alignment files (unsorted BAMs, etc.)

* `keep_reference`
    * Default: `false`
    * If true workflow will save a copy of the BWA indices to the output directory

#### fastp filtering parameters:

* `--quality_phred`
    * Default: 15
    * Quality score threshold.

* `--unqualified_perc`
    * Default: 40
    * Percent threshold of unqualified bases to pass reads.

* `--tmpdir`
    * Default: "~/scratch/$USER/tmp/"

#### BWA mem mapping parameters:

* `--mismatch_penalty`
    * Default: "-B 8"

* `--bwa_min_score`
    * Default: null
    * Threshold of alignment quality score to emit read alignment

#### Variant calling parameters:

* `--ploidy`
    * Default: 2
    * Options: 1, 2

* `--mpileup_depth`
    * Default: 100
    * For a given position, read maximally INT reads per input file

* `--skip_indels`
    * Default: false
    * If true, do not call indel sites

* `--variants_only`
    * Default: true
    * If true, only emit variant sites (e.g. not homozygous reference)

#### Variant filtering parameters:

* `--filter_dp`
    * Default: "DP < 25"
    * Sites with depth lower than this value will have a filter flag set as "LowCoverage"

* `--filter_very_low_qual`
    * Default: "QUAL < 30.0"
    * Sites with a quality score less than this value will have a filter flag set as "VeryLowQual"

* `--filter_low_qual`
    * Default: "QUAL > 30.0 && QUAL < 50.0"
    * Sites with a quality score less than this value will have a filter flag set as "LowQual"

* `--filter_qd`
    * Default: "QD < 1.5"
    * Sites with a depth-normalized quality score less than this value will have a filter flag set as "LowQD"

* `--filter_fs`
    * Default: "FS > 60.0"
    * Sites with a strand bias greater than this value will have a filter flag set as "StrandBias"



## Pipeline Default Outputs 
 
| Naming Convention                                                    | Description                                                                                                   |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `wgs_cohort_variants_bcftools_filtered.vcf` | Soft-filtered VCF including all samples |
| `wgs_cohort_variants_bcftools_filtered_genotype_table.tsv` | RSEM-generated gene-level TPM counts merged across all samples |
| `wgs_cohort_variants_bcftools_filtered_site_table.tsv` | RSEM-generated isoform-level raw counts merged across all samples |
| `wgs_report.html` | Nextflow autogenerated report |
| `index/` | BWA-generated indices saved with optional parameter `keep_reference` |
| `multiqc/` | MultiQC report summarizing quality metrics across all samples in the run |
| `${sampleID}/stat/` | QC and Picard metrics files for each sample |
| `trace.txt` | Nextflow trace of processes |
