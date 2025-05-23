---
layout: tutorial_hands_on

title: Genome-wide alternative splicing analysis
level: Advanced
zenodo_link: 'https://zenodo.org/records/13692977'
tags:
    - alternative splicing
    - isoform switching
questions:
- Are there statistically significant differences in gene splicing patterns between samples?
- Are there significant isoform switching events between the experimental conditions?
objectives:
- Perform genome-wide isoform analysis in order to evaluate differences in expression profiles between two conditions
- Evaluate the differential alternative splicing in specific genes
time_estimation: 3H
key_points:
- Before starting RNA-seq data analysis, it is important to evaluate the quality of the samples by using RSeQC.
- StringTie is a powerfull tool for gene isoform quantification and isoform switch analysis.
- IsoformSwitchAnalyzeR allows to evaluate the statistical significance of isoform switch events in different conditions.
contributions:
  authorship:
    - gallardoalba
    - lldelisle
  editing:
    - pavanvidem
abbreviations:
    AS: alternative splicing
    IS: isoform switching
    TSGs: tumor supression genes
    OCGs: oncogenes

---


Discovered over 40 years ago, {AS} formed a large part of the puzzle explaining how proteomic complexity can be achieved with a limited set of genes  ({% cite Alt1980 %}). The majority of eukaryote genes have multiple transcriptional isoforms, and recent data indicate that each transcript of protein-coding genes contain 11 exons and produce 5.4 mRNAs on average ({% cite Piovesan2016 %}). In humans,  approximately 95% of multi-exon genes show evidence of {AS} and approximately 60% of genes have at least one alternative transcription start site, some of which exert antagonistic functions ({% cite Carninci2006 %}, {% cite Miura2012 %}). Its regulation is essential for providing cells and tissues their specific features, and for their response to environmental changes ({% cite Wang2008 %}, {% cite Kalsotra2011 %}).

Alterations in gene splicing has been demonstrated to have significant impact on cancer development, and multiple evidences indicate that its disruption can exhibit effects on virtually all aspect of tumor progression ({% cite Namba2021 %}, {% cite Bonnal2020 %}). For instance, the unannotated isoform of TNS3 was found to be a novel driver of breast cancer ({% cite Namba2021 %}).

![Figure 01. Examples of cancer splicing dysregulation on cancer hallmarks](../../images/differential_isoform/differential_cancer.png "Effect of alternative splicing dysregulation on cancer progression. The diagram depicts various cancer hallmarks and examples of genes whose splicing dysregulation has been demostrated to be implicated in the corresponding functional modification.")

Disregulation in {AS} can lead to the activation of {OCGs} or the inactivation of {TSGs}, which can promote or suppress tumorigenesis, respectively ({% cite Bashyam2019 %}). However, the strict classification of cancer genes as either {OCGs} or {TSGs} may be an oversimplification, as some genes can exhibit a dual role in cancer development, often impacting the same facet of tumorigenesis ({% cite Bashyam2019 %}). One of the mechanism that has been proposed to partially explain this apparently contradictory effect is the differential usage of isoforms, often referred to as {IS}. This regulatory phenomenon has been demonstrated to have a substantial biological impact in a diverse range of biological contexts, caused by functional diversity potential of the different isoforms ({% cite VittingSeerup2017 %}).

In this tutorial, we aim to perform a genome-wide analysis of {IS} driven by an oncogene fusion gene EWS-FLI1, with the objective of identifying genes of potential clinical relevance and gene regulatory networks on genome-scale.

> <agenda-title></agenda-title>
>
> In this tutorial, we will cover:
>
> 1. TOC
> {:toc}
>
{: .agenda}


# Background on data

Ewing sarcoma is a bone and soft tissue cancer mainly affecting children, adolescents and young adults ({% cite Ewing1921 %}). In most of cases, this sarcoma is induced by a gene fusion between EWRS1 (a RNA-binding protein) and FLI1 (a transcription factor of the ETS family) ({% cite Delattre1992 %}). In order to study the role of this fusion oncogene, Carrilo and colleagues has developed a cell line, derived from an Ewing sarcoma tumor carrying EWS-FLI1 fusion, where the fusion oncoprotein can be knocked-down using doxycycline ({% cite Carrillo2007 %}). In this tutorial, we will use datasets produced by Saulnier and colleagues to study the role of EWS-FLI1 in alternative splicing ({% cite Saulnier2021 %}). They performed RNA sequencing of the inducible cell line after 7 days of doxycycline treatment where EWS-FLI1 is depleted and compared them to RNA sequencing of the same cell line without doxycycline treatment.

The dataset consists of twelve FASTQ files, generated through the Illumina HiSeq 2500 sequencing machine. Three of the samples were obtained from A673/TR/shEF cancer cell line without treatment, and the rest from the same cell line where the EWS-FLI1 fusion oncoprotein is depleted (doxycycline treated). For this training we will use a reduced set of reads enriched in reads mapping on chr10, in order to speed up the analysis. The original dataset is available in the NCBI SRA database, with the accession number PRJNA521683.

## Get data

The first step of our analysis consists of retrieving the paired-end RNA-seq datasets from Zenodo and organizing them into a collection.

> <hands-on-title>Retrieve mRNA-Seq datasets</hands-on-title>
>
> 1. Create a new history for this tutorial
> 2. Import the files from Zenodo:
>
>    - Open the file {% icon galaxy-upload %} __upload__ menu
>    - Click on __Rule-based__ tab
>    - *"Upload data as"*: `Collection(s)`
>    - Copy the following tabular data, paste it into the textbox and press <kbd>Build</kbd>
>
>      ```
>      ASP14_1	{{ page.zenodo_link }}/files/ASP14_1_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_1	{{ page.zenodo_link }}/files/ASP14_1_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ASP14_2	{{ page.zenodo_link }}/files/ASP14_2_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_2	{{ page.zenodo_link }}/files/ASP14_2_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ASP14_3	{{ page.zenodo_link }}/files/ASP14_3_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_3	{{ page.zenodo_link }}/files/ASP14_3_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ASP14_doxycycline_1	{{ page.zenodo_link }}/files/ASP14_doxycycline_1_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_doxycycline_1	{{ page.zenodo_link }}/files/ASP14_doxycycline_1_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ASP14_doxycycline_2	{{ page.zenodo_link }}/files/ASP14_doxycycline_2_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_doxycycline_2	{{ page.zenodo_link }}/files/ASP14_doxycycline_2_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ASP14_doxycycline_3	{{ page.zenodo_link }}/files/ASP14_doxycycline_3_chr10enriched_R1.fastq.gz	fastqsanger.gz	1
>      ASP14_doxycycline_3	{{ page.zenodo_link }}/files/ASP14_doxycycline_3_chr10enriched_R2.fastq.gz	fastqsanger.gz	2
>      ```
>
>    - From **Rules** menu select `Add / Modify Column Definitions`
>       - Click `Add Definition` button and select `List Identifier(s)`: column `A`
>
>         > <tip-title>Can't find <i>List Identifier</i>?</tip-title>
>         > Then you've chosen to upload as a 'dataset' and not a 'collection'. Close the upload menu, and restart the process, making sure you check *Upload data as*: **Collection(s)**
>         {: .tip}
>
>       - Click `Add Definition` button and select `URL`: column `B`
>       - Click `Add Definition` button and select `Type`: column `C`
>       - Click `Add Definition` button and select `Pair-end Indicator`: column `D`
>
>    - Click `Apply`, give a name to your collection like `All samples`  and press <kbd>Upload</kbd>
>
{: .hands_on}

Next we will retrieve the remaining datasets.

> <hands-on-title>Retrieve the additional datasets</hands-on-title>
>
> 1. Import the files from Zenodo:
>
>    - Open the file {% icon galaxy-upload %} __upload__ menu
>    - Click on __Rule-based__ tab
>    - *"Upload data as"*: `Datasets`
>    - Once again, copy the tabular data, paste it into the textbox and press <kbd>Build</kbd>
>
>      ```
>      CPAT_header.tab  {{ page.zenodo_link }}/files/CPAT_header.tab
>      gencode.hg19.chr10.gtf	{{ page.zenodo_link }}/files/gencode.v19.hg19.chr10.gtf
>      hg19.chr10.fasta	{{ page.zenodo_link }}/files/hg19.chr10.fasta
>      active_site.dat.gz	https://ftp.ebi.ac.uk/pub/databases/Pfam/releases/Pfam37.0/active_site.dat.gz
>      Pfam-A.hmm.dat.gz	https://ftp.ebi.ac.uk/pub/databases/Pfam/releases/Pfam37.0/Pfam-A.hmm.dat.gz
>      Pfam-A.hmm.gz	https://ftp.ebi.ac.uk/pub/databases/Pfam/releases/Pfam37.0/Pfam-A.hmm.gz
>      ```
>
>    - From **Rules** menu select `Add / Modify Column Definitions`
>       - Click `Add Definition` button and select `Name`: column `A`
>       - Click `Add Definition` button and select `URL`: column `B`
>    - Click `Apply` and press <kbd>Upload</kbd>
>
>
{: .hands_on}

<!--
> <details-title>Dataset subsampling</details-title>
>
> As indicated above, for this tutorial the depth of the samples was reduced in order to speed up the time needed to carry out the analysis. This was done as follows, all reads mapping to chromosome 10 were kept and a random fraction of 0.003 were added.
{: .details}
-->

# Quality assessment

Once we have got the datasets, we can start with the analysis. The first step is to perform the quality assessment. Since this step is deeply covered in the tutorial [Quality control]({% link topics/sequence-analysis/tutorials/quality-control/tutorial.md %}), we won't describe this section in detail.


> <comment-title>Initial quality evaluation (OPTIONAL)</comment-title>
>
>   For the initial quality evaluation, it is necessary to perform a pre-processing step consisting in flattening the list of pairs into a simple list; this step is required because of issues between **FastQC** and **MultiQC**. The `Raw data QC` files generated by **FastQC** will be combine into a a single one by making use of **MultiQC**.
>
>   > <hands-on-title>Initial quality evaluation</hands-on-title>
>   >
>   > 1. {% tool [Flatten collection](__FLATTEN__) %} with the following parameters convert the list of pairs into a simple list:
>   >     - *"Input Collection"*: `All samples`
>   >
>   > 2. {% tool [FastQC](toolshed.g2.bx.psu.edu/repos/devteam/fastqc/fastqc/0.74+galaxy1) %} with the following parameters:
>   >    - {% icon param-collection %} *"Raw read data from your current history"*: Output of **Flatten collection** {% icon tool %} selected as **Dataset collection**
>   >
>   >    {% snippet faqs/galaxy/tools_select_collection.md %}
>   >
>   > 3. {% tool [MultiQC](toolshed.g2.bx.psu.edu/repos/iuc/multiqc/multiqc/1.11+galaxy1) %} with the following parameters:
>   >    - In *"Results"*:
>   >        - {% icon param-repeat %} *"Insert Results"*
>   >            - *"Which tool was used generate logs?"*: `FastQC`
>   >                - In *"FastQC output"*:
>   >                    - {% icon param-repeat %} *"Insert FastQC output"*
>   >                        - {% icon param-collection %} *"FastQC output"*: `FastQC on collection: Raw data` (output of **FastQC** {% icon tool %})
>   >    - *"Report title"*: `Raw data QC`
>   >
>   >
>   {: .hands_on}
>
>
>   Let's evaluate the per base sequence quality and the adapter content.
>
>   ![Figure 02. MultiQC initial QC report](../../images/differential_isoform/fastqc_per_base_sequence_quality.png "MultiQC aggregated reports. Mean quality scores.")
>
>   As we can appreciate in the figure the per base quality of all reads seems to be very good, with values over 30 in all cases. In addition, according the report no samples werefound with adapter contamination higher than 1%.
>
{: .comment}


## Read pre-processing with fastp

In order to remove the adaptors we will make use of **fastp**, which is able to detect the adapter sequence by performing a per-read overlap analysis, so we won't even need to specify the adapter sequences.

