---
layout: tutorial_hands_on
title: Binning of metagenomic sequencing data
zenodo_link: https://zenodo.org/records/17660820
level: Intermediate
questions:
- What is metagenomic binning refers to?
- Which tools may be used for metagenomic binning?
- How to assess the quality of metagenomic binning?
objectives:
- Describe what is metagenomics binning.
- Describe common challenges in metagenomics binning.
- Perform metagenomic binning using MetaBAT 2 software.
- Evaluation of MAG quality and completeness using CheckM software.
time_estimation: 2H
key_points:
- Metagenomics binning is a computational approach to grouping together DNA sequences
  from a mixed microbial sample into metagenome-assembled genomes (MAGs)
- The metagenomics binning workflow involves several steps, including preprocessing
  of raw sequencing data, assembly of sequencing reads into contigs, binning of contigs
  into MAGs, quality assessment of MAGs, and annotation of functional genes and metabolic
  pathways in MAGs
- The quality and completeness of MAGs can be evaluated using standard metrics, such
  as completeness, contamination, and genome size
- Metagenomics binning can be used to gain insights into the composition, diversity,
  and functional potential of microbial communities, and can be applied to a range
  of research areas, such as human health, environmental microbiology, and biotechnology
contributions:
  authorship:
  - paulzierep
  - npechl
  - fpsom
  - vinisalazar
requirements:
  - type: internal
    topic: metagenomics
    tutorials: 
      - metagenomics-assembly
subtopic: metagenomics
tags:
- binning
- metagenomics
- microgalaxy
redirect_from:
- "/topics/metagenomics/tutorials/metagenomics-binning/tutorial"
edam_ontology:
- topic_3174
- topic_0196
recordings:
- youtube_id: m8ADGsRVqYI
  length: 25M
  galaxy_version: 24.1.2.dev0
  date: '2024-08-30'
  speakers:
  - npechl
  captioners:
  - npechl
  bot-timestamp: 1725013820


---

Metagenomics is the study of genetic material recovered directly from environmental samples, such as soil, water, or gut contents, without the need for isolation or cultivation of individual organisms. Metagenomics binning is a process used to classify DNA sequences obtained from metagenomic sequencing into discrete groups, or bins, based on their similarity to each other.

The goal of metagenomics binning is to assign the DNA sequences to the organisms or taxonomic groups that they originate from, allowing for a better understanding of the diversity and functions of the microbial communities present in the sample. This is typically achieved through computational methods that include sequence similarity, composition, and other features to group the sequences into bins.

> <comment-title></comment-title>
> Before starting this tutorial, it is recommended to do the [**Metagenomics Assembly Tutorial**]({% link topics/microbiome/tutorials/metagenomics-assembly/tutorial.md %})
{: .comment}

## Binning approaches

There are several approaches to metagenomics binning, including:

- **Sequence composition-based binning**: This method is based on the observation that different genomes have distinct sequence composition patterns, such as GC content or codon usage bias. By analyzing these patterns in metagenomic data, sequence fragments can be assigned to individual genomes or groups of genomes.

- **Coverage-based binning**: This method uses the depth of coverage of sequencing reads to group them into bins. Sequencing reads that originate from the same genome are expected to have similar coverage, and this information can be used to identify groups of reads that represent individual genomes or genome clusters.

- **Hybrid binning**: This method combines sequence composition-based and coverage-based binning to increase the accuracy of binning results. By using multiple sources of information, hybrid binning can better distinguish closely related genomes that may have similar sequence composition patterns.

- **Clustering-based binning**: This method groups sequence fragments into clusters based on sequence similarity, and then assigns each cluster to a genome or genome cluster based on its sequence composition and coverage. This method is particularly useful for metagenomic data sets with high levels of genomic diversity.

- **Supervised machine learning-based binning**: This method uses machine learning algorithms trained on annotated reference genomes to classify metagenomic data into bins. This approach can achieve high accuracy but requires a large number of annotated genomes for training.

## Binning challanges

Metagenomic binning is a complex process that involves many steps and can be challenging due to several problems that can occur during the process. Some of the most common problems encountered in metagenomic binning include:

