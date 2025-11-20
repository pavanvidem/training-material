## CONCOCT

In this tutorial version we will learn how to use **CONCOCT** {%cite Alneberg2014%} through Galaxy. **CONCOCT** is an *unsupervised metagenomic binner* that groups contigs using both **sequence characteristics** and **differential coverage across multiple samples**. In contrast to SemiBin, it does **not** rely on pretrained models or marker-gene constraints; instead, it clusters contig fragments purely based on statistical similarities.

> CONCOCT jointly models contig abundance profiles from multiple samples using a Gaussian mixture model. By taking advantage of differences in coverage across samples, it can separate genomes with similar sequence composition but distinct abundance patterns. CONCOCT also introduced the now-standard technique of splitting contigs into fixed-length fragments, allowing more consistent and accurate clustering.
> {: .quote author="Alneberg et al., 2014" }

CONCOCT is widely used in metagenomic binning due to:

* **Unsupervised probabilistic clustering**
  No marker genes, labels, or pretrained models are required.
* **Strong performance with multiple samples**
  Differential coverage helps disentangle closely related genomes.
100
* **Reproducible, transparent workflow**
  Its stepwise pipeline—fragmentation, coverage estimation, clustering—yields interpretable results.
* **Complementarity to other binners**
  Frequently used alongside SemiBin, MetaBAT2, or MaxBin2 in ensemble pipelines (e.g., MetaWRAP, nf-core/mag).

### Why preprocessing steps (such as cutting contigs) are required

CONCOCT relies heavily on preprocessing because its Gaussian mixture model treats **each contig fragment** as an individual data point. One key preprocessing step is **cutting contigs into equal-sized fragments**, typically around 10 kb. Fragmenting contigs helps balance the influence of long versus short contigs, generates uniform data points for statistical modeling, detects local variation or potential misassemblies within long contigs, and improves the resolution of abundance differences across genomes. This fragmentation is therefore mandatory for CONCOCT to function correctly.

After fragmentation, the **coverage of each fragment** is computed across all samples, providing a measure of abundance that CONCOCT uses alongside sequence statistics. These coverage profiles, together with basic sequence features, are then used as input to the **Gaussian mixture model clustering**, which groups fragments into bins. Once fragments are clustered, the results are mapped back to the original contigs to assign each contig to a specific bin.

Although CONCOCT produces a table assigning contigs to bins, it does not generate FASTA files for each bin by default. To obtain these sequences for downstream analyses, the tool `CONCOCT: Extract a FASTA file` is used. This tool takes the original contig FASTA and CONCOCT’s cluster assignments, extracts all contigs belonging to a chosen bin, and outputs a **FASTA file representing a single MAG**. This extraction step is essential to work with reconstructed genomes in subsequent analyses.

### Bin contigs using CONCOCT 

> <hands-on-title> Cut up contigs </hands-on-title>
>
> In this step we fragment the assembled contigs into fixed-length pieces, which CONCOCT requires for stable and consistent clustering.
>
> 1. {% tool [CONCOCT: Cut up contigs](toolshed.g2.bx.psu.edu/repos/iuc/concoct_cut_up_fasta/concoct_cut_up_fasta/1.1.0+galaxy2) %} with the following parameters:
>
>    * {% icon param-collection %} *"Fasta contigs file"*: `Contigs` (Input dataset collection)
>
>    * *"Concatenate final part to last contig?"*: `Yes`
>
>    * *"Output bed file with exact regions of the original contigs corresponding to the newly created contigs?"*: `Yes`
>
>    > <comment-title> Why this step? </comment-title>
>    >
>    > CONCOCT requires contigs to be split into equal-sized fragments. This prevents long contigs from dominating the clustering and increases resolution by allowing variation inside long contigs to be captured.
>    > {: .comment}
{: .hands_on}


