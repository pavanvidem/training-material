---
layout: tutorial_hands_on

title: "Proteogenomics 2: Database Search"
zenodo_link: "https://doi.org/10.5281/zenodo.1489208"
level: Intermediate
questions:
  - "How to identify variant proteoforms in MS data by searching with the customized Protein database?"
objectives:
  - "A proteogenomic data analysis of mass spectrometry data to identify and visualize variant peptides."
time_estimation: "15m"
key_points:
  - "With SearchGUI and PeptideShaker you can gain access to multiple search engines"
requirements:
  -
    type: "internal"
    topic_name: proteomics
    tutorials:
      - proteogenomics-dbcreation
follow_up_training:
  -
    type: "internal"
    topic_name: proteomics
    tutorials:
      - proteogenomics-novel-peptide-analysis

contributions:
  authorship:
  - subinamehta
  - timothygriffin
  - pratikdjagtap
  - jraysajulga
  - jj-umn
  - pravs3683
  - delphine-l

subtopic: multi-omics
tags: [proteogenomics]

recordings:
- captioners:
  - emmaleith
  date: '2021-02-15'
  galaxy_version: '21.01'
  length: 30M
  youtube_id: q1OjmTcbvBA
  speakers:
  - andrewr

---


In this tutorial, we perform proteogenomic database searching using the Mass Spectrometry data. The inputs for performing the proteogenomic database searching are the peaklist MGF files and the FASTA database file. The FASTA database is obtained by running the first workflow “Uniprot_cRAP_SAV_indel_translatedbed.FASTA”. The second workflow focuses on performing database search of the peak list files (MGFs).

![Workflow](../../images/second_workflow.png)


> <agenda-title></agenda-title>
>
> In this tutorial, we will deal with:
> 1. TOC
> {:toc}
>
{: .agenda}



# Pretreatments

