In this tutorial version we will learn how to use MaxBin2 {%cite maxbin2015%} through Galaxy. MaxBin2 is an automated metagenomic binning tool that uses an Expectation-Maximization algorithm to group contigs into genome bins based on abundance, tetranucleotide frequency, and single-copy marker genes.

> <hands-on-title>Individual binning of short-reads with MaxBin2</hands-on-title>
> 1.  {% tool [MaxBin2](toolshed.g2.bx.psu.edu/repos/mbernt/maxbin2/maxbin2/2.2.7+galaxy6) %} with parameters:
>     - *"Fasta file containing contigs"*: `assembly fasta files`
>     - In **Advanced options**, keep all as **default**.
>     - In **Output options:**
>
{: .hands_on}