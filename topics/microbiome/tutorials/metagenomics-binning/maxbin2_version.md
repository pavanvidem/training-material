## MaxBin2

In this tutorial version we will learn how to use MaxBin2 {%cite maxbin2015%} through Galaxy. MaxBin2 is an automated metagenomic binning tool that uses an Expectation-Maximization algorithm to group contigs into genome bins based on abundance, tetranucleotide frequency, and single-copy marker genes.

## Bin contigs using MaxBin2

> <hands-on-title> Calculate contig depths </hands-on-title>
>
> 1. {% tool [Calculate contig depths](toolshed.g2.bx.psu.edu/repos/iuc/metabat2_jgi_summarize_bam_contig_depths/metabat2_jgi_summarize_bam_contig_depths/2.17+galaxy0) %} with the following parameters:
>    - *"Mode to process BAM files"*: `One by one`
>        - {% icon param-file %} *"Sorted bam files"*: output of **Samtools sort** {% icon tool %}
>        - *"Select a reference genome?"*: `No`
>
>    > <comment-title> Why not use bam directly </comment-title>
>    >
>    > MetaBAT and MaxBin2 only accept per-contig depth tables because that is the specific input format their binning algorithm requires.
>    > BAM files contain read-level alignment data.
>    > These binners need summarized, contig-level coverage statistics.
>    {: .comment}
>
{: .hands_on}

> <hands-on-title> Individual binning of short-reads with MaxBin2 </hands-on-title>
>
> 1. {% tool [MaxBin2](toolshed.g2.bx.psu.edu/repos/mbernt/maxbin2/maxbin2/2.2.7+galaxy6) %} with the following parameters:
>    - {% icon param-collection %} *"Contig file"*: `Contigs` (Input dataset collection)
>    - *"Assembly type used to generate contig(s)"*: `Assembly of sample(s) one by one (individual assembly)`
>        - *"Input type"*: `Abundances`
>            - {% icon param-file %} *"Abundance file"*: `outputDepth` (output of **Calculate contig depths** {% icon tool %})
>    - In *"Outputs"*:
>        - *"Generate visualization of the marker gene presence numbers"*: `Yes`
>        - *"Output marker gene presence for bins table"*: `Yes`
>        - *"Output marker genes for each bin as fasta"*: `Yes`
>        - *"Output log"*: `Yes`
>
>
{: .hands_on}

> <question-title>Binning metrics</question-title>
>
> 1. How many bins where produced by MaxBin2 for our sample?
> 2. How many contigs are in the bin with most contigs? 
> > <solution-title></solution-title>
> >
> > 1. There are two bin for this sample.
> > 2. 35 and 24 in the other bin. 
> >
> {: .solution}
>
{: .question}