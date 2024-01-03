# RNA-seq Data Analysis Workflow

## Summary of methods for processing short-read data (`--workflow rnaseq`)

* Filter and trim reads with [fastp](https://github.com/OpenGene/fastp)
* Quality checking of reads with [FastQC](https://github.com/s-andrews/FastQC)
* Map to reference genome using [RSEM](http://bio-bwa.sourceforge.net/bwa.shtml)
* Sort and compress mapped reads with samtools
* Remove optical duplicates using GATK Mark Duplicates
* SV calling with [Delly](https://github.com/dellytools/delly), [Lumpy](https://github.com/arq5x/lumpy-sv), and [Manta](https://github.com/Illumina/manta)
* Merge SV calls with [SURVIVOR](https://github.com/fritzsedlazeck/SURVIVOR)
* Annotation of results based on intersection with [previously identified mouse SVs](https://zenodo.org/record/7644286/), genic and exonic regions.

Input data can be raw FASTQ reads, or a previously mapped BAM file. If a BAM file is provided, the workflow begins with the GATK Mark duplicates step.

## Parameters for Illumina methods

* `--sampleID` 
    * Default: `<STRING>`
    * The sample ID for the input data (required).
* `--pubdir` 
	* Default: `/<PATH>` 
	* Description: The directory that the saved outputs will be stored.
* `--cacheDir` 
 	* Default: `/projects/omics_share/meta/containers` 
 	* Description: This is directory that contains cached Singularity containers. JAX users should not change this parameter.
* `-w`
    * Default: `/<PATH>`
    * The directory that all intermediary files and nextflow processes utilize. This directory can become quite large. This should be a location on /fastscratch or other directory with ample storage.
* `--keep_intermediate`
    * Default: `FALSE`
    * Controls whether intermediate analysis files are copied to the `pubdir`. For this pipeline, this includes unsorted and sorted alignment files.

The following parameters include mutually exclusive options for specifying input data, including --fastq1/--fastq2, OR, --bam
* `--fastq1` 
    * Default: `/<PATH>`
    * The path to a single FASTQ file, or one of a pair of FASTQs for paired-end data.
* `--fastq2` 
    * Default: `/<PATH>`
    * The path to the second of a pair of FASTQs for paired-end data.
* `--bam`
    * `/<PATH>`
    * The path to a BAM input data if alignment has already been performed outside this pipeline.

The following parameters refer to the reference genome.
* `--fasta`
    * Default: `/<PATH>`
    * Path to the reference genome in FASTA format.
* `--fasta_index`
    * Default: `/<PATH>`
    * Optional paramter to specify index for reference genome. If not provided, pipeline will generate an index.
* `--bwa_index`
    * Default: `/<PATH>`
    * Optional paramter to specify BWA indices for alignment. If not provided, pipeline will generate these indices.

The following parameters specify reference data and annotations. The `--genome_build` paramter controls whether the default versions are `GRCm38` or `GRCm39`:
* `--genome_build` 
    * Default: `GRCm38`
    * Parameter that controls reference data used for alignment and annotation. GRCm38 is the default value, GRCm39 is an accepted alternate value.
* `--exclude_regions` 
    * `/ref_data/ucsc_mm10_gap_chr_sorted.bed`
    * BED file that lists the coordinates of centromeres and telomeres to exclude as alignment targets. Note: default path refers to a location within the containers `quay.io/jaxcompsci/lumpy-ref_data:0.3.1--refv0.2.0`and `quay.io/jaxcompsci/delly-ref_data:1.1.6--refv0.2.0`, which require this file.
* `--sv_ins_ref`
    * Default: `/ref_data/variants_freeze5_sv_INS_mm39_to_mm10_sorted.bed.gz`
    * BED file that lists previously indentified insertion SVs. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.
* `--sv_del_ref`
    * Default: `/ref_data/variants_freeze5_sv_DEL_mm39_to_mm10_sorted.bed.gz`
    * BED file that lists previously indentified deletion SVs. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.
* `--sv_inv_ref`
    * Default: `/ref_data/variants_freeze5_sv_INV_mm39_to_mm10_sorted.bed.gz`
    * BED file that lists previously indentified inversion SVs. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.
* `--reg_ref`
    * Default: `/ref_data/mus_musculus.GRCm38.Regulatory_Build.regulatory_features.20180516.gff.gz`
    * BED file that lists regulatory features. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.
* `--genes_bed` 
    * Default: `/ref_data/Mus_musculus.GRCm38.102.gene_symbol.bed`
    * BED file that lists gene symbol IDs and coordinates. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.
* `--exons_bed`
    * Default: `/ref_data/Mus_musculus.GRCm38.102.exons.bed`
    * BED file that lists exons and coordinates. Note: default path refers to a location within the container `quay.io/jaxcompsci/bedtools-sv_refs:2.30.0--refv0.2.0`, which requires this file.

fastp filtering paramenters:
* `--quality_phred`
    * Default: 30
    * Quality score threshold.
* `--unqualified_perc`
    * Default: 30
    * Percent threhold of unqualified bases to pass reads.

SURVIVOR merging parameters:
* `--surv_dist`
    * Default: 1000
    * Maximum distance between breakpoints for merging SVs.
* `--surv_supp`
    * Default: 1 
    * The number of callers (out of 4) required to support an SV.
* `--surv_type`
    * Default: 1
    * Boolean (0/1) that requires SVs to be the same type for merging.
* `--surv_strand`
    * Default: 1
    * Boolean (0/1) that requires SVs to be on the same strand for merging.
* `--surv_min`
    * Default: 30
    * Minimum length (bp) to output SVs.

## Pipeline Default Outputs 
 
| Naming Convention                                                    | Description                                                                                                   |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `mmrSVD_illumina_report.html`                                        | Nextflow autogenerated report                                                                                 |
| `trace.txt`                                                          | Nextflow trace of processes                                                                                   |
| `${sampleID}/${sampleID}_ILLUMINA_DLM_struct_var.vcf`                | VCF output combining merged Delly, Lumpy, and Manta calls annotated for overlap with exonic regions           |
| `${sampleID}/${sampleID}.survivor_joined_results.csv`                | Table of SVs annotated with overlaps of previously identified SVs (`beck`), genes, exons, regulatory regions  |
| `${sampleID}/alignments/${sampleID}.md.bam`                          | Analysis-ready alignment of reads                                                                             |
| `${sampleID}/alignments/${sampleID}.md.bai`                          | Index for analysis-ready alignment of reads                                                                   |
| `${sampleID}/alignments/${sampleID}.md.metrics`                      | GATK MarkDuplicates log                                                                                       |
| `${sampleID}/alignments/${sampleID}.insert_size.txt`                 | Inferred read insert size                                                                                     |
| `${sampleID}/alignments/mapped_lumpy/${sampleID}_alignBWA_lumpy.bam` | Alignment file modified module process LUMPY\_PREP                                                            |
| `${sampleID}/stats/${sampleID}_fastp_report.html`                    | Filtering and trimming report from `fastp`                                                                    |
| `${sampleID}/stats/${sampleID}_fastp_report.json`                    | Filtering and trimming report from `fastp`                                                                    |
| `${sampleID}/unmerged_calls/${sampleID}_dellySort.vcf`               | SV calls from Delly                                                                                           |
| `${sampleID}/unmerged_calls/${sampleID}_lumpySort.vcf`               | SV calls from Lumpy                                                                                           |
| `${sampleID}/unmerged_calls/${sampleID}_mantaSort.vcf`               | SV calls from Manta                                                                                           |