> <hands-on-title>data upload and organization</hands-on-title>
>
> 1. Create a **new history** and name it something meaningful (e.g. *Proteogenomics DB search*)
> 2. Import the four MGF MS/MS files and the Trimmed_ref_5000_uniprot_cRAP.FASTA sequence file from Zenodo.[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.13270741.svg)](https://doi.org/10.5281/zenodo.13270741)
>    ```
>    https://zenodo.org/record/1489208/files/Mo_Tai_Trimmed_mgfs__Mo_Tai_iTRAQ_f4.mgf
>    https://zenodo.org/record/1489208/files/Mo_Tai_Trimmed_mgfs__Mo_Tai_iTRAQ_f5.mgf
>    https://zenodo.org/record/1489208/files/Mo_Tai_Trimmed_mgfs__Mo_Tai_iTRAQ_f8.mgf
>    https://zenodo.org/record/1489208/files/Mo_Tai_Trimmed_mgfs__Mo_Tai_iTRAQ_f9.mgf
>    https://zenodo.org/records/15359565/files/Uniprot_cRAP_SAV_indel_translatedbed.fasta
>    https://zenodo.org/records/13270741/files/Reference_Protein_Accessions.tabular
>
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
> 3. Rename the datasets to something more recognizable (strip the URL prefix)
> 4. Verify that the `Reference_Protein_Accessions.tabular` format is `tabular`.
>
>    {% snippet faqs/galaxy/datasets_change_datatype.md datatype="tabular" %}
>
> 5. Build a **Dataset list** for the four MGF files, name it as `Mo_Tai_MGFs`
>
>    {% snippet faqs/galaxy/collections_build_list.md %}
>
{: .hands_on}


# Match peptide sequences

The search database labeled `Uniprot_cRAP_SAV_indel_translatedbed.FASTA` is the input database that
will be used to match MS/MS to peptide sequences via a sequence database search.

For this, the sequence database-searching program called [SearchGUI](https://compomics.github.io/projects/searchgui.html) will be used.The generated dataset collection of the three *MGF files* in the history is used as the MS/MS input. We will walk through a number of these settings in order to utilize SearchGUI on these example MGF files.



## SearchGUI

> <hands-on-title>SearchGUI</hands-on-title>
>
> 1. {% tool [Identification Parameters](toolshed.g2.bx.psu.edu/repos/galaxyp/peptideshaker/ident_params/4.0.41+galaxy1) %} with the following parameters:
>    - *"Fixed Modifications"*: `Carbamidomethylation of C, ITRAQ-4Plex of K, ITRAQ-4Plex of peptide N-term`
>    - *"Variable modifications"*: `Oxidation of M, ITRAQ-4Plex of Y`
>    - *"Fragment Tolerance (Daltons)"*: `0.05` (this is high resolution MS/MS data)
>    - *"Minimum charge"*:`2`
>    - *"Maximum charge"*:`6`
>    - Section **Search Engine Options**:
>      - *"X!Tandem Options"*: `Advanced`
>      - *"X!Tandem: Advanced Search Related"*: `Set Advanced Search Parameters`
>        - *"X!Tandem: Quick Acetyl"*: `No`
>        - *"X!Tandem: Quick Pyrolidone"*: `No`
>      - Section **X!Tandem peptide model refinement**
>        - *"X!Tandem: Maximum Valid Expectation Value, refinement"* : `100`
>
>
> 1. {% tool [Search GUI](toolshed.g2.bx.psu.edu/repos/galaxyp/peptideshaker/search_gui/4.0.41+galaxy1) %} with the following parameters:
>    - {% icon param-file %} *"Identification Parameters file"*: `Identification Parameters: PAR file`
>    - {% icon param-file %} *"Protein Database"*: `Uniprot_cRAP_SAV_indel_translatedbed.FASTA` (Or however you named the `FASTA` file)
>
>        > <comment-title></comment-title>
>        >    The "Uniprot_cRAP_SAV_indel_translatedbed" FASTA database is obtained when you run the first proteogenomics workflow *"Proteogenomics 1: Database Creation"*.
>        >    Please make sure to run the 1st workflow.
>        {: .comment}
>
>    - {% icon param-collection %} *"Input Peak lists (mgf)"*: `Mo_Tai_MGFs` dataset collection.
>
>      > <tip-title>Select dataset collections as input</tip-title>
>      >
>      > * Click the **Dataset collection** icon on the left of the input field:
>      >
>      > * Select the appropriate dataset collection from the list
>      {: .tip}
>
>    - Section **Search Engine Options**:
>      - {% icon param-check %} *"DB-Search Engines"*: `X!Tandem`
>
>        > <comment-title></comment-title>
>        >    The section **Search Engine Options** contains a selection of sequence database searching
>        >    programs that are available in SearchGUI. Any combination of these programs can be used for
>        >    generating PSMs from MS/MS data. For the purpose of this tutorial, **X!Tandem** we will be
>        >    used.
>        {: .comment}
>
>
> Once the database search is completed, the SearchGUI tool will output a file (called a
> SearchGUI archive file) that will serve as an input for the next section, PeptideShaker.
> Rename the output as "**Compressed SearchGUI results**"
>
>
> > <comment-title></comment-title>
> > Note that sequence databases used for proteogenomics are usually much larger than
> > the excerpt used in this tutorial. When using large databases, the peptide identification
> > step can take much more time for computation. In proteogenomics, choosing the optimal
> > database is a crucial step of your workflow.
> {: .comment}
>
{: .hands_on}



## PeptideShaker

[PeptideShaker](https://compomics.github.io/projects/peptide-shaker.html) is a post-processing software tool that
processes data from the SearchGUI software tool. It serves to organize the Peptide-Spectral
Matches (PSMs) generated from SearchGUI processing and is contained in the SearchGUI archive.
It provides an assessment of confidence of the data, inferring proteins identified from the
matched peptide sequences and generates outputs that can be visualized by users to interpret
results. PeptideShaker has been wrapped in Galaxy to work in combination with SearchGUI
outputs.

> <comment-title>File Formats</comment-title>
>   There are a number of choices for different data files that can be generated using
>   PeptideShaker. A compressed file can be made containing all information needed to
>   view them results in the standalone PeptideShaker viewer. A `mzidentML` file can
>   be generated that contains all peptide sequence matching information and can be
>   utilized by compatible downstream software. Other outputs are focused on the inferred
>   proteins identified from the PSMs, as well as phosphorylation reports, relevant if
>   a phosphoproteomics experiment has been undertaken.
{: .comment}


> <hands-on-title>PeptideShaker</hands-on-title>
>
> 1. {% tool [Peptide Shaker](toolshed.g2.bx.psu.edu/repos/galaxyp/peptideshaker/peptide_shaker/2.0.33+galaxy1) %} with the following parameters:
>    - {% icon param-file %} *"Compressed SearchGUI results"*: The `Search GUI` archive file
>    - *"Include the protein sequences in mzIdentML"*: `No`
>    - *"Contact Information"*: `Specify Contact Information`
>      - Enter your own informations
>    - *"Output options"*: Select the `PSM Report` (Peptide-Spectral Match) and the `Certificate of Analysis`
>
>     > <comment-title>Certificate of Analysis</comment-title>
>     >
>     > The "Certificate of Analysis" provides details on all the parameters
>     > used by both SearchGUI and PeptideShaker in the analysis. This can be downloaded from the
>     > Galaxy instance to your local computer in a text file if desired.
>     >
>     {: .comment}
>
> 2. Inspect {% icon galaxy-eye %} the resulting files
>
{: .hands_on}

A number of new items will appear in your History, each corresponding to the outputs selected in the PeptideShaker parameters. The Peptide Shaker’s PSM report is used as an input for the BlastP analysis. Before performing BlastP analysis. The Query Tabular tool and few test manipulation tools are used to remove spectra that belongs to the reference proteins. The output tabular file “Peptides_for_Blast-P_analysis” will contain only those spectra that did not belong to any known proteins.

# Create a SQLite database for peptide, protein and genomic annotation visualization

The mzidentml output from the Peptide shaker is converted into an sqlite database file by using the mz to sqlite tool. This sqlite output is used to open the Multi-omics visualization platform, wherein you can view the spectra of the peptides using Lorikeet parameters. To open the MVP viewer, click on the “Visualize" {% icon galaxy-visualise %}  icon and select "MVP Application" ( this will open the interactive multi-omics viewer)


> <hands-on-title>mz to sqlite</hands-on-title>
>
> This tool extracts mzidentml and its associated proteomics datasets into a sqlite db
>
> 1. {% tool [mz to sqlite](toolshed.g2.bx.psu.edu/repos/galaxyp/mz_to_sqlite/mz_to_sqlite/2.1.1+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Proteomics identification files"*: `PeptideShaker_mzidentml`
>    - {% icon param-collection %} *"Proteomics Spectrum files"*: `Mo_Tai_MGFs`
>    - {% icon param-file %} *"Proteomics Search Database Fasta"*: `Uniprot_cRAP_SAV_indel_translatedbed.FASTA`
>
>    ![mz2sqlite](../../images/mz2sqlite.png){:width="20%"}
>
{: .hands_on}

The next step is to remove known peptides from the list of PSMs that we acquired from the Peptide Shaker results. For that, we need to perform Query tabular to extract the list of known peptides from the UniProt and cRAP database.


## Query Tabular

> <hands-on-title>Remove Reference proteins</hands-on-title>
>
>  1. {% tool [Query Tabular](toolshed.g2.bx.psu.edu/repos/iuc/query_tabular/query_tabular/3.3.2) %} with the following parameters:
>
>     - {% icon param-repeat %} **Insert Database Table** (b): `PSM report`
>       - Section **Filter Dataset Input**:
>         - {% icon param-repeat %} **Insert Filter Tabular Input Lines**
>         - *"Filter by"*:  `skip leading lines`
>         - *"Skip lines"*: `1`
>
>     - Section **Table Options**:
>       - *"Specify Name for Table"*: `psms`
>       - *"Use first line as column names"* : `No`
>       - *"Specify Column Names (comma-separated list)"*:`id,Proteins,Sequence`
>       - *"Only load the columns you have named into database"*: `Yes`
>       - {% icon param-repeat %} **Table Index**
>         - *"This is a unique index"*: `No`
>         - *"Index on Columns"*: `id`
>
>     - {% icon param-repeat %} **Insert Database Table** (c):`PSM report`
>
>     - Section **Filter Dataset Input**
>       - {% icon param-repeat %} **Insert Filter Tabular Input Lines**
>         - *"Filter by"*:  `skip leading lines`
>         - *"Skip lines"*: `1`
>       - {% icon param-repeat %} **Insert Filter Tabular Input Lines**
>         - *"Filter by"*:  `select columns`
>         - *"Enter column numbers to keep"*: `1,2`
>       - {% icon param-repeat %} **Insert Filter Tabular Input Lines**
>         - *"Filter by"*:  `normalize list columns,replicate rows for each item in the list`
>         - *"Enter column numbers to normalize"*: `2`
>         - *"List item delimiter in column"*: `,`
>
>     - Section **Table Options**:
>       - *"Specify Name for Table"*: `prots`
>       - *"Use first line as column names"* : `No`
>       - *"Specify Column Names (comma-separated list)"*:`id,prot`
>       - *"Only load the columns you have named into database"*: `Yes`
>       - {% icon param-repeat %} **Insert Table Index**:
>         - *"This is a unique index"*: `No`
>         - *"Index on Columns"*: `prot,id`
>
>     - {% icon param-repeat %} **Insert Database Table** (a): `Reference_Protein_Accessions`
>     - Section **Table Options**:
>       - *"Tabular Dataset for Table"*: `Uniprot`
>       - *"Use first line as column names"* : `No`
>       - *"Specify Column Names (comma-separated list)"*:`prot`
>       - {% icon param-repeat %} **Insert Table Index**:
>         - *"This is a unique index"*: `No`
>         - *"Index on Columns"*: `prot`
>
>
>       > <comment-title></comment-title>
>       >
>       > By default, table columns will be named: c1,c2,c3,...,cn (column names for a table must be unique).
>       > You can override the default names by entering a comma separated list of names, e.g. `,name1,,,name2`
>       > would rename the second and fifth columns.
>       > Check your input file to find the settings which best fits your needs.
>       >
>       {: .comment}
>
>
>     - *"Save the sqlite database in your history"*: `No`
>
>       > <comment-title>Querying an SQLite Database</comment-title>
>       > **Query Tabular** can also use an existing SQLite database.
>       > Activating `Save the sqlite database in your history`
>       > will store the generated database in the history, allowing to reuse it directly.
>       {: .comment}
>
>     - *"SQL Query to generate tabular output"*:
>       ```
>       SELECT psms.*
>       FROM psms
>       WHERE psms.id NOT IN
>       (SELECT distinct prots.id
>       FROM prots JOIN uniprot ON prots.prot = uniprot.prot)
>       ORDER BY psms.id
>       ```
>
>     - *"include query result column headers"*: `Yes`
>
>  - Click **Run Tool** and inspect the query results file after it turns green.
>
>
{: .hands_on}

The output from this step is that the resultant peptides would be those which doesn't belong in the Uniprot or cRAP database. The query tabular tool is used again to create a tabular output containing peptides ready for Blast P analysis.


> <hands-on-title>Query Tabular</hands-on-title>
>
>  1. {% tool [Query Tabular](toolshed.g2.bx.psu.edu/repos/iuc/query_tabular/query_tabular/3.3.2) %} with the following parameters:
>
>     - {% icon param-repeat %} **Insert Database Table** (b): `Query results`
>     - Section **Filter Dataset Input**:
>       - {% icon param-repeat %} **Insert Filter Tabular Input Lines**
>         - *"Filter by"*:  `skip leading lines`
>         - *"Skip lines"*: `1`
>
>
>     - Section **Table Options**:
>       - *"Specify Name for Table"*: `psm`
>       - *"Use first line as column names"* : `No`
>       - *"Specify Column Names (comma-separated list)"*:`id,Proteins,Sequence`
>       - *"Only load the columns you have named into database"*: `Yes`
>
>     - *"SQL Query to generate tabular output"*:
>       ```
>       SELECT Sequence || ' PSM=' || group_concat(id,',') || ' length='
>       || length(Sequence) as "ID",Sequence
>       FROM  psm
>       WHERE length(Sequence) >6
>       AND length(Sequence) <= 30
>       GROUP BY Sequence
>       ORDER BY length(Sequence),Sequence
>       ```
>     - *"include query result column headers"*: `Yes`
>
>     - Click **Run Tool** and inspect the query results file after it turns green.
>
> 2. Rename {% icon galaxy-pencil %} the output as `Peptides_for_Blast-P_analysis`
>
> ![QT](../../images/QT_output.png)
>
{: .hands_on}


# Getting data Blast-P ready


BlastP search is carried out with the PSM report (output from PeptideShaker). Before, BlastP analysis the “Peptides_for_Blast-P_analysis” is first converted from Tabular format to FASTA file format which can be easily read by the BlastP algorithm. This is done with the help of “Tabular to FASTA” conversion tool.
The short BlastP uses parameters for short peptide sequences (8-30 aas). Please use the rerun option to look at the parameters used.

> <hands-on-title>Tabular to FASTA (version 1.1.1)</hands-on-title>
> 1. {% tool [Tabular-to-FASTA](toolshed.g2.bx.psu.edu/repos/devteam/tabular_to_fasta/tab2fasta/1.1.1) %} with the following parameters:
>    - *"Tab-delimited file"*: `Peptides_for_Blast-P_analysis`
>    - *"Title column"*: `1`
>    - *"Sequence Column"*:`2`
>
{: .hands_on}



The output FASTA file is going to be subjected to BLAST-P analysis.

> <comment-title>Tool Versions</comment-title>
>
> The tools are subjected to changes while being upgraded.
> Thus, running the workflow provided with the tutorial, the user might need to make sure they are using the latest version including updating the parameters.
>
{: .comment}



# Conclusion


This completes the walkthrough of the proteogenomics database search workflow. This tutorial is a guide to performing database searching with mass spectrometry files and having peptides ready for Blast-P analysis, you can perform follow-up analysis using the next GTN "Proteogenomics Novel Peptide Analysis".
Researchers can use this workflow with their data also, please note that the tool parameters, reference genomes, and the workflow will need to be modified accordingly.

This workflow was developed by the Galaxy-P team at the University of Minnesota. For more information about Galaxy-P or our ongoing work, please visit us at [galaxyp.org](https://galaxyp.org)


{: .comment}