- **High complexity**: Metagenomic samples contain DNA from multiple organisms, which can lead to high complexity in the data.
- **Fragmented sequences**: Metagenomic sequencing often generates fragmented sequences, which can make it difficult to assign reads to the correct bin.
- **Uneven coverage**: Some organisms in a metagenomic sample may be more abundant than others, leading to uneven coverage of different genomes.
- **Incomplete or partial genomes**: Metagenomic sequencing may not capture the entire genome of a given organism, which can make it difficult to accurately bin sequences from that organism.
- **Horizontal gene transfer**: Horizontal gene transfer (HGT) can complicate metagenomic binning, as it can introduce genetic material from one organism into another.
- **Chimeric sequences**: Sequences that are the result of sequencing errors or contamination can lead to chimeric sequences, which can make it difficult to accurately bin reads.
- **Strain variation**: Organisms within a species can exhibit significant genetic variation, which can make it difficult to distinguish between different strains in a metagenomic sample.

## Common binners

There are plenty of algorithms that perform metagenomic binning. Some of the most widely used include:

* **MaxBin** ({%cite maxbin2015%}): A popular de novo binning algorithm that uses a combination of sequence features and marker genes to cluster contigs into genome bins.
* **MetaBAT** ({%cite Kang2019%}): Another widely used de novo binning algorithm that employs a hierarchical clustering approach based on tetranucleotide frequency and coverage information.
* **CONCOCT** ({%cite Alneberg2014%}): A de novo binning tool that uses a clustering algorithm based on sequence composition and coverage information to group contigs into genome bins.
* **MyCC** ({%cite Lin2016%}): A reference-based binning tool that uses sequence alignment to identify contigs belonging to the same genome or taxonomic group.
* **GroopM** ({%cite Imelfort2014%}): A hybrid binning tool that combines reference-based and de novo approaches to achieve high binning accuracy.
* **SemiBin** ({%cite Pan2022%}): A command-line tool for metagenomic binning with deep learning; handles both short and long reads.
* **Vamb** ({%cite nissen2021improved%}): An algorithm that uses variational autoencoders (VAEs) to encode sequence composition and coverage information.
* **ComeBin** ({%cite Wang2024COMEBin%}): A metagenomic binning tool that integrates both composition and abundance features with machine learning-based clustering to improve binning accuracy across complex microbial communities.

## Bin refinement

There are also bin refinement tools, which can evaluate, combine, and improve the raw bins produced by primary binners such as MetaBAT2, CONCOCT, MaxBin2, or SemiBin. These tools help remove contamination, merge complementary bins, and recover higher-quality MAGs.

* **MetaWRAP** ({%cite Uritskiy2018%}):
  A comprehensive metagenomic analysis pipeline that includes modules for quality control, assembly, binning (wrapping multiple binners), refinement, reassembly, and annotation. Provides an easy-to-use framework for producing high-quality MAGs from raw reads.

* **DAS Tool** ({%cite Sieber2018DASTool%}):
  A bin-refinement tool that combines results from multiple binners (e.g., MetaBAT2, MaxBin2, CONCOCT, SemiBin) into a consensus set of optimized, non-redundant bins. DAS Tool improves overall bin quality by integrating strength from several algorithms.

* **Binnette** ({%cite Mainguy2024Binette%}):
  Binette is a fast and accurate bin refinement tool that constructs high-quality MAGs from the outputs of multiple binning tools. It generates hybrid bins using set operations on overlapping contigs — intersection, difference, and union — and evaluates their quality with CheckM2 to select the best bins. Compared to metaWRAP, Binette is faster and can process an unlimited number of input bin sets, making it highly scalable for large and complex metagenomic datasets.

## Anvi’o: Interactive bin refinement

**Anvi’o** ({%cite Eren2015%}) is a platform for **interactive visualization and manual refinement** of metagenomic bins. While it can run automated binning (defaulting to **CONCOCT**), its main strength lies in allowing users to:

* Inspect contig-level coverage, GC content, and single-copy gene presence
* Visualize connections between contigs in a network view
* Manually merge, split, or reassign contigs to improve bin completeness and reduce contamination
* Annotate bins and link them to taxonomic or functional information

This interactive approach is particularly useful when automated binning produces ambiguous or low-quality bins, enabling **high-confidence MAG reconstruction**.

## So many options, what binner to use ?

Each of these binning methods has its own strengths and limitations, and the choice of a binning tool often depends on the characteristics of the metagenomic dataset and the research question. Practical guidance on which binner to use for specific datasets and environments can be drawn from benchmark studies such as {%cite NatureBinner2025%}.

!["Benchmark for many Binners"](./images/Binning_Benchmark.png "Benchmark of multiple Binners on Activated sludge and Human Gut Microbiome, taken from {%cite NatureBinner2025%}"){:width="60%"}

