In this tutorial version we will learn how to use **SemiBin** {%cite Pan2022%} through Galaxy. **SemiBin** is a *semi-supervised deep learning method* for metagenomic binning. It uses both **must-link** and **cannot-link** constraints derived from single-copy marker genes to guide binning, allowing higher accuracy than purely unsupervised methods.

> Metagenome binning is essential for recovering high-quality metagenome-assembled genomes (MAGs) from environmental samples. SemiBin applies a semi-supervised Siamese neural network that learns from both contig features and automatically generated constraints. It has been shown to recover more high-quality and near-complete genomes than MetaBAT2, MaxBin2, or VAMB across multiple benchmark datasets. SemiBin also supports single-sample, co-assembly, and multi-sample binning workflows, demonstrating excellent scalability and versatility.
> {: .quote author="Pan et al., 2022" }

SemiBin is increasingly popular for metagenomic binning due to:

* **Higher reconstruction quality**
  SemiBin usually recovers **more high-quality and near-complete MAGs** than traditional binners, including MetaBAT2.

* **Semi-supervised learning**
  It combines deep learning with automatically generated constraints to better separate similar genomes.

* **Flexible binning modes**
  Works with:

  * individual samples
  * co-assemblies
  * multi-sample binning

* **Support for multiple environments**
  SemiBin includes trained models for:

  * human gut
  * dog gut
  * ocean
  * soil
    …plus a *generic pretrained model*.

We will use the uploaded assembled FASTA files as input (all other parameters will be preserved as default for simplicity).

> <hands-on-title>Individual binning of short reads with SemiBin</hands-on-title>
>
> 1. **{% tool [SemiBin](toolshed.g2.bx.psu.edu/repos/iuc/semibin/semibin/1.4.0) %}** with parameters:
>
>    * *"Select SemiBin mode"*: `Single-sample binning`
>    * *"Assembly (FASTA format)"*: `assembly fasta files`
>    * *"Environment"*: `auto`
>    * *"Use pretrained model"*: `Yes (default)`
>    * **Advanced Options**: leave all as **default**
>    * **Output options:**
>
>      * *"Output bins in separate FASTA files"*: `"Yes"`
{: .hands_on}