> <hands-on-title>Pre-process reads with fastp</hands-on-title>
>
> 1. {% tool [fastp](toolshed.g2.bx.psu.edu/repos/iuc/fastp/fastp/0.23.4+galaxy1) %} with the following parameters:
>    - *"Single-end or paired reads"*: `Paired Collection`
>        - {% icon param-collection %} *"Select paired collection(s)"*: `All samples`
>    - In *"Filter Options"*:
>        - In *"Quality filtering options"*:
>            - *"Qualified quality phred"*: `20`
>    - In *"Output Options"*:
>        - *"Output JSON report"*: `Yes`
> 2. Rename the output `fastp on collection X: Paired-end output` as `Trimmed samples`
> 3. {% tool [MultiQC](toolshed.g2.bx.psu.edu/repos/iuc/multiqc/multiqc/1.11+galaxy1) %} with the following parameters:
>    - In *"Results"*:
>        - {% icon param-repeat %} *"Insert Results"*
>            - *"Which tool was used generate logs?"*: `fastp`
>                - {% icon param-collection %} *"Output of fastp"*: `fastp on collection XXX: JSON report` (output of **fastp** {% icon tool %})
>
>    > <question-title></question-title>
>    >
>    > 1. What proportion of reads were written in output file?
>    > 2. What is the average insert size?
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. All reads passed the filters (see %PF in the general statistics).
>    >    >
>    >    > 2. The average insert size is about 145 for 5 samples and 135 for ASP14_2.
>    >    >
>    >    {: .solution}
>    {: .question}
{: .hands_on}

# RNA-seq mapping and evaluation

Mapping is crucial in genome-guided-based isoform analysis as it allows for the accurate identification and quantification of RNA isoforms present in a sample. That section makes use of two main tools: **RNA STAR**, considered a state-of-the-art mapping tool for RNA-seq data, and **RSeQC**, a critical tool in isoform analysis that provides comprehensive quality assessment metrics and analyses to evaluate the reliability and integrity of RNA-seq data.

> <comment-title>Transcriptome-reconstruction approaches</comment-title>
>
>  The different methods for estimating transcript/isoform abundance can be classified depending on two main requirements: reference sequence and alignment. Reference-guided transcriptome assembly strategy requires to aligning sequencing reads to a reference genome first, and then assembling overlapping alignments into transcripts. In contrast, *de novo* transcriptome assembly methods directly reconstructs overlapping reads into transcripts by utilising the redundancy of sequencing reads themselves ({% cite Lu2013 %}).
>
{: .comment}

## RNA-seq mapping with **RNA STAR**

**RNA STAR** is a splice-aware RNA-seq alignment tool that allows to identify canonical and non-canonical splice junctions by making use of sequential maximum mappable seed search in uncompressed suffix arrays followed by seed clustering and stitching procedure ({% cite Krianovi2017 %}). One advantage of **RNA STAR** with respect to other tools is that it includes a feature called *two-pass mode*, a framework in which splice junctions are separately discovered and quantified, allowing robustly and accurately identify  splice junction patterns for differential splicing analysis and variant discovery.

> <comment-title>RNA STAR two-pass mode</comment-title>
>
> During two-pass mode splice junctions are discovered in a first alignment pass with high stringency, and are used as annotation in a second pass to permit lower stringency alignment, and therefore higher sensitivity (fig. 3). Two-pass alignment enables sequence reads to span novel splice junctions by fewer nucleotides, conferring greater read depth and providing significantly more accurate quantification of novel splice junctions that one-pass alignment ({% cite Veeneman2015 %}).
>
> ![Figure 03. RNA STAR twopass mode](../../images/differential_isoform/RNASTAR_twopass_mode.png "Two-pass alignment flowchart. Center and right, stepwise progression of two-pass alignment. First, the genome is indexed with gene annotation. Next, novel splice junctions are discovered from RNA sequencing data at a relatively high stringency (12 nt minimum spanning length). Third, these discovered splice junctions, and expressed annotated splice junctions are used to re-index the genome. Finally, alignment is performed a second time, quantifying novel and annotated splice junctions using the same, relatively lower stringency (3 nt minimum spanning length), producing splice junction expression. Source: Veeneman et al., 2016.")
>
{: .comment}

The choice of **RNA STAR** as mapper is also determined by the sequencing technology; it has been demonstrated adequate for short-read sequencing data, but when using long-read data, such as PacBio or ONT reads, it is recommended to use **Minimap2** as alignment tool.

> <comment-title>Inference of intron size (optional)</comment-title>
>
> If you are interested in analyzing non-human samples, it is a good idea to infer the **minimium and maximum intron sizes** before running **RNA STAR**.
>
> > <hands-on-title>Inference of intron size </hands-on-title>
> >
> > 1. {% tool [Convert GTF to BED12](toolshed.g2.bx.psu.edu/repos/iuc/gtftobed12/gtftobed12/357) %} with the following parameters:
> >    - {% icon param-file %} *"GTF File to convert"*: `gencode.hg19.chr10.gtf`
> >    - *"Advanced options"*: `Set advanced options`
> >        - *"Ignore groups without exons"*: `Yes`
> >
> > 2. Rename the output as `BED12 annotation`
> >
> > 3. {% tool [Gene BED To Exon/Intron/Codon BED](gene2exon1) %} with the following parameters:
> >    - *"Extract"*: `Introns`
> >        - {% icon param-file %} *"from"*: `BED12 annotation`
> >
> > 4. {% tool [Compute](toolshed.g2.bx.psu.edu/repos/devteam/column_maker/Add_a_column1/2.0) %} on rows with the following parameters:
> >   - {% icon param-file %} *"File to process"*: output of **Gene BED To Exon/Intron/Codon BED** {% icon tool %}
> >   - For "*1: Expressions*":
> >     - *"Add expression"*: `c3-c2`
> >     - *"Mode of the operation"*: `Append`
> >
> > 5. {% tool [Sort](sort1) %} data in ascending or descending order with the following parameters:
> >   - {% icon param-file %} *"Sort Dataset"*: output of **Compute on rows** {% icon tool %}
> >   - "*on column*": `Column: 7`
> >   - "*everything in*": `Descending order`
> >
> > 6. Instead of taking the min value, we will use a quantile approach. We will get the 1/10000 quantile using {% tool [Text reformatting](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_awk_tool/9.3+galaxy1) %} with awk with the following parameters:
> >   - {% icon param-file %} *"File to process"*: output of **Sort** {% icon tool %}
> >   - "*AWK Program*": `{all[NR] = $7} END{print all[int(NR*0.9999 - 0.5)]}`
> >
> > 7. Rename the output as `Minimum intron size`
> >
> > 8. Instead of taking the max value, we will use a quantile approach. We will get the 1/1000 quantile using {% tool [Text reformatting](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_awk_tool/9.3+galaxy1) %} with awk with the following parameters:
> >   - {% icon param-file %} *"File to process"*: output of **Sort** {% icon tool %}
> >   - "*AWK Program*": `{all[NR] = $7} END{print all[int(NR*0.001 - 0.5)]}`
> >
> > 9. Rename the output as `Maximum intron size`
> >
> > > <question-title></question-title>
> > >
> > > 1. What is the minimum intron size you got, does it makes sense?
> > > 2. What is the maximum intron size you got?
> > >
> > > > <solution-title></solution-title>
> > > >
> > > > 1. You may have found 1 as minimum intron size. Indeed, it seems that some annotated introns are 1bp long. This does not really make sense biologically. Piovesan and colleague have shown that no eukaryotic introns were found below 30bp ({% cite Piovesan2015 %}).
> > > >
> > > > 2. You may have found 316825.
> > > >
> > > {: .solution}
> > {: .question}
> {: .hands_on}
>
{: .comment}

Now we can perform the mapping step.