Additionally, the CAMI I and II challenges provide standardized simulated datasets that highlight the strengths and weaknesses of different binners, helping researchers select the most appropriate tool for their analysis.

!["Benchmark for many Binners based on CAMI"](./images/CAMI_Binners.png "Benchmark of multiple Binners in the CAMI challenge, taken from {%cite Meyer2022%}"){:width="60%"}

A general approach is to perform binning using multiple binners that have shown good performance for the specific dataset, followed by bin refinement to generate an improved bin set that retains the best bins from the analysis.

Does using more binners always improve results? In practice, one must also consider computational resources and time constraints. Running many binners can be very time-consuming and resource-intensive, especially for large studies. In some cases, adding extra binners does not lead to a meaningful increase in bin quality, so the choice of binners should be made carefully. Overall, identifying the optimal combination of binners remains an active area of research, and clear, widely accepted guidelines are still being established.

# Mock binning dataset for this training

Read mapping and binning real metagenommic datasets is a computational demanding task and time consuming. To demonstrate the basics of binning in this tutorial we generated a small mock dataset, that is just large enough to produce bins for all binners in this tutorial. The same binners can be applied for any real life datasets, but as said, plan in some time, up to weeks in some cases.

> <agenda-title></agenda-title>
>
> In this tutorial, we will cover:
>
> 1. TOC
> {:toc}
>
{: .agenda}

# Prepare analysis history and data

Metagenomic binners take typically two data typs as input: assembled contigs in fasta format and coverage information in bam format.

- A fasta file containing the assembled contigs, which can be generated from raw metagenomic sequencing reads using an assembler such as MEGAHIT, SPAdes, or IDBA-UD.

- A bam file containing the read coverage information for each contig, which can be generated from the same sequencing reads using mapping software such as Bowtie2 or BWA.

> <comment-title>Can Bins be generated without coverage information</comment-title>
>
> Not all binners require coverage information — some, like MetaBAT2, can operate using only genomic composition (e.g. tetranucleotide frequencies) when coverage files are not available. This is especially useful for single-sample datasets or legacy data where coverage cannot easily be calculated.
>
> Other tools that support composition-only binning include:
> - **MaxBin 2** (can run with composition alone, but performs better with depth)
> - **SolidBin** (supports single-sample binning based on sequence features)
> - **VAMB** (primarily uses deep learning on composition, coverage optional)
>
> That said, including coverage information generally increases binning accuracy, especially for:
> - Differentiating closely related strains
> - Datasets with uneven abundance
> - Multi-sample metagenomics workflows (e.g. differential coverage binning)
>
> In summary: yes, it’s possible to bin without coverage, but coverage-aware workflows are recommended when available, as they reduce contamination and improve completeness.
>
{: .comment}


To run binning, we first need to get the data into Galaxy. Any analysis should get its own Galaxy history. So let's start by creating a new one:

> <hands-on-title>Prepare the Galaxy history</hands-on-title>
>
> 1. Create a new history for this analysis
>
>    {% snippet faqs/galaxy/histories_create_new.md %}
>
> 2. Rename the history
>
>    {% snippet faqs/galaxy/histories_rename.md %}
>
{: .hands_on}

We need to get the data into our history.

In case of a not very large dataset it's more convenient to upload data directly from your computer to Galaxy.

