## COMEbin

COMEbin is a relatively new binner that has shown remarkably strong performance in recent benchmarking studies.
However, it also has several drawbacks. 
* Due to its implementation, it cannot operate reliably on small test datasets, and therefore we cannot include it in this tutorial. 
* It requires substantial computational resources and long runtimes.
* The tool also suffers from other technical issues that can cause runs to fail. 

These problems cannot be resolved on the Galaxy side, and the tool is currently only lightly maintained upstream.

Nevertheless, because COMEbin can produce some of the best-performing bins when it runs successfully, we still mention it here. It may yield excellent results on real biological datasets and is available in Galaxy.

> <warning-title>Do not run COMEBin</warning-title>
>
> As said: Due to its implementation, it cannot operate reliably on small test datasets, and therefore we cannot include it in this tutorial. Do not run it on the tutorial dataset — it will fail. 
>
{: .warning}


###  Bin contigs using COMEbin

> <hands-on-title> Individual binning of short reads with COMEbin </hands-on-title>
>
> 1. {% tool [COMEBin](toolshed.g2.bx.psu.edu/repos/iuc/comebin/comebin/1.0.4+galaxy1) %} with the following parameters:
>    - {% icon param-collection %} *"Metagenomic assembly file"*: `Contigs` (Input dataset collection)
>    - {% icon param-file %} *"Input bam file(s)"*: `Reads` (output of **Samtools sort** {% icon tool %})
>
>    > <comment-title> Parameters </comment-title>
>    >
>    > The Batch size should be less then the number of contigs. But if this is the case for the batch size of 1014 your input data is likely too small to run with this tool !
>    {: .comment}
>
{: .hands_on}