> <hands-on-title> Generate coverage table </hands-on-title>
>
> This step computes coverage values for each contig fragment across all samples. CONCOCT uses these differential coverage profiles as one of the main signals for clustering.
>
> 1. {% tool [CONCOCT: Generate the input coverage table](toolshed.g2.bx.psu.edu/repos/iuc/concoct_coverage_table/concoct_coverage_table/1.1.0+galaxy2) %} with the following parameters:
>
>    * {% icon param-file %} *"Contigs BEDFile"*: `output_bed` (output of **CONCOCT: Cut up contigs** {% icon tool %})
>    * *"Type of assembly used to generate the contigs"*: `Individual assembly: 1 run per BAM file`
>
>      * {% icon param-file %} *"Sorted BAM file"*: `output1` (output of **Samtools sort** {% icon tool %})
>
>    > <comment-title> Why this step? </comment-title>
>    >
>    > CONCOCT relies on variation in abundance across samples. The coverage table generated here provides this information and is essential for identifying contigs that co-vary in abundance.
>    > {: .comment}
{: .hands_on}

> <hands-on-title> Run CONCOCT </hands-on-title>
>
> Here we perform the actual CONCOCT clustering. Using both coverage and sequence information, CONCOCT assigns contig fragments to genome bins.
>
> 1. {% tool [CONCOCT](toolshed.g2.bx.psu.edu/repos/iuc/concoct/concoct/1.1.0+galaxy2) %} with the following parameters:
>
>    * {% icon param-file %} *"Coverage file"*: `output` (output of **CONCOCT: Generate the input coverage table** {% icon tool %})
>    * {% icon param-file %} *"Composition file with sequences"*: `output_fasta` (output of **CONCOCT: Cut up contigs** {% icon tool %})
>    * In *"Advanced options"*:
>
>      * *"Read length for coverage"*: `{'id': 1, 'output_name': 'output'}`
>
>    > <comment-title> Why this step? </comment-title>
>    >
>    > This is the core of the CONCOCT workflow. The Gaussian mixture model groups contig fragments into clusters representing draft genomes (bins).
>    > {: .comment}
{: .hands_on}

> <hands-on-title> Merge fragment clusters </hands-on-title>
>
> Since CONCOCT clusters the **fragments**, we must merge them back to produce cluster assignments for the original contigs.
>
> 1. {% tool [CONCOCT: Merge cut clusters](toolshed.g2.bx.psu.edu/repos/iuc/concoct_merge_cut_up_clustering/concoct_merge_cut_up_clustering/1.1.0+galaxy2) %} with the following parameters:
>
>    * {% icon param-file %} *"Clusters generated by CONCOCT"*: `output_clustering` (output of **CONCOCT** {% icon tool %})
>
>    > <comment-title> Why this step? </comment-title>
>    >
>    > This step translates fragment-level cluster assignments into contig-level bin assignments—necessary for producing actual MAGs.
>    > {: .comment}
{: .hands_on}


> <hands-on-title> Extract MAG FASTA files </hands-on-title>
>
> In this final step we extract the contigs belonging to each bin and create FASTA files representing the reconstructed genomes (MAGs).
>
> 1. {% tool [CONCOCT: Extract a fasta file](toolshed.g2.bx.psu.edu/repos/iuc/concoct_extract_fasta_bins/concoct_extract_fasta_bins/1.1.0+galaxy2) %} with the following parameters:
>
>    * {% icon param-collection %} *"Original contig file"*: `output` (Input dataset collection)
>
>    * {% icon param-file %} *"CONCOCT clusters"*: `output` (output of **CONCOCT: Merge cut clusters** {% icon tool %})
>
>    > <comment-title> Why this step? </comment-title>
>    >
>    > This tool extracts the contigs belonging to each CONCOCT cluster and outputs them as FASTA files. These represent your preliminary MAGs and can now be evaluated and refined.
>    > {: .comment}
{: .hands_on}

> <question-title>Binning metrics</question-title>
>
> 1. How many bins where produced by MaxBin2 for our sample?
> 2. How many contigs are in the bin with most contigs? 
> > <solution-title></solution-title>
> >
> > 1. There are 10 bins for this sample.
> > 2. 50 - while all other bins only contain one contig each ! 
> >
> {: .solution}
>
{: .question}