> <hands-on-title>Upload data into Galaxy</hands-on-title>
>
> 1. Import the contig file from [Zenodo]({{ page.zenodo_link }}) or a data library:
>
>    ```text
>    {{ page.zenodo_link }}/files/MEGAHIT_contigs.fasta
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
>    {% snippet faqs/galaxy/datasets_import_from_data_library.md %}
>
>    > <comment-title></comment-title>
>    > In case of large dataset, we can use FTP server or the [Galaxy Rule-based Uploader]({% link topics/galaxy-interface/tutorials/upload-rules/tutorial.md %}).
>    {: .comment}
>
> 2. Create a collection named `Contigs`
>
>    {% snippet faqs/galaxy/collections_build_list.md %}
>
> 3. Also import the raw reads in fastq format (\*.fasta) from [Zenodo]({{ page.zenodo_link }}) or a data library:
>
>    ```text
>    {{ page.zenodo_link }}/files/reads_forward.fastqsanger.gz
>    {{ page.zenodo_link }}/files/reads_reverse.fastqsanger.gz
>    ```
> 4. Create a collection named `Reads`
>
>    {% snippet faqs/galaxy/collections_build_list_paired.md %}

> <comment-title>Why do we use collections here?</comment-title>
> In this tutorial, collections are not strictly necessary because we are working with only one contig file and its paired-end reads. However, in real metagenomic studies, it is common to process many samples—sometimes hundreds or even thousands—and in those cases, collections become essential for managing data efficiently.  
>  
> It is generally good practice to first test a workflow on a small subset of the data (for example, a collection containing only a single sample) to ensure that the tools run correctly and the parameters are appropriate before launching thousands of jobs on Galaxy.
{: .comment}

# Preparation for binning

As explained before we need coverage information in bam format as a requirement for all binners. Some binners need a specific format for the coverage information, but this will be covered in the version specific to the desired binner. For now we will map the quality controled reads to the contigs to get a bam file with the coverage information. This bam file also needs to be sorted for the downstream binners.

Make sure the reads are quality controlled. E.g. following the QC toturial TODO.

> <hands-on-title> Map reads to contigs </hands-on-title>
>
> 1. {% tool [Bowtie2](toolshed.g2.bx.psu.edu/repos/devteam/bowtie2/bowtie2/2.5.4+galaxy0) %} with the following parameters:
>    - *"Is this single or paired library"*: `Paired-end`
>        - {% icon param-collection %} *"FASTQ Paired Dataset"*: `Reads` (Input dataset collection)
>        - *"Do you want to set paired-end options?"*: `No`
>    - *"Will you select a reference genome from your history or use a built-in index?"*: `Use a genome from the history and build index`
>        - {% icon param-collection %} *"Select reference genome"*: `Contigs` (Input dataset collection)
>    - *"Set read groups information?"*: `Do not set`
>    - *"Select analysis mode"*: `1: Default setting only`
>    - *"Do you want to tweak SAM/BAM Options?"*: `No`
>    - *"Save the bowtie2 mapping statistics to the history"*: `Yes`
>
{: .hands_on}

> <hands-on-title> Sort bam files </hands-on-title>
>
> 1. {% tool [Samtools sort](toolshed.g2.bx.psu.edu/repos/devteam/samtools_sort/samtools_sort/2.0.7) %} with the following parameters:
>    - {% icon param-file %} *"BAM File"*: output of **Bowtie2** {% icon tool %}
>    - *"Primary sort key"*: `coordinate`
>
{: .hands_on}

The sorted bam file can be used as input for any of the binning tools.

# Binning

As explained before, there are many challenges to metagenomics binning. The most common of them are listed below:

- High complexity.
- Fragmented sequences.
- Uneven coverage.
- Incomplete or partial genomes.
- Horizontal gene transfer.
- Chimeric sequences.
- Strain variation.

![Metagenomic binning involves grouping contigs into 'bins' based on sequence composition, coverage, or other properties.](./images/binning.png "Metagenomic binning involves grouping contigs into 'bins' based on sequence composition, coverage, or other properties."){:width="60%"}

In this tutorial, we offer dedicated versions, which highlight each of the following binners:

{% include _includes/cyoa-choices.html option1="MetaBAT2" option2="MaxBin2" option3="SemiBin" option4="CONCOCT" option5="COMEBin" default="MetaBAT2" %}

<div class="MetaBAT2" markdown="1">
{% include topics/microbiome/tutorials/metagenomics-binning/metabet2_version.md %}
</div>
<div class="MaxBin2" markdown="1">
{% include topics/microbiome/tutorials/metagenomics-binning/maxbin2_version.md %}
</div>
<div class="SemiBin" markdown="1">
{% include topics/microbiome/tutorials/metagenomics-binning/semibin_version.md %}
</div>
<div class="CONCOCT" markdown="1">
{% include topics/microbiome/tutorials/metagenomics-binning/concoct_version.md %}
</div>
<div class="COMEBin" markdown="1">
{% include topics/microbiome/tutorials/metagenomics-binning/comebin_version.md %}
</div>

# Bin refinement

Now, that you have produced bins with your favorite Binning algorithms you can refine the recovered bins. 
Therefore, you need to convert the bins into a contig to bin mapping table, combine the tables from each binner into one collection and
use Binette to creat consensus bins. An alternative tool would be {% tool [DAS Tool](toolshed.g2.bx.psu.edu/repos/iuc/das_tool/das_tool/1.1.7+galaxy1) %} which is also available in Galaxy.

For the refinement we will use the bins created by all the binners used before. If you do not want to run them all by yourself,
we have provided the results here as well. 

> <hands-on-title>Get result bins</hands-on-title>
>
> 1. Import the contig file from [Zenodo]({{ page.zenodo_link }}) or a data library:
>
>    ```text
>    {{ page.zenodo_link }}/files/semibin_0.fasta
>    {{ page.zenodo_link }}/files/maxbin_0.fasta
>    {{ page.zenodo_link }}/files/maxbin_1.fasta
>    {{ page.zenodo_link }}/files/metabat_0.fasta
>    {{ page.zenodo_link }}/files/concoct_1.fasta
>    {{ page.zenodo_link }}/files/concoct_2.fasta
>    {{ page.zenodo_link }}/files/concoct_3.fasta
>    {{ page.zenodo_link }}/files/concoct_4.fasta
>    {{ page.zenodo_link }}/files/concoct_5.fasta
>    {{ page.zenodo_link }}/files/concoct_6.fasta
>    {{ page.zenodo_link }}/files/concoct_7.fasta
>    {{ page.zenodo_link }}/files/concoct_8.fasta
>    {{ page.zenodo_link }}/files/concoct_9.fasta
>    ```
>
> 2. Create a collection for each bin set called e.g. maxbin, semibin ... by selecting only the bins created by this binner and creating a collection:
>
>    {% snippet faqs/galaxy/collections_build_list.md %}
{: .hands_on}

Once each bin set is converting into one collection they can be converted into a contig to bin mapping table. Perform this step for every bin set.

> <hands-on-title> Convert the bins into a contig to bin mapping table </hands-on-title>
>
> 1. {% tool [Converts genome bins in fasta format](toolshed.g2.bx.psu.edu/repos/iuc/fasta_to_contig2bin/Fasta_to_Contig2Bin/1.1.7+galaxy1) %} with the following parameters:
>    - {% icon param-file %} *"Bin sequences"*: `bins` (output of any of the binners {% icon tool %})
>
{: .hands_on}


> <hands-on-title> Build a list of the binning tables </hands-on-title>
>
> 1. {% tool [Build list](__BUILD_LIST__) %} with the following parameters:
>    - In *"Dataset"*:
>        - {% icon param-repeat %} *"Insert Dataset"*
>            - {% icon param-file %} *"Input Dataset"*: `contigs2bin` (output of **Converts genome bins in fasta format** of SemiBin {% icon tool %})
>            - *"Label to use"*: `Index`
>        - {% icon param-repeat %} *"Insert Dataset"*
>            - {% icon param-file %} *"Input Dataset"*: `contigs2bin` (output of **Converts genome bins in fasta format** of MetaBAT2 {% icon tool %})
>            - *"Label to use"*: `Index`
>        - {% icon param-repeat %} *"Insert Dataset"*
>            - {% icon param-file %} *"Input Dataset"*: `contigs2bin` (output of **Converts genome bins in fasta format** of MaxBin2 {% icon tool %})
>            - *"Label to use"*: `Index`
>        - {% icon param-repeat %} *"Insert Dataset"*
>            - {% icon param-file %} *"Input Dataset"*: `contigs2bin` (output of **Converts genome bins in fasta format** of CONCOCT {% icon tool %})
>            - *"Label to use"*: `Index`
>
{: .hands_on}

> <hands-on-title> Refine with Binette </hands-on-title>
>
> 1. {% tool [Binette](toolshed.g2.bx.psu.edu/repos/iuc/binette/binette/1.2.0+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Input contig table"*: `output` (output of **Build list** {% icon tool %})
>    - {% icon param-collection %} *"Input contig file"*: `output` (Input dataset collection)
>    - *"Select if database should be used either via file or cached database"*: `cached database`
>
{: .hands_on}

> <question-title>Bin refinement</question-title>
>
> 1. How many bins are left after refinement ?
>
> > <solution-title></solution-title>
> >
> > 1. Two bins are left. Most contigs from different bins where combined into one bin. There is still one single contig bin left.
> >
> {: .solution}
>
{: .question}

# Checking the quality of the bins

Once binning is done, it is important to check its quality.

Binning results can be evaluated with **CheckM** ({%cite Parks2015%}). CheckM is a software tool used in metagenomics binning to assess the completeness and contamination of genome bins. 

CheckM compares the genome bins to a set of universal single-copy marker genes that are present in nearly all bacterial and archaeal genomes. By identifying the presence or absence of these marker genes in the bins, CheckM can estimate the completeness of each genome bin (i.e., the percentage of the total set of universal single-copy marker genes that are present in the bin) and the degree of contamination (i.e., the percentage of marker genes that are found in more than one bin).

This information can be used to evaluate the quality of genome bins and to select high-quality bins for further analysis, such as genome annotation and comparative genomics. CheckM is widely used in metagenomics research and has been shown to be an effective tool for assessing the quality of genome bins. Some of the key functionalities of CheckM are:

- *Estimation of genome completeness*: CheckM uses a set of universal single-copy marker genes to estimate the completeness of genome bins. The completeness score indicates the proportion of these marker genes that are present in the bin, providing an estimate of how much of the genome has been recovered.

- *Estimation of genome contamination*: CheckM uses the same set of marker genes to estimate the degree of contamination in genome bins. The contamination score indicates the proportion of marker genes that are present in multiple bins, suggesting that the genome bin may contain DNA from more than one organism.

- *Identification of potential misassemblies*: CheckM can identify potential misassemblies in genome bins based on the distribution of marker genes across the genome.

- *Visualization of results*: CheckM can generate various plots and tables to visualize the completeness, contamination, and other quality metrics for genome bins, making it easier to interpret the results.

- *Taxonomic classification*: CheckM can also be used to classify genome bins taxonomically based on the presence of specific marker genes associated with different taxonomic groups.

Based on the previous analysis we will use **CheckM lineage_wf**: *Assessing the completeness and contamination of genome bins using lineage-specific marker sets*

`CheckM lineage_wf` is a specific workflow within the CheckM software tool that is used for taxonomic classification of genome bins based on their marker gene content. This workflow uses a reference database of marker genes and taxonomic information to classify the genome bins at different taxonomic levels, from domain to species.

Now you can investigate the completeness and contamination of any of your previously generated genome bins as well as the refined set.

> <hands-on-title>Assessing the completeness and contamination of genome bins using lineage-specific marker sets with `CheckM lineage_wf`</hands-on-title>
> 1.  {% tool [CheckM lineage_wf](toolshed.g2.bx.psu.edu/repos/iuc/checkm_lineage_wf/checkm_lineage_wf/1.2.0+galaxy0) %} with parameters:
>     - *"Bins"*: `Folder containing the produced bins`
{: .hands_on}


The output of "CheckM lineage_wf" includes several files and tables that provide information about the taxonomic classification and quality assessment of genome bins. Here are some of the key outputs:

- **CheckM Lineage Workflow Output Report (Bin statistics)**: This report provides a summary of the quality assessment performed by CheckM. It includes statistics such as the number of genomes analyzed, their completeness, contamination, and other quality metrics.

- **Lineage-specific Quality Assessment**: CheckM generates lineage-specific quality assessment files for each analyzed genome. These files contain detailed information about the completeness and contamination of the genome based on its taxonomic lineage.

- **Marker Set Analysis**: CheckM uses a set of marker genes to estimate genome completeness and contamination. The tool produces marker-specific analysis files that provide details on the presence, absence, and copy number of each marker gene in the analyzed genomes.

- **Visualizations**: CheckM generates various visualizations to aid in the interpretation of the results. These include plots such as the lineage-specific completeness and contamination plots, scatter plots, and other visual representations of the data.

- **Tables and Data Files**: CheckM generates tabular data files that contain detailed information about the analyzed genomes, including their names, taxonomic assignments, completeness scores, contamination scores, and other relevant metrics. These files are useful for further downstream analysis or data manipulation.

It should be noted that "CheckM lineage_wf" offers a range of optional outputs that can be generated to provide additional information to the user.

To keep it simple we will check the bin statistics to investigate the performance of our previous binning efforts.

> <question-title>Binning performance</question-title>
>
> 1. Which binner created the best bins?
> 2. How well did the bin refinement work?
>
> > <solution-title></solution-title>
> >
> > **MetaBAT2**
> >
> > | Bin ID | Marker lineage        | # genomes | # markers | # marker sets | 0  | 1  | 2 | 3 | 4 | 5+ | Completeness | Contamination | Strain heterogeneity |
> > |--------|------------------------|-----------|------------|----------------|----|----|----|----|----|----|--------------|---------------|-----------------------|
> > | 1      | k__Bacteria (UID203)   | 5449      | 103        | 58             | 89 | 14 | 0  | 0  | 0  | 0  | 15.67        | 0.00          | 0.00                  |
> >
> > **SemiBin**
> >
> > | Bin ID     | Marker lineage        | # genomes | # markers | # marker sets | 0  | 1  | 2 | 3 | 4 | 5+ | Completeness | Contamination | Strain heterogeneity |
> > |------------|------------------------|-----------|------------|----------------|----|----|----|----|----|----|--------------|---------------|-----------------------|
> > | SemiBin_0  | k__Bacteria (UID203)   | 5449      | 103        | 58             | 89 | 14 | 0  | 0  | 0  | 0  | 15.67        | 0.00          | 0.00                  |
> >
> > **MaxBin**
> >
> > | Bin ID | Marker lineage        | # genomes | # markers | # marker sets | 0  | 1  | 2 | 3 | 4 | 5+ | Completeness | Contamination | Strain heterogeneity |
> > |--------|------------------------|-----------|------------|----------------|----|----|----|----|----|----|--------------|---------------|-----------------------|
> > | 001    | k__Bacteria (UID203)   | 5449      | 103        | 58             | 92 | 11 | 0  | 0  | 0  | 0  | 10.50        | 0.00          | 0.00                  |
> > | 002    | k__Bacteria (UID203)   | 5449      | 103        | 58             | 99 | 4  | 0  | 0  | 0  | 0  | 6.90         | 0.00          | 0.00                  |
> >
> > **CONCOCT**
> >
> > | Bin ID | Marker lineage         | # genomes | # markers | # marker sets | 0  | 1  | 2 | 3 | 4 | 5+ | Completeness | Contamination | Strain heterogeneity |
> > |--------|-------------------------|-----------|------------|----------------|----|----|----|----|----|----|--------------|---------------|-----------------------|
> > | 0      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 1      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 2      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 3      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 4      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 5      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 6      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 7      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 8      | root (UID1)             | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> > | 9      | k__Bacteria (UID203)    | 5449      | 103        | 58             | 89 | 14 | 0  | 0  | 0  | 0  | 15.67        | 0.00          | 0.00                  |
> >
> > **Binette**
> >
> > | Bin ID        | Marker lineage        | # genomes | # markers | # marker sets | 0  | 1  | 2 | 3 | 4 | 5+ | Completeness | Contamination | Strain heterogeneity |
> > |---------------|------------------------|-----------|------------|----------------|----|----|----|----|----|----|--------------|---------------|-----------------------|
> > | binette_bin1  | k__Bacteria (UID203)   | 5449      | 103        | 58             | 89 | 14 | 0  | 0  | 0  | 0  | 15.67        | 0.00          | 0.00                  |
> > | binette_bin2  | root (UID1)            | 5656      | 56         | 24             | 56 | 0  | 0  | 0  | 0  | 0  | 0.00         | 0.00          | 0.00                  |
> >
> > **MetaBAT2** and **SemiBin** generated the best‐performing bins in this dataset, each recovering one bin with ~15.7% completeness and no detectable contamination. 
> >
> > **MaxBin** produced bins of lower completeness (10.5% and 6.9%), but still without contamination. 
> >
> > **CONCOCT** largely failed to recover meaningful bins in this dataset: most bins show 0% completeness. This often occurs with CONCOCT on small assemblies or uneven coverage, because its Gaussian clustering model struggles when the number of contigs is low or the variation in coverage is insufficient.
> >
> > **Binette** produced one bin essentially identical in quality to the MetaBAT2/SemiBin bins (~15.7% completeness), plus a root-level bin with no completeness. Binette therefore could recover the most completely bin sucessful. The additional
loq quality bin, would have normally be filtered by Binette if the `Set minimus completeness` parameter would be set to a reasonable values, often > 75 % for true biological data.
> >
> {: .solution}
>
{: .question}

> <comment-title>CheckM2</comment-title>
> CheckM2 ({%cite Chklovski2023CheckM2%}) is the successor of CheckM, but CheckM is still widely used, since its marker-based logic can be more interpretable in a biological sense. E.g. to date (2025-11-21) NCBI still allows to submit MAGs to genbank if either checkM or checkM2 has a completeness of > 90% (see the [NCBI WGS/MAG submission guidelines](https://www.ncbi.nlm.nih.gov/genbank/wgsfaq/#metagen)).
>
> **Key differences compared to CheckM1**:
>
> * CheckM1 relies primarily on lineage-specific single-copy marker genes to estimate completeness and contamination of microbial genomes.
> * CheckM2 uses a machine-learning (gradient boost / ML) approach trained on simulated and experimental genomes, and does *not* strictly require a well-represented lineage in its marker database. 
> * CheckM2 is reported to be more accurate and faster for both bacterial and archaeal lineages, especially when dealing with novel or very reduced-genome lineages (e.g., candidate phyla, CPR/DPANN) where classical marker-gene methods may struggle. 
> * The database of CheckM2 can be updated more rapidly with new high-quality reference genomes, which supports scalability and improved performance over time.
>
> If you’re working with MAGs from under-represented taxa (novel lineages) or very small genomes (streamlined bacteria/archaea), CheckM2 tends to give more reliable estimates of completeness/contamination. For more “standard” microbial genomes from well-studied taxa, CheckM1 may still work well, but you may benefit from the improved performance with CheckM2.
>
{: .comment}

# De-replication

De-replication is the process of identifying sets of genomes that are the "same" in a list of genomes, and removing all but the “best” genome from each redundant set. How similar genomes need to be to be considered “same”, how to determine which genome is “best”, and other important decisions are discussed in [Important Concepts](https://drep.readthedocs.io/en/latest/choosing_parameters.html).

A common use for genome de-replication is the case of individual assembly of metagenomic data. If metagenomic samples are collected in a series, a common way to assemble the short reads is with a “co-assembly”. That is, combining the reads from all samples and assembling them together. The problem with this is assembling similar strains together can severely fragment assemblies, precluding recovery of a good genome bin. An alternative option is to assemble each sample separately, and then “de-replicate” the bins from each assembly to make a final genome set.

![Image shows the process of individual assembly on two strains and five samples, after individual assembly of samples two samples are chosen for de-replication process. In parallel, co-assembly on all five samples is performed](./individual-assembly.png "Individual assembly followed by de-replication vs co-assembly"){:width="80%"}

Several tools have been designed for the proccess of de-replication. **`dRep`** is a software tool designed for the dereplication of genomes in metagenomic datasets. The goal is to retain a representative set of genomes to improve downstream analyses, such as taxonomic profiling and functional annotation.

An typical workflow of how `dRep` works for dereplication in metagenomics includes:

- *Genome Comparison*: `dRep` uses a pairwise genome comparison approach to assess the similarity between genomes in a given metagenomic dataset.

- *Clustering*: Based on the genome similarities, `dRep` performs clustering to group similar genomes into "genome clusters." Each cluster represents a group of closely related genomes.

- *Genome Quality Assessment*: `dRep` evaluates the quality of each genome within a cluster. It considers factors such as completeness, contamination, and strain heterogeneity.

- *Genome Selection*: Within each genome cluster, `dRep` selects a representative genome based on user-defined criteria. This representative genome is considered as the "dereplicated" version of the cluster.

- *Dereplication Output*: The output of `dRep` includes information about the dereplicated genomes, including their identity, completeness, and contamination. The user can choose a threshold for genome similarity to control the level of dereplication.

> <hands-on-title>General list of actions for de-replication</hands-on-title>
> 1. Create new history
> 2. Assemble each sample separately using your favorite assembler
> 3. Perform a co-assembly to catch low-abundance microbes
> 4. Bin each assembly separately using your favorite binner
> 5. Bin co-assembly using your favorite binner
> 6. Pull the bins from all assemblies together
> 7. Run **`dRep`** on them
> 8. Perform downstream analysis on the de-replicated genome list
>
{: .hands_on}

# Conclusions

In summary, this tutorial shows a step-by-step on how to bin metagenomic contigs using various Binners, including Bin refinement.

It is critical to select the appropriate binning tool for a specific metagenomics study, as different binning methods may have different strengths and limitations depending on the type of metagenomic data being analyzed. By comparing the outcomes of several binning techniques, researchers can increase the precision and accuracy of genome binning.

There are various binning methods available for metagenomic data, including reference-based, clustering-based, hybrid approaches, and machine learning. Each method has its advantages and disadvantages, and the selection of the appropriate method depends on the research question and the characteristics of the data.

Comparing the outcomes of multiple binning methods can help to identify the most accurate and reliable method for a specific study. This can be done by evaluating the quality of the resulting bins in terms of completeness, contamination, and strain heterogeneity, as well as by comparing the composition and functional profiles of the identified genomes.

Overall, by carefully selecting and comparing binning methods, researchers can improve the quality and reliability of genome bins, which can ultimately lead to a better understanding of the functional and ecological roles of microbial communities in various environments.