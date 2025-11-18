In this tutorial version we will learn how to use MaxBin2 {%cite Wu2016%} through Galaxy. MaxBin2 is an automated metagenomic binning tool that uses an Expectation-Maximization algorithm to group contigs into genome bins based on abundance, tetranucleotide frequency, and single-copy marker genes.

> <hands-on-title>Individual binning of short-reads with MaxBin2</hands-on-title>
> 1.  {% tool [MetaBAT 2](https://toolshed.g2.bx.psu.edu/view/iuc/metabat2/01f02c5ddff8) %} with parameters:
>     - *"Fasta file containing contigs"*: `assembly fasta files`
>     - In **Advanced options**, keep all as **default**.
>     - In **Output options:**
>
{: .hands_on}