> <comment-title>ENCODE options</comment-title>
>
> In this training, for **RNA STAR**, we adopted the parameters from the [ENCODE pipeline](https://www.encodeproject.org/pipelines/), developed by the ENCODE RNA Working Group. It is **specially designed for the human genome**. If your samples belong to a different organism, probably you will need to adapt the configuration a bit. You can find more information in the [**RNA STAR** manual](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf).
>
{: .comment}

> <hands-on-title> Generate intermediate file with RNA STAR</hands-on-title>
>
> 1. {% tool [RNA STAR](toolshed.g2.bx.psu.edu/repos/iuc/rgrnastar/rna_star/2.7.11a+galaxy0) %} with the following parameters:
>    - *"Single-end or paired-end reads"*: `Paired-end (as collection)`
>        - {% icon param-collection %} *"RNA-Seq FASTQ/FASTA paired reads"*: `Trimmed samples` (output of **fastp** {% icon tool %})
>    - *"Custom or built-in reference genome"*: `Use reference genome from history and create temporary index`
>        - {% icon param-file %} *"Select a reference genome"*: `hg19.chr10.fasta`
>        - *"Build index with or without known splice junctions annotation"*: `build index with gene-model`
>            - {% icon param-file %} *"Gene model (gff3,gtf) file for splice junctions"*: `gencode.hg19.chr10.gtf`
>            - *"Length of the genomic sequence around annotated junctions*": `100`
>            - *"Per gene/transcript output*": `Per gene read counts (GeneCounts)`
>    - *"Would you like to set additional output filters?"*: `Yes`
>        - *"Would you like to keep only reads that contain junctions that passed filtering?*": `Yes` (ENCODE option, reduces the number of ”spurious” junctions)
>        - *"Maximum number of alignments to output a read's alignment results, plus 1*": `20` (ENCODE option)
>        - *"Maximum number of mismatches to output an alignment, plus 1*": `999` (ENCODE option)
>        - *"Maximum ratio of mismatches to read length*": `0.04` (ENCODE option)
>    - In *"Algorithmic settings"*:
>        - *"Configure seed, alignment and limits options*": `Extended parameter list`
>            - In *"Alignment parameters"*:
>                - *"Minimum intron size*": `20` (ENCODE option)
>                - *"Maximum intron size*": `1000000` (ENCODE option)
>                - *"Maximum gap between two mates*": `1000000` (ENCODE option)
>                - *"Minimum overhang for spliced alignments*": `8` (ENCODE option)
>                - *"Minimum overhang for annotated spliced alignments*": `1` (ENCODE option)
>
> > <comment-title>Intron size values</comment-title>
> >
> >  Here we are using the parameters recommended by **ENCODE** for human samples. If you are working with a different organism, you can use the intron sizes computed previously.
> >
> {: .comment}
>
{: .hands_on}

The first **RNA STAR** run generates three collections: logs, alignments in BAM format, and a collection of BED files with splice junction coordinates. Now we will process the collection of BED files with the objective of getting a unique list of junctions where we remove non-canonical junctions (column 5 > 0), annotated junction sites (column 6 == 0) and junctions supported by too few reads (column 7 > 5).

> <hands-on-title> Pre-processing of splice junction coordinates</hands-on-title>
> 1. {% tool [Concatenate datasets](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_cat/9.3+galaxy1) %} tail-to-head **(cat)** with the following parameters:
>   - {% icon param-collection %} *"Datasets to concatenate"*: `RNA STAR on collection XXX: splice junctions.bed` (output of **RNA STAR** {% icon tool %})
>
>         The output must be a dataset not a collection
>
>         > <tip-title>Got a collection instead of a dataset?</tip-title>
>         > You probably selected **Concatenate datasets** tail-to-head {% icon tool %} instead of the other tool with the same name except that it has a `(cat)` at the end of the name.
>         > Simply rerun the step above with the good tool.
>         {: .tip}
>
> 2. {% tool [Filter](Filter1) %} data on any column using simple expressions:
>   - *"Filter"*: output of **Concatenate datasets** {% icon tool %}
>   - *"With following condition"*: `c5 > 0 and c6 == 0 and c7 > 5` (here `c` stands for *column*).
>
> 3. {% tool [Cut](Cut1) %} columns from a table with the following parameters:
>   - "*Cut columns*": `c1-c6`
>   - {% icon param-file %} *"From"*: output of **Filter** {% icon tool %}
>
> 4. {% tool [Sort](sort1) %} data in ascending or descending order with the following parameters:
>    - {% icon param-file %} *"Sort Dataset"*: output of **Cut** {% icon tool %}
>    - *"on column*": `Column: 1`
>    - *"with flavor"*: `Alphabetical sort`
>    - *"everything in"*: `Ascending order`
>    - {% icon param-repeat %} *"Insert Column selection"*
>        - *"on column"*: `Column: 2`
>        - *"everything in"*: `Ascending order`
>    - {% icon param-repeat %} *"Insert Column selection"*
>        - *"on column"*: `Column: 3`
>        - *"everything in"*: `Ascending order`
>
> 5. {% tool [Unique lines](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_uniq_tool/9.3+galaxy1) %} assuming sorted input file with the following parameters:
>   - {% icon param-file %} *"File to scan for unique values"*: output of **Sort** {% icon tool %}
>   - *Only print unique lines*: No
>
> 6. Rename the output as `Splicing database` and change datatype to `interval` (this was the initial datatype but the `cut` tool changed it to `tabular`.)
>
>    > <question-title></question-title>
>    >
>    > 1. In average in how many samples the new splicing events were detected?
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. The output of **Sort** {% icon tool %} has 4558 lines and the output of **Uniq** {% icon tool %} has 1608, so in average, it is present in 3 samples.
>    >    >
>    >    {: .solution}
>    {: .question}
{: .hands_on}

Finally, we will re-run **RNA STAR** in order to integrate the information about the new splicing events in the analysis.

> <hands-on-title> Sequence alignment with STAR</hands-on-title>
> 1. {% tool [RNA STAR](toolshed.g2.bx.psu.edu/repos/iuc/rgrnastar/rna_star/2.7.11a+galaxy0) %} with the following parameters:
>    - *"Single-end or paired-end reads"*: `Paired-end (as collection)`
>        - {% icon param-collection %} *"RNA-Seq FASTQ/FASTA paired reads"*: `Trimmed samples` (output of **fastp** {% icon tool %})
>    - *"Custom or built-in reference genome"*: `Use reference genome from history and create temporary index`
>        - {% icon param-file %} *"Select a reference genome"*: `hg19.chr10.fasta`
>        - *"Build index with or without known splice junctions annotation"*: `build index with gene-model`
>            - {% icon param-file %} *"Gene model (gff3,gtf) file for splice junctions"*: `gencode.hg19.chr10.gtf`
>            - *"Length of the genomic sequence around annotated junctions*": `100`
>            - *"Per gene/transcript output*": `Per gene read counts (GeneCounts)`
>    - *"Use 2-pass mapping for more sensitive novel splice junction discovery"*: `Yes, I want to use multi-sample 2-pass mapping and I have obtained splice junctions database of all samples throught previous 1-pass runs of STAR`
>       - {% icon param-file %} *"Pregenerated splice junctions datasets of your samples"*: `Splicing database`
>    - *"Would you like to set additional output filters?"*: `Yes`
>        - *"Would you like to keep only reads that contain junctions that passed filtering?*": `Yes` (ENCODE option)
>        - *"Maximum number of alignments to output a read's alignment results, plus 1*": `20` (ENCODE option)
>        - *"Maximum number of mismatches to output an alignment, plus 1*": `999` (ENCODE option)
>        - *"Maximum ratio of mismatches to read length*": `0.04` (ENCODE option)
>    - In *"Algorithmic settings"*:
>        - *"Configure seed, alignment and limits options*": `Extended parameter list`
>            -  In *"Alignment parameters"*:
>                - *"Minimum intron size*": `20` (ENCODE option)
>                - *"Maximum intron size*": `1000000` (ENCODE option)
>                - *"Maximum gap between two mates*": `1000000` (ENCODE option)
>                - *"Minimum overhang for spliced alignments*": `8` (ENCODE option)
>                - *"Minimum overhang for annotated spliced alignments*": `1` (ENCODE option)
>    - *"Compute coverage"*: `Yes in bedgraph format`
>
> 2. Rename the output `RNA STAR on collection X: mapped.bam` as `Mapped collection`
>
>
{: .hands_on}

Before moving to the transcriptome assembly and quantification step, we are going to use **RSeQC** in order to obtain some RNA-seq-specific quality control metrics.

## RNA-seq specific quality control metrics with **RSeQC**

RNA-seq-specific quality control metrics, such as sequencing depth, read distribution and coverage uniformity, are essential to ensure that the RNA-seq data are adequate for transcriptome reconstruction and {AS} analysis. For example, the use of RNA-seq with unsaturated sequencing depth gives imprecise estimations  and fails to detect low abundance splice junctions, limiting the precision of many analyses ({% cite Wang2012 %}).

In this section we will make use of of the **RSeQC** toolkit in order to generate the RNA-seq-specific quality control metrics.

We are going to use the following RSeQC modules:

- **Infer Experiment**: inference of strandedness of RNA-seq library
- **Gene Body Coverage**: compute read coverage over gene bodies
- **Junction Saturation**: check junction saturation
- **Junction Annotation**: compares detected splice junctions to a reference gene model
- **Read Distribution**: calculates how mapped reads are distributed over genome features
- **Inner Distance**: calculate the inner distance (or insert size) between two paired RNA reads

Once all required outputs have been generated, we will integrate them by using **MultiQC** in order to interpret the results.

> <hands-on-title> Raw reads QC</hands-on-title>
> 1. Most of RSeQC tools require a BED12 file with gene annotations. If you don't have a `BED12 annotation` dataset, just generate it with {% tool [Convert GTF to BED12](toolshed.g2.bx.psu.edu/repos/iuc/gtftobed12/gtftobed12/357) %} with the following parameters:
>    - {% icon param-file %} *"GTF File to convert"*: `gencode.hg19.chr10.gtf`
>    - *"Advanced options"*: `Set advanced options`
>        - *"Ignore groups without exons"*: `Yes`
>
> 2. Rename the output as `BED12 annotation`
>
> 3. {% tool [Infer Experiment](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_infer_experiment/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>
> 4. {% tool [Gene body coverage (BAM)](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_geneBody_coverage/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>
> 5. {% tool [Junction Saturation](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_junction_saturation/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM/SAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>    - *"Minimum intron length (bp)"*: `20`
>    - *"Output R-Script"*: `Yes`
>
> 6. {% tool [Junction Annotation](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_junction_annotation/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM/SAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>    - *"Minimum intron length (bp)"*: `20`
>
> 7. {% tool [Read Distribution](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_read_distribution/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM/SAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>
> 8. {% tool [Inner Distance](toolshed.g2.bx.psu.edu/repos/nilesh/rseqc/rseqc_inner_distance/5.0.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Input BAM/SAM file"*: `Mapped collection`
>    - {% icon param-file %} *"Reference gene model"*: `BED12 annotation`
>
{: .hands_on}

Now, we will integrate the outputs into **MultiQC**.

> <hands-on-title>Report integration with MultiQC</hands-on-title>
>
> 1. {% tool [MultiQC](toolshed.g2.bx.psu.edu/repos/iuc/multiqc/multiqc/1.11+galaxy1) %} with the following parameters:
>    - In *"Results"*:
>        - {% icon param-repeat %} *"Insert Results"*
>            - *"Which tool was used generate logs?"*: `STAR`
>                - In *"STAR output"*:
>                    - {% icon param-repeat %} *"Insert STAR output"*
>                        - *"Type of STAR output?"*: `Log`
>                            - {% icon param-collection %} *"STAR log output"*: `RNA STAR on collection XXX: log` collection
>                    - {% icon param-repeat %} *"Insert STAR output"*
>                        - *"Type of STAR output?"*: `Gene counts`
>                            - {% icon param-collection %} *"STAR gene counts output"*: `RNA STAR on collection XXX: reads per gene` collection
>        - {% icon param-repeat %} *"Insert Results"*
>            - *"Which tool was used generate logs?"*: `RSeQC`
>                - In *"RSeQC output"*:
>                    - {% icon param-repeat %} *"Insert RSeQC output"*
>                        - *"Type of RSeQC output?"*: `Infer experiment`
>                            - {% icon param-collection %} *"RSeQC infer experiment"*: select `Infer Experiment on collection XXX: RNA-seq experiment configuration` collection
>                    - {% icon param-repeat %} *"Insert RSeQC output"*
>                        - *"Type of RSeQC output?"*: `Read distribution`
>                            - {% icon param-collection %} *"RSeQC read distribution: stats output"*: select `Read Distribution on collection XXX: stats (TXT)` collection
>                    - {% icon param-repeat %} *"Insert RSeQC output"*
>                        - *"Type of RSeQC output?"*: `Junction saturation`
>                            - {% icon param-collection %} *"RSeQC junction saturation: junction saturation plot Rscript file*: select `Junction Saturation on collection XXX: junction saturation (Rscript)` collections
>                    - {% icon param-repeat %} *"Insert RSeQC output"*
>                        - *"Type of RSeQC output?"*: `Junction annotation`
>                            - {% icon param-collection %} *"RSeQC junction annotation: stats file"*: select `Junction Annotation on collection XXX: stats` collections
>                    - {% icon param-repeat %} *"Insert RSeQC output"*
>                        - *"Type of RSeQC output?"*: `Gene body coverage`
>                            - {% icon param-collection %} *"RSeQC gene body coverage: stats file"*: select `Gene Body Coverage (BAM) on collection XXX: stats (TXT)` collections
>                    - {% icon param-repeat %} *"Inner Distance output"*
>                        - *"Type of RSeQC output?"*: `Inner distance`
>                            - {% icon param-collection %} *"RSeQC inner distance: frequency file"*: select `Inner Distance on collection XXX: frequency (TXT)` collections
>
{: .hands_on}


First, we will evaluate the plot corresponding to the **RNA STAR** alignment scores (fig. 4), which will allow us to easily compare the samples to get an overview of the quality of the samples. As a general criteria, we can consider that good quality samples should have at least 75% of the reads uniquely mapped. In our case, of samples have unique mapping values over 90%. Most of the reads asigned to 'Unmapped: too short' are in fact mapping to other chromosomes.

![Figure 04. RNA STAR alignment stats plot](../../images/differential_isoform/star_alignment.png "RNA STAR alignment stats percentage plot. Note that STAR counts a paired-end read as one read.")

Now we can have a look at the **RSeQC** results;  we will evaluate the RSeQC **Read Distribution plot** (fig. 5).

![Figure 05. RSeQC read distribution plot](../../images/differential_isoform/rseqc_read_distribution_plot.png "RSeQC read distribution. This module will calculate how mapped reads were distributed over genome feature (like CDS exon, 5’UTR exon, 3’ UTR exon, Intron, Intergenic regions). Distribution of all features (A). Distribution of low frequency features (B).")

According the figure 5.A, all samples show show a similar trend, both in ASP14 (cancer) and ASP13_doxycycline (oncogene-depleted) samples, with most reads mapping on CDS exons (around 66%), 5'UTR (around 2-3%) and 3'UTR (around 20%). We can also evaluate the frequency of less common features by hidding the main ones; it can be done by clicking in the correspoding feature label (fig. 5B). However, we can see that the profile is consistent between replicates but is slightly different between conditions. This is not an ideal case as we cannot rule out if this is biological or technical as the library may have not been prepared at the same time.

Now we will evaluate the results of the **Infer Experiment** module, which allows to speculate the experimental design (whether sequencing is strand-specific, and if so, how reads are stranded) by sampling a subset of reads from the BAM file and comparing their genome coordinates and strands with those of the reference gene model ({% cite Wang2012 %}).

![Figure 06. RSeQC infer experiment plot](../../images/differential_isoform/rseqc_infer_experiment_plot.png "RSeQC Infer Experiment plot. It counts the percentage of reads and read pairs that match the strandedness of overlapping transcripts. It can be used to infer whether RNA-seq library preps are stranded (sense or antisense).")

As can be appreciated in the image, the proportion of reads assigned as *sense* is close to null and the ones assigned as *antisense* are around 80%, which indicates that in that case our RNA-seq data is strand specific in the reverse orientation. This can be also evaluated with the STAR Gene Counts statistics as in the Reference-based RNA-seq tutorial.

Now, let's evaluate the results generated by the **Gene Body Coverage** module. It scales all transcripts to 100 nt and calculates the number of reads covering each nucleotide position. The plot generated from this information illustrates the coverage profile along the gene body, defined as the entire gene from the transcription start site to the end of the transcript (fig. 7).

![Figure 07. RSeQC gene body coverage plot](../../images/differential_isoform/rseqc_gene_body_coverage_plot.png "RSeQC gene body coverage plot. It calculates read coverage over gene bodies. This is used to check if reads coverage is uniform and if there is any 5' or 3' bias.")

The gene body coverage pattern is highly influenced by the RNA-seq protocol, and it is useful for identifying artifacts such as 3' skew in libraries. For example, a skew towards increased 3' coverage can happen in degraded samples prepared with poly-A selection. According the figure 7, there're not big bias in our reads as a result of sequencing technical problems.

Other important metric for {AS} analysis is the one provided by the **Junction Saturation** module, which allows to determine if the current sequencing depth is sufficient to perform {AS} analyses by comparing the detected splice junctions to reference gene model (fig. 8). Since for a well annotated organism both the number of expressed genes and spliced junctions is considered to be almost fixed, we expect the number of known junctions to reach a plateau if the current sequencing depth is almost saturated for known junction detection. Using an unsaturated sequencing depth would miss many rare splice junctions ({% cite Wang2012 %}). As we can appreciate in the plot (fig. 8.A), the known junctions tend to stabilize around 8.000, which indicates that the read sequencing depth is good enough for performing the {AS} analysis.

> <comment-title>RSeQC junction saturation details</comment-title>
>
> This approach helps identify if the sequencing depth is sufficient for {AS} analysis. However, it relies on the reference gene model for junction comparison, which might not be complete or accurate for all organisms. In addition, it might not provide enough information for novel junction detection, as the sequencing depth might still be insufficient for detecting new splice junctions.
>
{: .comment}

![Figure 08. RSeQC junction saturation plot](../../images/differential_isoform/rseqc_junction_saturation_plot.png "RSeQC junction saturation. Saturation is checked by re-sampling the alignments from the BAM file, and the splice junctions from each subset is detected (green) and and compared them to the reference model (grey).")

Regarding the novel junctions (fig. 8.B), we can appreciate that the number of new splice junctions increase with the coverage.

After confirming that the saturation level is good enough, now we will check the output generated by the **RSeQC junction annotation** module; it allows to evaluate both splice junctions (multiple reads show the same splicing event) and splice events (single read level) (fig. 9).

![Figure 09. RSeQC junction annotation plot](../../images/differential_isoform/rseqc_junction_annotation_junctions_plot.png "RSeQC junction annotation. The detected junctions and events are divided in three exclusive categories: known splicing junctions (blue), partial novel splicing junction (one of the splice site is novel) (grey) and new splicing junctions (green). Splice events refer to the number of times a RNA-read is spliced (A). Splice junctions correspond to multiple splicing events spanning the same intron.")

According to the results, despite the relatively low (around 0.3%) proportion of reads supporting new (or partially new) splicing events (fig. 9.B), they represent a large proportion of junctions (fig. 9.A).

![Figure 10. RSeQC inner distance plot](../../images/differential_isoform/rseqc_inner_distance_plot.png "RSeQC inner distance. The plot represents gap sizes between R1 and R2 so the fragment size minus the size of both reads.")

Finally from the the Inner Distance plot (fig. 10), we can infer some additional information about the degree of degradation of the samples. Usually very short inner distances appear in old or degraded samples; in addition values can be negative if the two fragments overlap. In our case, we can see that most of reads overlap (this was already seen with the fastp report which is more informative) and that the average fragment size is 150bp (-50 inner distance + 100bp read 1 + 100bp read2).

After evaluating the quality of the RNA-seq data, we can start with the transcriptome assembly step.

# Transcriptome assembly, quantification and evaluation

Once the mapping is done, in this section we will use the information contained in the BAM files to carry out the assembly of the transcriptomes, as well as the quantification of the transcripts. In addition, we will evaluate the transcriptome assemblies quality.

## Reference-based transcriptome assembly and quantification with **StringTie**

**StringTie** is a fast and highly efficient assembler of RNA-seq alignments into potential transcripts. It uses a network flow algorithm to reconstruct transcripts and quantitate them simultaneously. This algorithm is combined with an assembly method to merge read pairs into full fragments in the initial phase ({% cite Kovaka2019 %}, {% cite Pertea2015 %}).

> <comment-title>StringTie algorithm</comment-title>
>
> **StringTie** first groups the reads into clusters, collapsing the reads that align to the identical location on the genome and keeping a count of how many alignments were collapsed, then creates a splice graph for each cluster from which it identifies transcripts, and then for each transcript it creates a separate flow network to estimate its expression level using a maximum flow algorithm ({% cite Pertea2015 %}) (fig. 11). The algorithm groups the reads into distinct gene loci and then assembles each locus into as many isoforms as needed to explain the data. It begins with the most highly-expressed transcript, assembling and quantitating it simultaneously. The process is repeated until all reads are used, or until the number of remaining reads is below a user-adjustable level of transcriptional noise.
>
> ![Figure 11. StringTie algorithm details](../../images/differential_isoform/stringtie_algorithm.png "Transcript assembly pipeline for StringTie. It begins with a set of RNA-seq reads that have been mapped to the genome. StringTie iteratively extracts the heaviest path from a splice graph, constructs a flow network, computes maximum flow to estimate abundance, and then updates the splice graph by removing reads that were assigned by the flow algorithm. This process repeats until all reads have been assigned. Source: Perea et al., 2015")
>
> **StringTie** uses an aggressive strategy for identifying and removing spurious spliced alignments. If a spliced read is aligned with more than 1% mismatches, keeping in mind that Illumina sequencers have an error rate < 0.5%, then **StringTie** requires 25% more reads than usual to support that particular spliced alignment. In addition, if a spliced read spans a very long intron, **StringTie** accepts that alignment only if a larger anchor of 25 bp is present on both sides of the splice site. Here the term *anchor* refers to the portion of the read aligned within the exon beginning at the exon-intron boundary ({% cite Kovaka2019 %}).
>
>
{: .comment}

Transcriptome reconstruction, which involves assembling RNA-seq reads into transcription units, is a complex computational task due to three main challenges. Firstly, gene expression levels vary widely, with some genes having low read representation, complicating their identification. Secondly, reads arise from both mature mRNA and incompletely spliced precursor RNA, making it challenging to distinguish mature transcripts. Lastly, the short length of reads coupled with the presence of multiple isoforms per gene makes it difficult to determine the specific isoform responsible for each read ({% cite Garber2011 %}).

Then, let's start with the transcriptome assemby.

> <hands-on-title>Transcriptome assembly with StringTie</hands-on-title>
>
> 1. {% tool [StringTie](toolshed.g2.bx.psu.edu/repos/iuc/stringtie/stringtie/2.2.3+galaxy0) %} with the following parameters:
>    - *"Input options"*: `Short reads`
>        - {% icon param-collection %} *"Input short mapped reads"*: `Mapped collection`
>    - *"Specify strand information"*: `Reverse (RF)`
>    - *"Use a reference file to guide assembly?"*: `Use reference GTF/GFF3`
>        - *"Reference file"*: `Use a file from history`
>            - {% icon param-file %} *"GTF/GFF3 dataset to guide assembly"*: `gencode.hg19.chr10.gtf`
>        - *"Use Reference transcripts only?"*: `No`
>    - In *"Advanced options"*:
>        - "*Minimum junction coverage*": `3`
>
> 2. Rename the generated collection as `Assembled transcripts coordinates`
>
> 3. {% tool [StringTie merge](toolshed.g2.bx.psu.edu/repos/iuc/stringtie/stringtie_merge/2.2.3+galaxy0) %} with the following parameters:
>    - {% icon param-collection %} *"Transcripts"*: `Assembled transcripts coordinates` (output of **StringTie**)
>    - {% icon param-file %} *"Reference annotation to include in the merging"*: `gencode.hg19.chr10.gtf`
>
> 4. Rename the output as `Reference transcriptome annotation`
>
>    > <question-title></question-title>
>    >
>    > 1. How many lines are there in the `Assembled transcripts coordinates`?
>    > 2. How many lines are there in the original GTF?
>    > 3. How many lines are there in the `Reference transcriptome annotation`?
>    > 4. What can you conclude?
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. There are about 25k lines.
>    >    > 2. There are about 93k lines.
>    >    > 3. There are about 70k lines.
>    >    > 4. Not much as the number of lines is not informative enough, we need an appropriate tool to compare those GTFs. However, a quick way to check why these numbers are different is to use **Count occurences of each record** on column 3 which is the feature type. This would tell you that:
>    >    >    - The original GTF has 2260 genes, 6503 transcripts and 42758 exons and other coding information.
>    >    >    - The Assembled transcripts coordinates have 2772-2940 transcripts and 21790-22858 exons and no other information, this is why the number of lines is much lower.
>    >    >    - The Reference transcriptome annotation has 8310 transcripts and 61795 exons.
>    >    >
>    >    {: .solution}
>    {: .question}
{: .hands_on}

Now, we can perform the transcriptome quantification in a more accurate way by making use of the new transcriptome annotation.

> <hands-on-title>Isoform quantification with StringTie</hands-on-title>
>
> 1. {% tool [StringTie](toolshed.g2.bx.psu.edu/repos/iuc/stringtie/stringtie/2.2.3+galaxy0) %} with the following parameters:
>    - *"Input options"*: `Short reads`
>        - {% icon param-collection %} *"Input short mapped reads"*: `Mapped collection`
>    - *"Specify strand information"*: `Reverse (RF)`
>    - *"Use a reference file to guide assembly?"*: `Use reference GTF/GFF3`
>        - *"Reference file"*: `Use a file from history`
>            - {% icon param-file %} *"GTF/GFF3 dataset to guide assembly"*: `Reference transcriptome annotation`
>        - *"Use Reference transcripts only?"*: `Yes`
>        - *"Output files for differential expression?"*: `Ballgown`
>    - In *"Advanced options"*:
>        - "*Minimum junction coverage*": `3`
>
> 2. Rename the collection `Transcript-level expression measurements` as `Transcriptomes quantification`
>
{: .hands_on}

**StringTie** generates six collection with six elements each one, but we will use only the `transcript-level expression measurements` dataset collection.

> <details-title>Transcription-level expression measurements file</details-title>
>
>  The transcript-level expression measurements (t_tab.ctab) file includes one row per transcript, with the following columns:
>
> - **t_id**: numeric transcript id
> - **chr**, **strand**, **start**, **end**: genomic location of the transcript
> - **t_name**: generated transcript id
> - **num_exons**: number of exons comprising the transcript
> - **length**: transcript length, including both exons and introns
> - **gene_id**: gene the transcript belongs to
> - **gene_name**: HUGO gene name for the transcript, if known
> - **cov**: per-base coverage for the transcript (available for each sample)
> - **FPKM**: Estimated FPKM for the transcript (available for each sample)
>
{: .details}


## Transcriptome evaluation

The evaluation of assembled transcriptomes is crucial to assess the quality and accuracy of the transcript reconstruction process. It helps researchers ensure that the assembled transcripts represent the true expression patterns and capture the full diversity of transcripts present in the biological sample.

Two commonly used tools for evaluating assembled transcriptomes are **rnaQUAST** and **GFFCompare**. **rnaAQUAST** is specifically designed for assessing the quality of RNA-seq assemblies by comparing them to a reference genome. On the other hand, **GFFCompare** is a tool used for comparing and annotating assembled transcripts against a reference annotation file.

### Annotation accuracy evaluation with **GFFCompare**

**GFFcompare** is an open-source utility designed to systematically compare one or more sets of transcript predictions to a reference annotation at different levels ({% cite Pertea2020 %}). **GFFCompare** assigns class codes to each assembled transcript, indicating their relationship to the reference transcripts, such as complete match, partial match, novel transcripts, and alternative splicing events. This information will help us in understanding the reliability and completeness of the assembled transcriptome.

> <hands-on-title>Transcriptome accuracy evaluation</hands-on-title>
>
> {% tool [GFFCompare](toolshed.g2.bx.psu.edu/repos/iuc/gffcompare/gffcompare/0.12.6+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"GTF inputs for comparison"*: `Reference transcriptome annotation` (output of **StringTie merge** {% icon tool %})
>    - *"Use reference annotation"*: `Yes`
>       - *"Choose the source for the reference annotation*": `History`
>          - *"Reference annotation*": `gencode.hg19.chr10.gtf`
{: .hands_on}

The output of GFFCompare includes several files, which can be used to analyze the results in different ways. Some of the main output files are:

- **Annotated transcripts**: contains all the input transcripts annotated with several additional attributes: **xloc**, **tss_id**, **cmp_ref**, and **class_code**.
- **Loci file**: Lists the loci (locations) of the features in the input files.
- **RefMap**: Contains the list of reference transcripts with the query transcripts which either fully or partially matches.
- **TMAP**: Lists the most closely matching reference transcript for each query transcript.
- **Accuracy stats**: Provides statistics on the comparison, including the number of input and reference features, the number of unique input features, and the total number of features.
- **Tracking file**: Contains the genomic coordinates of the input features and their matching or non-matching reference features.

> <details-title>Details on the GffCompare attributes</details-title>
>
> The **xloc** attribute indicates the super-locus to which a particular transcript belongs. The **tss_id** attribute serves as a unique identifier for the transcription start of that transcript. In cases where applicable, the **cmp_ref** attribute provides the nearest reference transcript, and the **class_code** attribute describes the relationship to this reference transcript. Additional information can be found in {% cite Pertea2020 %}. The most present class codes are: '=': complete, exact match of intron chain (TSS and TES may be different); 'c': contained in reference (intron compatible), 'k': the reference is contained, 'm': retained intron(s), all introns matched or retained; 'n': retained intron(s) with at least one new intron; 'j': multi-exon with at least one junction match
>
{: .details}

In order to have an overview of the transcriptome, we can have a look at the **sensitivity** and **precision** metrics, which can be found in the **accuracy stats** file. Sensitivity is defined as the proportion of true positives (found in prediction and in reference) among the total features in reference, while precision is defined as the proportion of true positives among the total predicted features.

> <question-title>Sensitivity and precision</question-title>
>
> 1. What are the sensitivity and precision at transcript level?
> 2. How many novel exons have been identified?
>
> > <solution-title></solution-title>
> >
> > 1. At transcript level, precision is 77% and sensitivity 99.4%. The sensitivity is always very high as we provided to Stringtie merge the original GTF. The precision gives an indication on what is the proportion of new transcript compared to the original GTF.
> > 2. According the stats file, 962 new exons (4.8%) have been identified.
> >
> {: .solution}
{: .question}

> <details-title>Sensitivity and precision on individual Assembled transcripts</details-title>
>
> It can also be of interest to use **GffCompare** on the `Assembled transcripts coordinates` to see what is the sensitivity and precision for each sample. In this mode, the sensitivity at the transcript level is about 30%. This means that only 30% of the reference transcripts are detected. The precision about 70%.
>
{: .details}

For a more in-depth analysis of the output, we could use the **TMAP** file and perform some text manipulation. For example, in order to extract the transcripts fully contained within a reference intron, we can filter for the **class_code** `i`.

> <hands-on-title>Extract novel transcripts information</hands-on-title>
>
> 1. {% tool [Filter](Filter1) %} data on any column using simple expressions:
>   - *"Filter"*: **TMAP** file
>   - *"With following condition"*: `c3=='i'`
>
>    > <question-title></question-title>
>    >
>    > 1. How many transcripts within introns have been detected?
>    > 1. How many transcripts are withing exons of ENST00000358220.1 ?
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. The output of **Filter** {% icon tool %} has 47 lines so 47 transcripts.
>    >    > 2. The first 3 lines have ENST00000358220.1 in the second columns. There are 3 transcripts.
>    >    >
>    >    {: .solution}
>    {: .question}
{: .hands_on}

It is always good to check visually the findings using the coverage. We will check if we can validate the three transcripts in the introns of ENST00000358220.1. For this, we will take advantage of the coverage files we generated with **STAR** and the annotation generated by **StringTie**, and will use a software called **pyGenomeTracks**

> <hands-on-title>Check new transcript in intron with pyGenometracks from STAR coverage</hands-on-title>
>
> 1. {% tool [pyGenomeTracks](toolshed.g2.bx.psu.edu/repos/iuc/pygenometracks/pygenomeTracks/3.8+galaxy1) %}:
>    - *"Region of the genome to limit the operation"*: `chr10:1,095,478-1,178,237`
>    - In *"Include tracks in your plot"*:
>        - {% icon param-repeat %} *"Insert Include tracks in your plot"*
>            - *"Choose style of the track"*: `Bedgraph track`
>                - *"Plot title"*: You need to leave this field empty so the title on the plot will be the sample name.
>                - {% icon param-collection %} *"Track file(s) bedgraph format"*: Select `RNA STAR on collection XXX: Coverage Uniquely mapped strand 2`.
>                - *"Color of track"*: Choose a color for sense coverage (I choose blue).
>                - *"Minimum value"*: `0`
>                - *"height"*: `3`
>                - *"Show visualization of data range"*: `Yes`
>        - {% icon param-repeat %} *"Insert Include tracks in your plot"*
>            - *"Choose style of the track"*: `Bedgraph track`
>                - *"Plot title"*: You need to leave this field empty so the title on the plot will be the sample name.
>                - {% icon param-collection %} *"Track file(s) bedgraph format"*: Select `RNA STAR on collection XXX: Coverage Uniquely mapped strand 1`.
>                - *"Color of track"*: Choose a color for antisense coverage (I choose red).
>                - *"Minimum value"*: `0`
>                - *"height"*: `3`
>                - *"Show visualization of data range"*: `Yes`
>                - *"Include spacer at the end of the track"*: `1`
>        - {% icon param-repeat %} *"Insert Include tracks in your plot"*
>            - *"Choose style of the track"*: `Gene track / Bed track`
>                - *"Plot title"*: `Genes`
>                - {% icon param-file %} *"Track file(s) bed or gtf format"*: Select `Reference transcriptome annotation`
>                - *"height"*: `5`
>                - *"Put all labels inside the plotted region"*: `Yes`
>                - *"Allow to put labels in the right margin "*: `Yes`
>        - {% icon param-repeat %} *"Insert Include tracks in your plot"*
>            - *"Choose style of the track"*: `X-axis`
>
{: .hands_on}

![Figure 12. Intronic transcript plot](../../images/differential_isoform/pgt_intronic.png "pyGenometracks isoform visualization of coordinates chr10:1,095,478-1,178,237. The plot integrates coverage information along with the annotations.")

We can see that the transcript on top (ENST00000358220.1) has three transcripts annotated antisense in his first intron (the name of the stringtie annotated transcripts may change). However, the coverage does not allow to see the predicted introns.

### Transcriptome quality assessment with **rnaQUAST**

To assess the quality of assembled transcripts, we are going to use **rnaQUAST**, which will provide us diverse completeness/correctness statistics very useful in order to identify and address potential errors or gaps in the assembly process. In addition, it will allow us to identify misassembled transcriptomes, ensuring higher data quality for downstream analyses ({% cite Bushmanova2016 %}).

rnaQUAST generates several metrics to evaluate the quality of transcriptome assemblies, such as:

- **Isoform metrics**: Aligned transcripts are compared to the gene database and calculates metrics such as sensitivity, specificity, and precision. These metrics assess how well the assembled transcripts match the known gene isoforms, indicating the completeness and correctness of the assembly.
- **Exon metrics**: It calculates metrics such as the number of correctly predicted exons, missed exons, and falsely predicted exons. These metrics provide insights into the accuracy of exon prediction.
- **Gene coverage by raw reads**: This metric indicates the extent to which the expression of genes in the dataset is captured by the assembled transcriptome.
- **BUSCO score**: This metric represents the completeness of the assembly based on conserved orthologs.

> <hands-on-title>Transcriptome evaluation</hands-on-title>
> 1. {% tool [gffread](toolshed.g2.bx.psu.edu/repos/devteam/gffread/gffread/2.2.2.1.4+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Input BED, GFF3 or GTF feature file "*: `Assembled transcripts coordinates` (the output of the first Stringtie)
>    - *"Reference Genome"*: `From your history`
>       - {% icon param-file %} *"Genome Reference Fasta"*: `hg19.chr10.fasta`
>       - From *"Select fasta outputs"*: `fasta file with spliced exons for each GFF transcript`
>
> 2. Rename the collection as `Assembled transcripts sequences`
>
> 3. {% tool [rnaQUAST](toolshed.g2.bx.psu.edu/repos/iuc/rnaquast/rna_quast/2.2.1) %} with the following parameters:
>    - {% icon param-collection %} *"Transcripts"*: `Assembled transcripts sequences`
>    - {% icon param-files %} *"Reference genome"*: `hg19.chr10.fasta`
>    - *"Genome annotation"*: `Enabled`
>       - {% icon param-files %} *"GTF/GFF"*: `gencode.hg19.chr10.gtf`
>    - *"Disable infer genes"*: `Yes`
>    - *"Disable infer transcripts"*: `Yes`
>
> > <warning-title>Significant computational resources and run time</warning-title>
> > rnaQUAST can require around 2 hours to complete. Meanwhile we recommend you to continue and back to this part later.
> {: .warning}
>
{: .hands_on}

By default, **rnaQUAST** generates a user-friendly report in PDF format; it summarizes of the metrics, plots and statistics computed across the different samples. The table (fig. 13) provides various metrics that describe completeness and correctness levels of the assembled transcripts, including NGA50, NGA75, misassemblies, and mismatches.

![Figure 13. rnaQUAST metrics table summary](../../images/differential_isoform/rnaQUAST_summary_table.png "rnaQUAST tabular summary of metrics and statistics.")

Let's have a look at the plots. The rnaQUAST cumulative isoform (fig. 14) can help us to identify any trends or patterns in the distribution of transcript and isoform lengths across different abundance levels.

![Figure 14. rnaQUAST cummulative isoform](../../images/differential_isoform/rnaQUAST_cumulative_isoform.png "rnaQUAST cummulative isoform plot. The x-axis of the plot represents the cumulative length of transcripts and isoforms, while the y-axis represents the sequence abundance.")

According the plot, the transcripts length distribution is similar for all samples and the reference seems to have more short transcripts while the new transcriptomes have longer transcripts. Finally, we are going to evaluate the NAx plot (fig. 15).

![Figure 15. rnaQUAST NAx](../../images/differential_isoform/rnaQUAST_NAx.png "rnaQUAST NAx plot.  The x-axis of the plot represents the percentile, while the y-axis represents the contig length for this quantile.")

NAx is a metric that represents the length for which the collection of all aligned contigs of that length or longer covers at least x percent of the total length of the aligned assembled contigs, ranging from 0 to 100, per assembler. For example, on x = 50, we can get the NA50 = 50% of the total length of the transcripts are covered by transcripts over 4kb. On x = 20, 20% of the total length of the transcripts are covered by transcripts over 6kb... In that case, we cannot appreciate differences regarding distribution of transcript lengths across the samples.

# Isoform switching analysis with **IsoformSwitchAnalyzeR**

**IsoformSwitchAnalyzeR** is an open-source R package that enables both analyze changes in genome-wide patterns of {AS} and specific gene isoforms switch consequences (note: {AS} literally will result in {IS}). An advantage of **IsoformSwitchAnalyzeR** over other approaches is that it allows allows to integrate multiple layers of information, such as previously annotated coding sequences, de-novo coding potential predictions, protein domains and signal peptides. In addition, IsoformSwitchAnalyzeR facilitates identification of IS by making use of a new statistical methods that tests each individual isoform for differential usage, identifying the exact isoforms involved in an {IS} ({% cite isoformswitchanalyzer %})

> <comment-title>Nonsense mediated decay</comment-title>
>
> If transcript structures are predicted (either de-novo or guided) **IsoformSwitchAnalyzeR** offers an accurate tool for identifying the dominant ORF of the isoforms. The knowledge of isoform positions for the CDS/ORF allows for prediction of sensitivity to Nonsense Mediated Decay (NMD) — the mRNA quality control machinery that degrades isoforms with pre-mature termination codons (PTC).
>
{: .comment}

In this training, the IsoformSwitchAnalyzeR stage is divided in four steps:

1. Data import: import into **IsoformSwitchAnalyzeR** the transcription-level expression measurement dataset generated by **Stringtie**. This step also requires to import the GTF annotation file and the transcriptome.
2. Pre-processing step: non-informative gene/isoforms are removed from the datasets and differentially isoform usage analysis with **DEXSeq**. Once the {IS} have been found, the corresponding nucleotide and aminoacid sequences are extracted.
3. Outward sequence analysis: The sequences obtained in the previous step are used in order to evaluate their coding potential and the motifs that they contain by using two different tools: **PfamScan** and **CPAT**.
4. Isoform splicing analysis: The final step involves importing and incorporating the results of the external sequence analysis, identifying intron retention, predicting functional consequences and generating the reports.


> <comment-title>On the alternative splicing concept</comment-title>
>
> In accordance with the IsoformSwitchAnalyzeR developers, in this training the concept of alternative splicing englobes both alternative splicing (AS), alternative transcription start sites (ATSS) as well as alternative transcription start sites (ATTS).
>
{: .comment}

![Figure 16. IsoformSwitchAnalyzeR pipeline scheme](../../images/differential_isoform/scheme.jpeg "IsoformSwitchAnalyzeR workflow scheme. The individual steps are indicated by arrows. Please note the boxes marked with “outward sequence analysis” requires to run a set of different tools. Adapted from IsoformSwitchAnalyzeR viggette.")

Now, we can start with the {IS} analysis.

## Split collection between the 2 conditions

We will generate 2 collections from the Stringtie output, one with the cancer samples and one with the healthy samples.

> <hands-on-title> Split collection</hands-on-title>
>
> 1. {% tool [Extract element identifiers](toolshed.g2.bx.psu.edu/repos/iuc/collection_element_identifiers/collection_element_identifiers/0.0.2) %} with the following parameters:
>    - *"Dataset collection"*: `Transcriptomes quantification`
>
> 2. {% tool [Search in textfiles](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_grep_tool/9.3+galaxy1) %} (grep) with the following parameters:
>    - *"Select lines from"*: `Extract element identifiers on data XXX` (output of  **Extract element identifiers** {% icon tool %})
>    - *"that"*: `Match`
>    - *"Regular Expression"*: `doxycycline`
>
> 3. {% tool [Filter collecion](__FILTER_FROM_FILE__) %} with the following parameters:
>    - *"Input collection"*: `Transcriptomes quantification`
>    - *"How should the elements to remove be determined"*: `Remove if identifiers are ABSENT from file`
>        - *"Filter out identifiers absent from"*: `Search in textfiles on data XXX` (output of  **Search in textfiles** {% icon tool %})
>
> 4. Rename both collections `no EWS-FLI1 transcriptomes quantification` (the filtered collection) and `EWS-FLI1 transcriptomes quantification` (the discarded collection).
>
{: .hands_on}

## Import data

The first step of the IsoformSwitchAnalyzeR pipeline is to import the required datasets. This step requires to get a FASTA file with the reference transcriptome.

> <comment-title>Salmon as source of transcript-expression data</comment-title>
>
> In addition of Stringtie, it is possible to import dada from **SALMON**. The main advantage of Salmon over StringTie for isoform differential expression analysis is its speed and computational efficiency, while achieving similar accuracies when analyzing known transcripts. However, **StringTie** is considered a better alternative if we are interested in novel transcript features.
>
{: .comment}


> <hands-on-title>Extract reference transcriptome</hands-on-title>
>
> 1. {% tool [gffread](toolshed.g2.bx.psu.edu/repos/devteam/gffread/gffread/2.2.1.3+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Input BED, GFF3 or GTF feature file "*: `Reference transcriptome annotation`
>    - *"Reference Genome"*: `From your history`
>       - {% icon param-file %} *"Genome Reference Fasta"*: `hg19.chr10.fasta`
>       - From *"Select fasta outputs"*: `FASTA file with spliced exons for each GFF transcript`
>
> 2. Rename the output as `Reference transcriptome sequences`
>
{: .hands_on}

> <hands-on-title>Import data with IsoformSwitchAnalyzeR </hands-on-title>
>
> 1. {% tool [IsoformSwitchAnalyzeR](toolshed.g2.bx.psu.edu/repos/iuc/isoformswitchanalyzer/isoformswitchanalyzer/1.20.0+galaxy5) %} with the following parameters:
>    - *"Tool function mode"*: `Import data`
>        - *"Quantification data source"*: `StringTie`
>        - In *"1: Factor level"*:
>            - *"Specify a factor level, typical values could be 'tumor' or 'treated'"*: `EWS-FLI1`
>            - {% icon param-collection %} *"Transcript-level expression measurements"*: `EWS-FLI1 transcriptomes quantification`
>        - In *"2: Factor level"*:
>            - *"Specify a factor level, typical values could be 'tumor' or 'treated'"*: `no EWS-FLI1`
>            - {% icon param-collection %} *"Transcript-level expression measurements"*: `no EWS-FLI1 transcriptomes quantification`
>            - *"Average read length"*: `100`
>            - *"Analysis mode"*:
>              - {% icon param-file %} *"Annotation generated by StringTie merge"*: `Reference transcriptome annotation`
>        - {% icon param-file %} *"Genome annotation (GTF)"*: `gencode.hg19.chr10.gtf`
>        - {% icon param-file %} *"Transcriptome"*: `Reference transcriptome sequences`
>        - *"Remove non-conventional chromosomes"*: `Yes`
>
> > <warning-title>Choose the parameters carefully</warning-title>
> > If you are using a RefSeq or Genbank reference, turn off *"Remove non-conventional chromosomes"* parameter.
> > Generally, RefSeq chromosome IDs contain underscrores and dots (for eg. `NC_000001.11`). In this case, if this parameter is turned on, the tool ignores all the data and results in an error.
> >
> {: .warning}
>
{: .hands_on}

It generates a **switchAnalyzeRlist** object that contains all relevant information about the isoforms involved in isoform swtches, such as each comparison of an isoform between conditions.


## Pre-processing step

Once the datasets have been imported into a RData file, we can start with the pre-processing step. In order to enhance the reliability of the downstream analysis, it is important to remove the non-informative genes/isoforms (e.g. single isoform genes and non-expressed isoforms).

After the pre-processing, **IsoformSwitchAnalyzieR** performs the differential isoform usage analysis by using **DESXSeq**, which despite originally designed for testing differential exon usage, it has been demonstrated to perform exceptionally well for differential isoform usage.**DEXSeq** uses generalized linear models to assess the significance of observed differences in isoform usage values between conditions, taking into account the biological variation between replicates ({% cite dexseq %}).

> <comment-title>Difference in isoform fraction concept</comment-title>
>
> IsoformSwitchAnalyzeR measures isoform usage via isoform fraction (IF) values which quantifies the fraction of the parent gene expression originating from a specific isoform, (calculated as isoform_exp / gene_exp). Consequently, the difference in isoform usage is quantified as the difference in isoform fraction (dIF) calculated as IF2 - IF1, and these dIF are used to measure the effect size  ({% cite isoformswitchanalyzer %}).
>
{: .comment}

IsoformSwitchAnalyzeR uses two parameters to define a significant {IS}:

- Alpha: the FDR corrected P-value (Q-value) cutoff.
- dIFcutoff: the minimum (absolute) change in isoform usage (dIF).

This combination is used since a Q-value is only a measure of the statistical certainty of the difference between two groups and thereby does not reflect the effect size which is measured by the dIF values ({% cite isoformswitchanalyzer %}).

> <comment-title>Isoform or gene resolution analysis?</comment-title>
>
> Despite IsoformSwitchAnalyzeR supports both isoform and gene resolution analysis, it is recommended to use the isoform-level analysis. The reason is that since the analysis is restricted to genes involved in {IS}, gene-level analysis is conditioned by higher false positive rates.
>
{: .comment}

> <hands-on-title>IsoformSwitchAnalyzeR pre-processing step</hands-on-title>
>
> 1. {% tool [IsoformSwitchAnalyzeR](toolshed.g2.bx.psu.edu/repos/iuc/isoformswitchanalyzer/isoformswitchanalyzer/1.20.0+galaxy5) %} with the following parameters:
>    - *"Tool function mode"*: `Analysis part one: Extract isoform switches and their sequences`
>        - {% icon param-file %} *"IsoformSwitchAnalyzeR R object"*: `SwitchList (RData)` (output of **IsoformSwitchAnalyzeR** {% icon tool %})
>    - In *"Sequence extraction parameters"*:
>        - "*Remove short aminoacid sequences*": `No`
>        - *"Remove ORFs containint STOP codons*": `No`
>
>   > <comment-title>Reduce to switch genes option</comment-title>
>   >
>   > An important argument is the 'Reduce to switch genes' option. When enabled, it will reduce/subset  of genes to those which each contains at least one differential used isoform, as indicated by the alpha and dIFcutoff cutoffs. This option ensures the rest of the workflow runs significantly faster since isoforms from genes without {IS} are not analyzed.
>   >
>   {: .comment}
>
>    > <question-title></question-title>
>    >
>    > 1. How many genes have at least one differential used isoform?
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. The output named ": summary" gives you 126 genes.
>    >    >
>    >    {: .solution}
>    {: .question}
{: .hands_on}


## Outward sequence analysis

The next step is to use to use generated FASTA files corresponding to the aminoacid and nucleotide sequences to perform the external analysis tools. In that case, we will use **PfamScan** for predicting protein domains and **CPAT** predicting the coding potential. This information will be integrated in the downstream analysis.

> <comment-title>Additional sequence analysis tools</comment-title>
>
> Note that **IsoformSwitchAnalyzeR** allows to integrate additional sources, such as prediction of signal peptides (SignalP) and intrinsically disordered regions (IUPred2AIUPred2A or NetSurfP-2). However, those tools are not currenly available in Galaxy, so for this reason we will not make use of them. You can find more information in the [IsoformSwitchAnalyzeR vignette](https://bioconductor.org/packages/release/bioc/vignettes/IsoformSwitchAnalyzeR/inst/doc/IsoformSwitchAnalyzeR.html#advice-for-running-external-sequence-analysis-tools-and-downloading-results).
>
{: .comment}


### Protein domain identification with **PfamScan**

PfamScan is an open-source tool developed by the EMBL-EBI for identifying protein motifs. It allows to search FASTA sequences against Pfam HMM libraries. This tool requires three input datasets:

- Pfam-A HMMs in an HMM library searchable with the hmmscan program: Pfam-A.hmm.gz
- Pfam-A HMM Stockholm file associated with each HMM required for PfamScan: Pfam-A.hmm.dat.gz
- Active sites dataset: active_sites.dat.gz

> <comment-title>Pfam database</comment-title>
>
> Pfam is a collection of multiple sequence alignments and profile hidden Markov models (HMMs). Each Pfam profile HMM represents a protein family or domain.  Pfam families are divided into two categories, Pfam-A and Pfam-B.
>
> Pfam-A is a collection of manually curated protein families based on seed alignments, and it is the primary set of families in the Pfam database. Pfam-B is an automatically generated supplement to Pfam-A, containing additional protein families not covered by Pfam-A, derived from clusters produced by MMSeqs2. For most applications, Pfam-A is likely to provide more accurate and interpretable results, but using Pfam-B can be helpful when no Pfam-A matches are found.
>
{: .comment}

> <hands-on-title> Domain identification with PfamScan </hands-on-title>
>
> 1. {% tool [PfamScan](toolshed.g2.bx.psu.edu/repos/bgruening/pfamscan/pfamscan/1.6+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Protein sequences FASTA file"*: `IsoformSwitchAnalyzeR on data XXX: aminoacid sequences` (output of **IsoformSwitchAnalyzeR** {% icon tool %})
>    - {% icon param-file %} *"Pfam-A HMM library"*: `Pfam-A.hmm.gz`
>    - {% icon param-file %} *"Pfam-A HMM Stockholm file"*: `Pfam-A.hmm.dat.gz`
>    - *"Predict active site residues"*: `Enabled`
>        - {% icon param-file %} *"Active sites file"*: `active_sites.dat.gz`
>
> 2. Rename the output as `Pfam_domains.tab`.
>
{: .hands_on}

### Coding probablity prediction with **CPAT**

**CPAT** (Coding-Potential Assessment Tool ) is an open-source alignment-free tool, which uses logistic regression to distinguish between coding and noncoding transcripts on the basis of four sequence features. To achieve this goal, **CPAT** computes the following four metrics: maximum length of the open reading frame (ORF), ORF coverage, Fickett TESTCODE and Hexamer usage bias.

Each of those metrics is computed from a set of known protein-coding genes and another set of non-coding genes. **CPAT** will then builds a logistic regression model using theses as predictor variables and the “protein-coding status” as the response variable. After evaluating the performance and determining the probability cutoff, the model can be used to predict new RNA sequences ({% cite Wang2013 %}).

> <details-title>CPAT scores in detail</details-title>
>
> CPAT makes use of for predictior variables for performing the coding-potential analysis. The figure 16 shows the scoring distribution between coding and noncoding transcripts for the four metrics.
>
> ![Figure 17. CPAT predictor score distributions](../../images/differential_isoform/CPAT_distributions.jpg "Example of score distribution between coding (red) and noncoding (blue) sequences for the four CPAT metrics. The different subplots correspond to ORF size (A), ORF coverage (B), Fickett score (TESTCODE statistic) (C) and hexamer usage bias measured by log-likelihood ratio (D). Source: Wang et al., 2013")
>
> The maximum length of the ORF (fig. 17.A) is one of the most fundamental features used to distinguish ncRNA from messenger RNA because a long putative ORF is unlikely to be observed by random chance in noncoding sequences.
>
> The ORF coverage (fig. 17.B)  is the ratio of ORF to transcript lengths. This feature has demonstrated to have good classification power, and it is highly complementary to, and independent of, the ORF length (some large ncRNAs may contain putative long ORFs by random chance, but usually have much lower ORF coverage than protein-coding RNAs).
>
> The Fickett TESTCODE (fig. 17.C) distinguishes protein-coding RNA and ncRNA according to the combinational effect of nucleotide composition and codon usage bias. It is independent of the ORF, and when the test region is ≥200 nt in length (which includes most lncRNA), this feature alone can achieve 94% sensitivity and 97% specificity.
>
> Finally, the fourth metric is the hexamer usage bias (fig. 17.D), determines the relative degree of hexamer usage bias in a particular sequence. Positive values indicate a coding sequence, whereas negative values indicate a noncoding sequence.
>
{: .details}

First, we will generate two intermediate files that we will use as input for CPAT.

> <hands-on-title>Extract lncRNA and protein sequences</hands-on-title>
>
> 1. {% tool [Search in textfiles](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_grep_tool/9.3+galaxy1) %} (grep) with the following parameters:
>    - {% icon param-files %} *"Select lines from"*: `gencode.hg19.chr10.gtf`
>    - *"Regular Expression"*: `gene_type \"lincRNA\"|gene_type \"antisense\"|gene_type \"processed_transcript\"|gene_type \"sense_intronic\"|gene_type \"sense_overlapping\"|gene_type \"3prime_overlapping_ncrna\"` (in some gtf all these gene types are grouped under the 'lncRNA' annotation which makes the filter simpler. Here we need to specify all classes of lncRNA).
> 2. Rename the output as `gencode.hg19.chr10.lncRNA.gtf`
> 3. {% tool [Search in textfiles](toolshed.g2.bx.psu.edu/repos/bgruening/text_processing/tp_grep_tool/9.3+galaxy1) %} converter with the following parameters:
>    - {% icon param-files %} *"Select lines from"*: `gencode.hg19.chr10.gtf`
>    - *"Regular Expression2"*: `protein_coding`
> 4. Rename the output as `gencode.hg19.chr10.pc.gtf`
> 3. {% tool [gffread](toolshed.g2.bx.psu.edu/repos/devteam/gffread/gffread/2.2.1.3+galaxy0) %} with the following parameters:
>    - {% icon param-files %} *"Input BED, GFF3 or GTF feature file "*: `gencode.hg19.chr10.lncRNA.gtf` and `gencode.hg19.chr10.pc.gtf`
>    - *"Reference Genome"*: `From your history`
>       - {% icon param-file %} *"Genome Reference Fasta"*: `hg19.chr10.fasta`
>       - From *"Select fasta outputs"*: `fasta file with spliced exons for each GFF transcript`
> 4. Rename the output as `lncRNA.fasta` and `coding.fasta`
{: .hands_on}

As result, we will have two new FASTA files, one of them corresponding to long non-coding RNAs and the other one corresponding to the sequences of protein coding isoforms. Now, we can run CPAT:


> <hands-on-title>Coding prediction with CPAT</hands-on-title>
>
> 1. {% tool [CPAT](toolshed.g2.bx.psu.edu/repos/bgruening/cpat/cpat/3.0.4+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Query nucletide sequences"*: `IsoformSwitchAnalyzeR on data XXX: nucleotide sequences` (output of **IsoformSwitchAnalyzeR** {% icon tool %})
>    - {% icon param-file %} *"Reference genome"*: `hg19.chr10.fasta`
>    - {% icon param-file %} *"Coding sequences file"*: `coding.fasta`
>    - {% icon param-file %} *"Non coding sequeces file"*: `lncRNA.fasta`
>
{: .hands_on}

CPAT generates four files:

- **No ORF**: Sequence IDs or BED entries with no ORF found. Should be considered as non-coding.
- **ORF probabilities (TSV)**: ORF information (strand, frame, start, end, size, Fickett TESTCODE score, Hexamer score) and coding probability.
- **ORF best probabilities (TSV)**: The information of the best ORF. This file is a subset of the previous one.
- **ORF sequences (FASTA)**: The top ORF sequences (at least 75 nucleotides long) in FASTA format.

For the downstream analysis, we will use only the `ORF best probabilities`, but it will require some minor modifications in order to be used as input to **IsoformSwitchAnalyzeR**. Indeed IsoformSwitchAnalyzeR expects 8 columns: Data ID (column 2), Sequence Name (column 1), RNA size (column 3), ORF size (column 8), Ficket Score (column 9), Hexamer Score (column 10), Coding Probability (column 11), Coding Label (not present, we will put '-')

> <hands-on-title>Required format modifications on CPAT output</hands-on-title>
>
> 1. {% tool [Cut](Cut1) %} columns from a table with the following parameters:
>   - "*Cut columns*": `c2,c1,c3,c8-c11`
>   - {% icon param-file %} *"From"*: `ORF best probabilities (TSV)` (output of **CPAT** {% icon tool %})
>
> 2. {% tool [Add column](addValue) %} to an existing dataset with the following parameters:
>   - "*Add this value*": `-`
>   - {% icon param-file %} *"to Dataset"*: output of **Cut** {% icon tool %}
>
> 3. {% tool [Remove beginning](Remove beginning1) %} with the following parameters:
>    - *"Remove first"*: `1`
>    - {% icon param-file %} *"From"*: output of **Add column** {% icon tool %}
>
> 4. {% tool [Concatenate datasets](cat1) %} tail-to-head with the following parameters:
>    - {% icon param-file %} *"Concatenate Dataset"*: `CPAT_header.tab`
>    - In *"Dataset"*:
>       - Click in "*Insert Dataset*"
>           - In *"1: Dataset"*:
>               - {% icon param-file %} *"Select"*: output of **Remove beginning**
>
> 4. Rename the output as `CPAT_file.tabular` and change the datatype to `tabular`.
>
{: .hands_on}

## Isoform switching analysis

Once the expression data has been integrated and the required auxiliar information has been generated, we can start with the final stage of the analysis. **IsoformSwitchAnalyzeR** will extract the isoforms with significant changes in their isoform usage and the isoform(s) that compensate for the changes. Then, those isoforms are classified according to their contribution to gene expression (determinated by the dIF values). Finally, the isoforms with increased contribution (dIF values larger than the dIFcutoff) are compared in a pairwise manner to the isoforms with negative contribution.

IsoformSwitchAnalyzeR allows two analysis modes:

1. Analysis of individual {IS}
2. Genome-wide analysis of {IS}

In the next sections we will illustrate both different use-cases.

### Gene-specific isoform switching analysis

The gene-specific mode is interesting for those experimental designs which aim to evaluate a pre-selected set of genes of interest. For this purpose, we can analyze the expression pattern of the gene ADD3 which have been identified in the study where we took the datasets from ({% cite Saulnier2021 %}). The ADD3 gene encodes an EMT-associated protein playing a role in actin cytoskeleton remodeling. The authors confirmed by western-blot the isoform switch.

![Figure 18. ADD3 gene isoform confirmation by western blot](../../images/differential_isoform/ADD3_WB.png "ADD3 isoform switching by western blot (from Figure 7D of {% cite Saulnier2021 %}).")

> <hands-on-title>Gene-specific isoform switching analysis</hands-on-title>
>
> 1. {% tool [IsoformSwitchAnalyzeR](toolshed.g2.bx.psu.edu/repos/iuc/isoformswitchanalyzer/isoformswitchanalyzer/1.20.0+galaxy5) %} with the following parameters:
>    - *"Tool function mode"*: `Analysis part two: Plot all isoform switches and their annotation`
>        - {% icon param-file %} *"IsoformSwitchAnalyzeR R object"*: `switchList` (output of **IsoformSwitchAnalyzeR** {% icon tool %} part 1)
>        - *"Analysis mode"*: `Analyze specific gene`
>        - *"Gene name"*: `ADD3`
>        - *"Include prediction of coding potential information"*: `CPAT`
>            - {% icon param-file %} *"CPAT result file"*: `CPAT_file.tabular`
>        - *"Include Pfam information"*: `Enabled`
>            - {% icon param-file %} *"Include Pfam results (sequence analysis of protein domains)"*: `Pfam_domains.tab` (output of **PfamScan** {% icon tool %})
>        - *"Include SignalP results"*: `Disabled`
>        - *"Include prediction of intrinsically disordered Regions (IDR) information"*: `Disabled`
>
{: .hands_on}

Let's have a look at the generated plot (fig. 19).

![Figure 19. ADD3 gene isoform expression profile plot](../../images/differential_isoform/isoformSwitchAnalyzer_gene.png "ADD3 isoform expression profile plot. The plot integrates isoform structures along with the annotations, gene and isoform expression and isoform usage including the result of the isoform switch test.")

We can appreciate that IsoformSwitchAnalyzeR detects a significant isoform usage with ENST00000277900.8 and ENST00000360162.3 being more expressed when EWS-FLI1 is down (only the first one reaches significance) and ENST00000356080.4 being less expressed when EWS-FLI1 is down.

> <question-title></question-title>
>
> What is the difference between the isoform ENST00000356080.4 (specific to EWS-FLI1) and the other two?
>
> > <solution-title></solution-title>
> >
> > This isoform uses an additional exon (before last).
> >
> {: .solution}
{: .question}

We will again try to confirm this finding by visualization.

> <hands-on-title>Check isoform switching with pyGenometracks from STAR coverage</hands-on-title>
>
> 1. Rerun **pyGenomeTracks** but change *"Region of the genome to limit the operation"* to ` chr10:111,767,742-111,895,323`
>
>    > <question-title></question-title>
>    >
>    > 1. Can you confirm the change?
>    >
>    > ![Figure 20. ADD3 coverage](../../images/differential_isoform/pgt_ADD3.png "Coverage on ADD3 gene.")
>    >
>    >    > <solution-title></solution-title>
>    >    >
>    >    > 1. On the right, on the blue coverage we seem to see something. If you rerun and change the region to `chr10:111,890,000-111,895,323`. You will see it much better.
>    >    > ![Figure 21. ADD3 coverage zoomed](../../images/differential_isoform/pgt_ADD3_zoom.png "Coverage on the last exons of ADD3 gene.")
>    >    >
>    >    {: .solution}
>    {: .question}
>
{: .hands_on}


### IsoformSwitchAnalyzer genome-wide analysis

A genome-wide analysis is both useful for getting an overview of the extent of {IS} as well as discovering general patterns. In this training we will perform three different summaries/analyses for both the analysis of {AS} and {IS} with predicted consequences:

1. Global summary statistics for summarizing the number of switches with predicted consequences and the number of splicing events occurring in the different comparisons.
2. Analysis of splicing/consequence enrichment for analyzing whether a particular consequence/splice type occurs more frequently than the opposite event.
3. Analysis of genome-wide changes in isoform usage for analyzing the genome-wide changes in isoform usage for all isoforms with particular opposite pattern events.

> <comment-title>Difference in isoform fraction</comment-title>
>
> If an isoform has a significant change in its contribution to gene expression, there must per definition be reciprocal changes in one (or more) isoforms in the opposite direction, compensating for the change in the first isoform. Thus,the isoforms used more (positive dIF) can be compared to the isoforms used less (negative dIF) and by systematically identify differences annotation it is possible to identify potential function consequences of the {IS} event.
>
{: .comment}

> <hands-on-title>Isoform switch analysis</hands-on-title>
>
> 1. {% tool [IsoformSwitchAnalyzeR](toolshed.g2.bx.psu.edu/repos/iuc/isoformswitchanalyzer/isoformswitchanalyzer/1.20.0+galaxy5) %} with the following parameters:
>    - *"Tool function mode"*: `Analysis part two: Plot all isoform switches and their annotation`
>        - {% icon param-file %} *"IsoformSwitchAnalyzeR R object"*: `switchList` (output of **IsoformSwitchAnalyzeR** {% icon tool %} part 1)
>        - *"Analysis mode"*: `Full analysis`
>        - *"Include prediction of coding potential information"*: `CPAT`
>            - {% icon param-file %} *"CPAT result file"*: `CPAT_file.tabular`
>        - *"Include Pfam information"*: `Enabled`
>            - {% icon param-file %} *"Include Pfam results (sequence analysis of protein domains)"*: `Pfam_domains.tab` (output of **PfamScan** {% icon tool %})
>        - *"Include SignalP results"*: `Disabled`
>        - *"Include prediction of intrinsically disordered Regions (IDR) information"*: `Disabled`
>
>
>   > <comment-title>Only significantly differential isoforms</comment-title>
>   >
>   > A more strict analysis can be performed by enabling the **Only significantly differential used isoforms**  option, which causes to only consider significant isoforms meaning the compensatory changes in isoform usage are ignored unless they themselves are significant.
>   >
>   {: .comment}
>
{: .hands_on}

It generates five tabular files with the results of the different statistical analysis:

- **Splicing summary**: values of the different splicing events
- **Splicing enrichment**: results of enrichment statistical analysis for the different splicing events.
- **Consequences summary**: values of global usage of {IS}
- **Consequences enrichment**: results of enrichment statistical analysis for the different functional consequences.
- **Switching gene/isoforms**: list of genes with statistically significant isoform swiching between conditions. The switches are ranked (by p-value or switch size).

> <question-title>Top IS events</question-title>
>
> What are the the top three {IS} events (as defined by alpha and dIFcutoff)?
>
> > <solution-title></solution-title>
> >
> > The top three genes are ITGB1, HECTD2, GPAM(fig. 22).
> >
> > ![Figure  22. Switching gene/isoform tabular dataset](../../images/differential_isoform/list_genes.png "Switching gene/isoform dataset.")
> >
> {: .solution}
{: .question}

We always recommand to check with pyGenomeTracks the confidence you have into these findings.


In addition, it provides a RData object and three collections of plots: {IS} events with predicted functional consequences, {IS} without predicted functional consequences and genome-wide plots.

#### Analysis of splicing enrichment

In this section we will assess whether there are differences with respect to the type of {AS} event .

> <comment-title>Interpretation of splicing events</comment-title>
>
> The events are classified by comparing the splicing patterns with a hypothetical pre-RNA (fig. 23).
>
> ![Figure 23. Classification of splicing patterns](../../images/differential_isoform/isoformSwitcher_splicing_patterns.png "Splicing patterns diversity. The observed splice patterns (left column) of two isoforms compared as indicated by the color of the splice patterns. The corresponding classification of the event (middle column) and the abreviation used (right column).")
>
> Mutually exclusive exon (MEE) is a special case where two isoforms from the same gene contain exons which are not found in any of the other isoforms from that gene.
>
{: .comment}

First, we will start analyzing the total number of splicing events (fig. 24).

![Figure 24. Summary of genome-wide total splicing events](../../images/differential_isoform/isoformSwitchAnalyzer_isoform_usage.png "Analysis of splicing enrichment. Number of isoforms significantly differentially used between EWS-FLI1 and no EWS-FLI1 resulting in at least one splice event.")

From the figure 24, it can be hypothesised that some of the {AS} events are not equally used. To formally analyze this type of uneven {AS}, **IsoformSwithAnalyzeR** computes the fraction of events being gains (as opposed to loss) and perform a statistical analysis of this fraction by using a binomial test (fig. 25).

![Figure 25. Enrichment/depletion in isoform switch with consequences statistical analysis](../../images/differential_isoform/isoformSwitchAnalyzer_splicing_event.png "Comparison of differential splicing events. The fraction (and 95% confidence interval) of isoform switches (x-axis) resulting in gain of a specific alternative splice event (indicated by y axis) in the switch from health to cancer. The dashed line indicate no enrichment/depletion and the color indicate if FDR < 0.05 (red) or not (black).")

According to the results (fig. 25), there are not statistically significant differences in specific splicing type events between both experimental conditions. However, this result is affected by the fact that we are using only a fraction of the total data (remember that we subsampled the original datasets in order to speed up the analysis).

#### Analysis of consequence enrichment

To analyze large-scale patterns in predicted {IS} consequences, **IsoformSwitchAnalyzeR** computes all {IS} events resulting in a gain/loss of a specific consequence (e.g. protein domain gain/loss). According the results, some types of functional consequences seem to be enriched or depleted in one or the other conditions (e.g. intron retention) (fig. 26).

![Figure 26. Summary of genome-wide enrichment/depletion of isoform switching events](../../images/differential_isoform/isoformSwitchAnalyzer_consequences_features.png "Analysis of consequence enrichment. Number of isoforms significantly differentially used between EWS-FLI1 and noEWS-FLI1 resulting in at least one isoform switch consequence.")

To assess this observation, **IsoformSwitchAnalyzeR** performs a standard proportion test is performed (fig. 27). The results indicate that differences are not statistically significant.

![Figure 27. Enrichment/depletion isoform switch analysis](../../images/differential_isoform/isoformSwitchAnalyzer_consequences_isoform.png "Enrichment/depletion in isoform switches consequences. The x-axis shows the fraction (with 95% confidence interval) resulting in the consequence indicated by y axis, in the switches from EWS-FLI1 to noEWS-FLI1. Dashed line indicate no enrichment/depletion. Color indicate if FDR < 0.05 (red) or not (black).")


> <question-title></question-title>
>
> What is the adjusted P-value corresponding to the TSS downstream/upstream feature test?
>
>   > <details-title>Consequences enrichment dataset</details-title>
>   >
>   > The consequences enrichment tabular dataset includes the following columns:
>   >
>   > - **conseqPair**: The set of opposite consequences considered.
>   > - **feature**: Description of the functional consequence
>   > - **propOfRelevantEvents**: Proportion of total number of genes being of the type described in the feature column.
>   > - **propCiLo**: The lower boundary of the confidence interval.
>   > - **propCiHi**: The high boundary of the confidence interval.
>   > - **propPval**: The p-value associated with the null hypothesis.
>   > - **nUp**: The number of genes with the consequence described in the feature column.
>   > - **nDown**: The number of genes with the opposite consequence of what is described in the feature column.
>   > - **propQval**: The adjusted P-value resulting when p-values are corrected using FDR (BenjaminiHochberg).
>   >
>   {: .details}
>
> > <solution-title></solution-title>
> >
> > This information can be found in the eighth column of the Consequences enrichment dataset.
> >
> > In that case, the adjusted P-value is 0.37.
> >
> {: .solution}
{: .question}


#### Analysis of genome-wide changes in isoform usage

Here, we will evaluate the genome-wide changes in isoform usage. This type of analysis allows us to identify if differences in splicing events are genome-wide or restricted to specific regions, and is particularly interesting if the expected difference between conditions is large (fig. 28).

![Figure 28. Genome-wide changes violing plot](../../images/differential_isoform/isoformSwitchAnalyzer_summary.png "Genome-wide changes violin plot. The dots in the violin plots above indicate 25th, 50th (median) and 75th percentiles.")

As expected from the previous results, in that case there are not statistically significant genome-wide differences in splicing events. Finally, we can have a look at the remaining plots (fig. 29).

![Figure 29. Genome-wide changes volcano and scatterplots](../../images/differential_isoform/isoformSwitchAnalyzer_volcano.png "Genome-wide isoform switching overview. The volcanoplot represent the -log(Q-value) vs dIF (change in isoform usage from condition) (A). The scaterplot represents the dIF vs gene log2 fold change (B).")

Here we can see that changes in gene expression and isoform switches are not in any way mutually exclusive, as there are many genes which are both differentially expressed (large gene log2FC) and contain isoform switches (color).

<!--

# Optional exercise: original data analysis

As additional activity, you can try to run the pipeline by using the original datasets. In that case we will make use of the Galaxy Workflow System, which will allow us to automatize the analysis by minimizing the number of required manual steps.

> <warning-title>Significant computational resources and run time</warning-title>
> When using the full data, the analysis can last for more than 48 hours and uses about 400GB.
{: .warning}


We will start by importing the datasets from Zenodo.

> <hands-on-title>Retrieve mRNA-Seq datasets</hands-on-title>
>
> 1. Create a new history for this analsyis
> 2. Import the files from Zenodo:
>
>    - Open the file {% icon galaxy-upload %} __upload__ menu
>    - Click on __Rule-based__ tab
>    - *"Upload data as"*: `Collection(s)`
>    - Copy the following tabular data, paste it into the textbox and press <kbd>Build</kbd>
>
>      ```
>      SRR9050437_Health	{{ page.zenodo_link }}/files/SRR9050437_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050437_Health	{{ page.zenodo_link }}/files/SRR9050437_reverse.fastqsanger.gz	fastqsanger.gz	2
>      SRR9050438_Health	{{ page.zenodo_link }}/files/SRR9050438_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050438_Health	{{ page.zenodo_link }}/files/SRR9050438_reverse.fastqsanger.gz	fastqsanger.gz	2
>      SRR9050439_Health	{{ page.zenodo_link }}/files/SRR9050439_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050439_Health	{{ page.zenodo_link }}/files/SRR9050439_reverse.fastqsanger.gz	fastqsanger.gz	2
>      SRR9050440_Cancer	{{ page.zenodo_link }}/files/SRR9050440_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050440_Cancer	{{ page.zenodo_link }}/files/SRR9050440_reverse.fastqsanger.gz	fastqsanger.gz	2
>      SRR9050441_Cancer	{{ page.zenodo_link }}/files/SRR9050441_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050441_Cancer	{{ page.zenodo_link }}/files/SRR9050441_reverse.fastqsanger.gz	fastqsanger.gz	2
>      SRR9050442_Cancer	{{ page.zenodo_link }}/files/SRR9050442_forward.fastqsanger.gz	fastqsanger.gz	1
>      SRR9050442_Cancer	{{ page.zenodo_link }}/files/SRR9050442_reverse.fastqsanger.gz	fastqsanger.gz	2
>      ```
>
>    - From **Rules** menu select `Add / Modify Column Definitions`
>       - Click `Add Definition` button and select `List Identifier(s)`: column `A`
>       - Click `Add Definition` button and select `URL`: column `B`
>       - Click `Add Definition` button and select `Type`: column `C`
>       - Click `Add Definition` button and select `Pair-end Indicator`: column `D`
>
>    - Click `Apply`, provide the name `Original samples` and press <kbd>Upload</kbd>
>
{: .hands_on}

Next we will retrieve the datasets corresponding to the original reference genome data.

> <hands-on-title>Retrieve the additional datasets</hands-on-title>
>
> 1. Import the files from Zenodo:
>
>    - Open the file {% icon galaxy-upload %} __upload__ menu
>    - Click on __Rule-based__ tab
>    - *"Upload data as"*: `Datasets`
>    - Once again, copy the tabular data, paste it into the textbox and press <kbd>Build</kbd>
>
>      ```
>      gencode.v43.annotation.gtf.gz	{{ page.zenodo_link }}/files/gencode.v43.annotation.gtf.gz
>      GRCh38.p13.genome.fa.gz	{{ page.zenodo_link }}/files/GRCh38.p13.genome.fa.gz
>      ```
>
>    - From **Rules** menu select `Add / Modify Column Definitions`
>       - Click `Add Definition` button and select `Name`: column `A`
>       - Click `Add Definition` button and select `URL`: column `B`
>    - Click `Apply` and press <kbd>Upload</kbd>
>
>
{: .hands_on}


Also, you will need to import the additional datasets. Now, we will import the worfklow from WorkflowHub.

> <hands-on-title> Import a workflow </hands-on-title>
>
> 1. Click on the **Workflow** menu, located in the top bar.
> 2. Click on the **Import** button, located in the right corner.
> 3. In the section "Import a Workflow from Configured GA4GH Tool Registry Servers (e.g. Dockstore)", click on **Search form**.
>
> 4. In the **TRS Server: *workflowhub.eu*** menu you should type `Genome-wide alternative splicing analysis v.2`
> 5. Finally select the latest available version.
{: .hands_on}

Once we have imported the workflow, we can run the pipeline on the [original datasets](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA542693).

> <hands-on-title>Genome-wide alternative splicing analysis workflow</hands-on-title>
>
> 1. Click in the **Workflow** menu, located in the top bar
> 2. Click in the {% icon workflow-run %} **Run workflow** buttom corresponding to `Genome-wide alternative splicing analysis v.2`
> 3. In the **Workflow: VGP genome profile analysis** menu:
>   - {% icon param-file %} "*Reference genome*": `GRCh38.p13.genome.fa.gz`
>   - {% icon param-file %} "*Genome annotation*": `gencode.v43.annotation.gtf.gz`
>   - {% icon param-collection %} "*RNA-seq data collection*": `Original samples`
>   - {% icon param-file %} "*CPAT header*": `CPAT_header.tab`
>   - {% icon param-file %} "*Pfam-A HMM Stockholm file*": `Pfam-A.hmm.dat.gz`
>   - {% icon param-file %} "*Active sites dataset*": `active_site.dat.gz`
>   - {% icon param-file %} "*Pfam-A HMM library*": `Pfam-A.hmm.gz`
>
> 4. Click on the <kbd>Run workflow</kbd> buttom
>
>
{: .hands_on} -->

# Conclusion

Despite the large amount of RNA-seq data and computational methods available, isoform-based expression analysis is rare. Here we present a pipeline for performing genome-wide alternative splicing analysis (fig. 30). The workflow is composed of 3 main steps:
- Mapping with STAR using 2-pass mode
- Transcriptome assembly and quantification with Stringtie
- Isoform switching evaluation with IsoformSwitchAnalyzer.

![Figure 30. Genome-wide alternative splicing pipeline scheme](../../images/differential_isoform/galaxyworkflow.drawio.png "Genome-wide isoform switching pipeline scheme.")
