---
layout: tutorial_hands_on
title: Introduction to Galaxy as an RDM platform
zenodo_link: https://zenodo.org/records/18803585/
level: Introductory
questions:
- Which RDM features does Galaxy offer?
objectives:
- Familiarize yourself with the basics of Galaxy
- Learn how to import data
- Learn how to process and analyze data
- Learn how to share your work

time_estimation: 3H
key_points:
subtopic: core
priority: 3

contributions:
  authorship:
    - shiltemann
  editing:
    - Sch-Da
  funding:
    - uni-freiburg
    - nfdi4plants

---


This tutorial aims to familiarize you with the Galaxy user interface, with a special focus on highlighting Galaxy's many RDM (Research Data Management) features.

Galaxy has over 10,000 available tools in it's [Tool Shed](https://toolshed.g2.bx.psu.edu/), covering a wide variety of scientific domains, ranging from life sciences, to astronomy, and digital humanities, and covering techniques from simple text manipulation to advanced machine learning and other complex algorithms.

To keep this tutorial accessible for people with different backgrounds, we perform a toy analysis on a tabular dataset, namely a table of all athletes competing in the Olympics. The question we ask ourselves is ***"What is the age distribution of Olympic athletes?"***. In addition, we want to make sure our analysis is reproducible, so that it can be easily be repeated on different datasets, and shared with others.


> <agenda-title></agenda-title>
>
> In this tutorial, we will:
>
> 1. TOC
> {:toc}
>
{: .agenda}


## Overview

### The Research Data Life Cycle

The research life cycle refers to the series of stages through which a research project or study progresses from inception to completion. Although the specifics of the research process vary across disciplines, they share several key phases that help ensure that the research is systematic, rigorous, and produces reliable results. From **planning** and designing your study, to **collecting**, **processing**, and **analysing** your data, evaluating results, and finally **preserving** and **sharing** your data and findings for **reuse** by others.

![RDM life cycle]({% link topics/introduction/images/galaxy-intro-rdm/rdm-overview.png %}){: style="width:40%"}

### Galaxy as part of the RDM Life Cycle

Galaxy supports you in your research throughout the different stages of the life cycle, covering the steps from data collection to data reuse.

![The RDM lifecycle with Galaxy features listed for each stage]({% link topics/introduction/images/galaxy-intro-rdm/rdm-all.png %}){: style="width:75%"}

For  more information, see also [RDMKit Galaxy page](https://rdmkit.elixir-europe.org/galaxy_assembly)


### Watch

Below is a 5-minute video introducing Galaxy as a cross-domain RDM platform.

{% include _includes/youtube.html id="2w8okrORVtM"  title="Introduction to Galaxy: Image analysis in Life Sciences, Climate, Earth Science and Astronomy" nofigure="true" %}


In this tutorial, we will take you through all the stages of the Research data life cycle, and provide a hands-on introduction to the Galaxy platform at each stage.


## The Galaxy Web Interface

Before we go into the stages of the RDM life cycle, let's start with the basics and log into Galaxy and explore the graphical user interface.

### Create an account on a Galaxy instance/server
If you already have an account, skip to the next section!

In Galaxy, *server* and *instance* are often used interchangeably. These terms basically mean that different regions have different Galaxy servers/instances, with slightly different tool installations and appearances. If you don't have a specific server/instance in mind, we recommend registering at one of the main public servers/instances, detailed below.

{% snippet faqs/galaxy/account_create.md %}

Depending on your Galaxy server, you may also be able to log in with your institutional or social account.

> <tip-title>Galaxy Single Sign-on </tip-title>
>
> In the Galaxy login screen, you may find the option to log in with
> an institutional account. Which options are offered depend on which Galaxy
> you are using.
>
> ![example of SSO options to log into Galaxy]({% link topics/introduction/images/galaxy-intro-rdm/login-sso.png %}){: width="40%"}
> > ![example of SSO options to log into Galaxy]({% link topics/introduction/images/galaxy-intro-rdm/login-sso2.png %}){: width="40%"}
{: .tip}

### What does Galaxy look like?

> <hands-on-title>Log in to Galaxy</hands-on-title>
> 1. Open your favorite browser (Chrome, Safari, Edge or Firefox as your browser, not Internet Explorer!)
> 2. Browse to your Galaxy instance
> 3. Log in or register
>
> ![Screenshot of Galaxy Australia with the register or login button highlighted](../../images/galaxy-login.png)
>
>   > <comment-title>Different Galaxy servers</comment-title>
>   >  This is an image of Galaxy Australia, located at [usegalaxy.org.au](https://usegalaxy.org.au/)
>   >
>   > The particular Galaxy server that you are using may look slightly different and have a different web address.
>   >
>   > You can also find more possible Galaxy servers at the top of this tutorial in **Available on these Galaxies**
>   {: .comment}
{: .hands_on}

The Galaxy homepage is divided into four sections (panels):
- The **Activity Bar** on the left: _This is where you will navigate to the resources in Galaxy (Tools, Workflows, Histories etc.)_
- Currently active **"Activity Panel"** on the left: _By default, the {% icon tool %} **Tools** activity will be active and its panel will be expanded_
- **Viewing panel** in the middle: _The main area for context for your analysis_
- **History** of analysis and files on the right: _Shows your "current" history; i.e.: Where any new files for your analysis will be stored_

![Screenshot of the Galaxy interface with aforementioned structure](../../images/galaxy_interface.png)

The first time you use Galaxy, there will be no files in your history panel.


## Collect: Data import

![The RDM lifecycle with the collect stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-collect.png %}){: style="width:50%"}


### The Galaxy History

Your "History" is in the panel at the right. This is where all the files you import or create will be shown. It is also a record of the actions you have taken. Galaxy tracks the provenance of all datasets; which tools were used to create them, which version, and which parameter settings. Everything you need to write the methods section of your journal publication.
Before we begin, let's name our history. It is recommended to create a new history for each analysis that you perform, and giving your histories good names will help keep your analyses organized.


#### Name your current history

> <hands-on-title>Name history</hands-on-title>
> 1. Go to the **History** panel (on the right)
> 2. Click {% icon galaxy-pencil %} (**Edit**) next to the history name (which by default is "Unnamed history")
>
>    ![Screenshot of the galaxy interface with the history name being edited, it currently reads "Unnamed history", the default value. An input box is below it.]({% link shared/images/rename_history.png %}){:width="250px"}
>
>    > <comment-title></comment-title>
>    >
>    > In some previous versions of Galaxy, you will need to click the history name to rename it as shown here:
>    > ![Screenshot of the galaxy interface with the history name being edited, it currently reads "Unnamed history", the default value.](../../../../shared/images/rename_history_old.png){:width="320px"}
>    {: .comment}
>
> 3. Type in a new name, for example, "Olympics Data Analysis"
> 4. Click **Save**
>
> > <comment-title>Renaming not an option?</comment-title>
> > If renaming does not work, it is possible you aren't logged in, so try logging in to Galaxy first. Anonymous users are only permitted to have one history, and they cannot rename it.
> {: .comment}
>
{: .hands_on}


### Upload a dataset

<!--
https://zenodo.org/records/18803585/files/olympics-2010-winter.tsv
https://zenodo.org/records/18803585/files/olympics-2008-summer.tsv
https://zenodo.org/records/18803585/files/olympics-1896-2016.zip
-->

> <comment-title> Galaxy Data Import Options </comment-title>
> There are various ways to get data into Galaxy
> - Uploading from **your computer**
> - Import from **URL**
> - Import directly from **data repositories**, e.g.
>   - SRA/NCBI/EBI/Uniprot (Biological Sequence Data)
>   - OMERO (Image database)
>   - Copernicus (Climate Data)
    - CERN Open Data (Particle Physics)
>   - many more (See "Get Data" section of the Tool panel in Galaxy)
> - **Bring-your-own-data** (e.g. Dropbox, Gdrive, OneData, eLabFTW)
>
>   {% snippet faqs/galaxy/manage_your_repositories.md %}
>
> - Connections to your **LIMS** system
>
>   {% snippet faqs/galaxy/importing-data-from-sierra-lims.md %}
>
{: .comment}

For this tutorial, we will import datasets from the general-purpose FAIR data repository [Zenodo](https://zenodo.org).


> <hands-on-title>Upload a file from URL</hands-on-title>
>
> 1. At the top of the **Activity Bar**, click the {% icon galaxy-upload %} **Upload** activity
>
>    ![upload data button shown in the galaxy interface](../../images/upload-data.png)
>
>    This brings up a box:
>
>    ![the complicated galaxy upload dialog, the 'regular' tab is active with a large textarea to paste subsequent URL](../../images/upload-box.png)
>
> 3. Click **Paste/Fetch data**
> 4. Paste in the address of a file:
>
>    ```
>    https://zenodo.org/records/18803585/files/olympics-2010-winter.tsv
>    ```
>
> 5. Click **Start**
> 6. Click **Close**
>
{: .hands_on}

Your uploaded file is now in your current history.
When the file has uploaded to Galaxy, it will turn green.

> <comment-title></comment-title>
> After this you will see your first history item (called a "dataset") in Galaxy's right panel. It will go through
> the gray (preparing/queued) and yellow (running) states to become green (success).
>
{: .comment}

What is this file?

> <hands-on-title>View the dataset content</hands-on-title>
> 1. Click the {% icon galaxy-eye %} (eye) icon next to the dataset name, to look at the file content
>
>    ![galaxy history view showing a single dataset olympics-2010-winter.tsv. Display link is being hovered.]({% link topics/introduction/images/galaxy-intro-rdm/eye-icon.png %}){:width="25%"}
{: .hands_on}

The contents of the file will be displayed in the central Galaxy panel. If the dataset is large, you will see a warning message which explains that only the first megabyte is shown.


This file contains a table listing all athletes who competed in the 2010 Winter Olympics in Oslo.

![galaxy center panel view showing a single dataset olympics-2010-winter.tsv. ]({% link topics/introduction/images/galaxy-intro-rdm/file-preview.png %} "Preview of the dataset in Galaxy. Each row corresponds to an athlete, and each column provides further information about this athlete including birthyear, weight, medals.")


> <question-title> Explore the dataset </question-title>
>
> 1. How many athletes participated in this Olympics?
> 2. What was the location of this Olympic games?
>
> > <solution-title></solution-title>
> > 1. 4402 athletes. Each row signifies an athlete, there are 4403 rows, one of which is the header row.
> > 2. Vancouver. This information is given in column 13.
> {: .solution}
{: .question}


### Dataset attributes (metadata)

Let's have a look at the metadata that Galaxy tracks for your datasets.

> <hands-on-title> Explore metadata </hands-on-title>
>
> 1. **Expand** the item in your history by clicking on its name
>    - Here you will see a peek of the contents, some basic file attributes such as the format, number of lines, and number of columns
>
>    ![the history item expanded, showing dataset preview, number of links, and a series of buttons]({% link topics/introduction/images/galaxy-intro-rdm/file-expanded.png %})
>
> 2. **Click** on the **"Dataset Details"** {% icon details %} button
>    - Here you can see further metadata such as file size, creation date, hash, format, original URL, and more
>    - Scrolling down you will also see details of the upload job that performed the import. We will look more closely at this later.
>
>    ![dataset details]({% link topics/introduction/images/galaxy-intro-rdm/dataset-details.png %} "Screenshot of the dataset details")
>
> 3. **Rename** the file to include the city of the Olympic. You can do this by **editing the dataset attributes**
>    - This can be done by clicking on the **Edit** tab at the top of your screen, or the pencil icon {% icon galaxy-pencil %} on the expanded dataset.
>    - For example, rename it to `2010 Winter Olympics Vancouver`
>
>    {% snippet faqs/galaxy/datasets_rename.md %}
>
>    ![the renamed dataset]({% link topics/introduction/images/galaxy-intro-rdm/dataset-renamed.png %})
>
{: .hands_on}


## Process: Data preparation and QC

![The RDM lifecycle with the process stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-process.png %}){: style="width:50%"}


The first steps of an analysis are often data cleaning and quality control steps.
Galaxy offers many tools that can help prepare your data for analysis, such as format conversions and data manipulation tools.

### Use a tool

Recall that our research question in this tutorial is "What is the age distribution of Olympic athletes?"
Looking at the dataset, you will see that we do not have an "age" column in our table. We do however, have a column with the birth year
of each athlete, and a column containing the year of the olympics. Let's prepare our data for analysis by calculating a new age column
based on these two existing columns.


> <hands-on-title>Find a tool</hands-on-title>
>
> 1. Search for the tool {% tool [Compute - on rows](toolshed.g2.bx.psu.edu/repos/devteam/column_maker/Add_a_column1/2.1) %}
>    - Click on **Tools** in the Activity bar
>    - Enter "Compute" in the search bar
>
> 2. Open the tool
>    - You will see the tool form in the center panel of Galaxy
>
> 3. Scroll down to the **Help** section and read about the tool
>    - Here you will always find usage information about the tool, including citations and links to tutorials explaining the tool.
>    - How could we use this tool to add an age column to our dataset?
>
>    ![tool help section]({% link topics/introduction/images/galaxy-intro-rdm/tool-help.png %} "Help section of the Compute tool")
>
{: .hands_on}

We can use this tool to comute an age column for our dataset, but first we must ask ourselves some questions:

> <question-title> Explore the dataset </question-title>
>
> 1. Which column contains the birth year information
> 2. Which column contains the year of the Olympics?
> 3. How can we compute the age of the athlete from these columns?
>
> > <solution-title></solution-title>
> > 1. column 4 (c4)
> > 2. column 10 (c10)
> > 3. we subtract the columns, `c10-c4`
> {: .solution}
{: .question}


We now have what we need to add an age column to our dataset, let's do it:

> <hands-on-title>Use a tool</hands-on-title>
>
> 1. {% tool [Compute - on a row](toolshed.g2.bx.psu.edu/repos/devteam/column_maker/Add_a_column1/2.1) %} with the following parameters
>    - {% icon param-file %} *"Input file"*: `2010 Winter Olympics Vancouver`
>    - *"Input has a header line with column names?"*: `Yes`
>    - *"Expressions"*
>      - {% icon plus %} Insert Expressions
>        - *"Add expression"*: `c10-c4`
>        - *"Mode of the operation"*: `Append`
>        - *"The new column name"*: `age`
> 2. **Run** the tool
>
{: .hands_on}

This tool will run and a new output dataset will appear at the top of your history panel.

> <hands-on-title> Check the results </hands-on-title>
>
> 1. **View** {% icon galaxy-eye %} the resulting file
>    - make sure the new column was successfully added, and the column header is "age"
>
> > <question-title> </question-title>
> >
> > 1. What column number is our new "age" column?
> > 2. What age is the first Olympian in our file, *Muhammad Abbas*?
> >
> > > <solution-title></solution-title>
> > > 1. The column was added at the end, column 16.
> > > 2. Age 23.
> > {: .solution}
> {: .question}
>
>
{: .hands_on}


{% snippet faqs/galaxy/tutorial_mode.md %}


### Tool provenance

We already examined the attributes for the file we uploaded. For datasets that result from running tools, Galaxy tracks even more provenance.
Let's look at this now

> <hands-on-title> Explore metadata </hands-on-title>
>
> 1. **Expand** the item in your history by clicking on its name
> 2. **Click** on the **"Dataset Details"** {% icon details %} button
>
{: .hands_on}

Here you will see all the metadata that Galaxy keeps track of. It has all the same basic information as we saw with the uploaded file.
In addition, it shows which tool produced this output, complete with exact parameter settings and tool version.

![tool parameters of our tool run]({% link topics/introduction/images/galaxy-intro-rdm/tool-provenance.png %} "Parameters of the job (tool run) that produced this dataset")

![job information of our tool run]({% link topics/introduction/images/galaxy-intro-rdm/job-information.png %} "Job information of our tool run. ")



> <question-title> Examine the Job metadata </question-title>
>
> 1. What was the version of the tool that produced your dataset?
> 2. What was the command that was run behind the scenes?
>
> > <solution-title></solution-title>
> > 1. Version 2.1. This can be found under *"Job Information -> Galaxy Tool ID"*, where the last part is the version. E.g. `toolshed.g2.bx.psu.edu/repos/devteam/column_maker/Add_a_column1/2.1`. Note that this may be different for you if a newer version has been released since writing this tutorial.
> > 2. The command that is run can be found under *"Job Information -> Command Line"*. It will be something like:
> >    ```
> >    python '/opt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/column_maker/aff5135563c6/column_maker/column_maker.py' --column-types int,str,str,int,float,float,str,str,str,int,str,str,str,str,str  --header --file '/data/jwd07/main/097/599/97599988/configs/tmp1vp1f4gh' --fail-on-non-existent-columns --fail-on-non-computable '/data/dnb12/galaxy_db/files/7/a/6/dataset_7a6bad76-3181-45e2-a460-31cbe2a6e4a3.dat' '/data/jwd07/main/097/599/97599988/outputs/dataset_5ce07003-fe0c-4836-8f16-b64f25dc9219.dat'
> >    ```
> {: .solution}
{: .question}


### Visualise a dataset


> <hands-on-title> Visualise a dataset </hands-on-title>
>
> 1. Expand the output from **Compute** {% icon tool %}
> 2. Click on the **visualise** {% icon galaxy-visualise %} icon
>
>    ![visualisation button]({% link topics/introduction/images/galaxy-intro-rdm/visualise-button.png %})
>
> 3. Select the **Boxplot** option
>
>    ![visualisation options]({% link topics/introduction/images/galaxy-intro-rdm/visualise-options.png %})
>
> 4. Change **Column of y-axis values** to `Column: 16` (our new age column)
>
{: .hands_on}

This is a quick way to get a feeling for our data.

![histogram visualisation]({% link topics/introduction/images/galaxy-intro-rdm/visualisation-boxplot-labels.png %} "screenshot of the resulting boxplot. Hovering your mouse over the plot shows you the labels")

> <question-title></question-title>
>
> 1. What age range were our athletes?
>
> > <solution-title></solution-title>
> > 1. Based on the box plot, it looks like our youngest athelete was 15, and our oldest 51. The mean age was 25.
> {: .solution}
{: .question}


> <tip-title> Save your visualisation </tip-title>
>
> 1. Click on the **Save** {% icon cloud-upload %} icon at the top-right
>
>    ![visualisation save button]({% link topics/introduction/images/galaxy-intro-rdm/viz-save.png %})
>
> 2. In the Activity bar, click on **Visualization**
> 3. Click on **Saved Visualizations** at the top of the panel
>
>    ![saved visualisations]({% link topics/introduction/images/galaxy-intro-rdm/visualisations-saved.png %})
>
> 4. Here you will find your saved visualisations
>    - Here you can view, adjust, rename your previously saved visualisaions
>
>    ![saved visualisations list]({% link topics/introduction/images/galaxy-intro-rdm/visualisations-list.png %})
>
{: .tip}


### Re-run a tool

Our file only contained information for a single Olympics, let's have a look at a second Olympics as well.

We will import another file from Zenodo, but in a slightly different way. Instead of providing the download URL for the dataset, we can also browse Zenodo repositories (and many other data repositories) directly from the Galaxy upload menu.

> <hands-on-title> Upload a second dataset </hands-on-title>
>
> 1. **Option 1:** Choose from Repository
>    - Open the **Upload** window
>    - At the bottom, click on  "Choose from Repository"
>
>      ![choose from repositories button]({% link topics/introduction/images/galaxy-intro-rdm/upload-from-repositories.png %})
>
>    - Search for *"Zenodo"* at the top
>      - If you do not find anything, this is not supported on your Galaxy yet, please skip to option 2 below
>    - Search for the repository with the same name as this tutorial *"Introduction to Galaxy as an RDM platform"*
>
>      ![choose from repositories button]({% link topics/introduction/images/galaxy-intro-rdm/zenodo-search.png %})
>
>    - Select the file `olympics-2010-summer.tsv`
>
>      ![choose from repositories button]({% link topics/introduction/images/galaxy-intro-rdm/zenodo-repo-import.png %})
>
>    - Click **Start**
>
> 2. **Option 2:** From URL (same method as before)
>
>    ```
>    https://zenodo.org/records/18803585/files/olympics-2008-summer.tsv
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
> > <question-title> Examine the file </question-title>
> >
> > 1. Which Olympics is this file for? Which city was it held in?
> >
> > > <solution-title></solution-title>
> > > 1. This file is from the 2008 summer Olympics in Beijing
> > >
> > {: .solution}
> {: .question}
{: .hands_on}


Now that we have a second dataset, we want to run the same **Compute** {% icon tool %} tool on this data so that we get an age column.
We could open the tool again, and re-configure all the settings, but there is an easier way to repeat what we did before.

> <hands-on-title> Re-run a tool </hands-on-title>
>
> 1. Click the **Re-run** {% icon dataset-rerun %} button on the output from the **Compute tool**
>    - You will see the tool form with the exact same settings we used before
>    - Because Galaxy tracks all the parameter settings, it is easy to repeat a tool on new data, without having to choose all the parameters again.
>
>    ![rerun button]({% link topics/introduction/images/galaxy-intro-rdm/rerun-tool.png %})
>
> 2. **Change the input dataset** {% icon param-file %} to the summer olympics file we just uploaded
>    - Run the tool
>
> > <question-title> How did it go? </question-title>
> >
> > 1. What do you see?
> >
> > > <solution-title></solution-title>
> > > 1. If all went well, something went wrong in this step. That is, your dataset turned red instead of green. Not to worry, we will show you how to troubleshoot errors next.
> > >
> > >    ![a red failed dataset in history]({% link topics/introduction/images/galaxy-intro-rdm/dataset-failed.png %})
> > >
> > {: .solution}
> {: .question}
>
>
{: .hands_on}

Oh no! The dataset turned red! This means something went wrong. In the next section we will show you how you can troubleshoot errors in Galaxy.


### Troubleshooting errors

So something went wrong with one of your tools. This will happen now and then, and can have different causes. It might be something you can fix yourself (e.g. a problem with the input dataset), or it might be something that needs to be fixed in Galaxy (e.g. a bug in the tool). Next we will see how you can find more information about the error, and submit a bug report if you think it might be a problem with the tool.


> <hands-on-title> Troubleshoot a failed tool </hands-on-title>
>
> 1. Click on the **bug icon** {% icon galaxy-bug %} on the failed (red) dataset
>
>    ![the bug icon on a historty item]({% link topics/introduction/images/galaxy-intro-rdm/tool-bug.png %})
>
{: .hands_on}

You will now see details about the error in the center panel:

![Error tab of dataset details]({% link topics/introduction/images/galaxy-intro-rdm/galaxy-bugreport.png %} "the error tab of the dataset details page. This page shows us the error message (stderr) and other tool logs (stdout). It also has a form to submit a bug report at the bottom.")

The error messages can sometimes be a bit cryptic, but the more you use the tools the easier it will get. If you do not know how to fix the error yourself, you can submit a bug report at the bottom of this page. This will be sent to the administrators of the Galaxy you are using.


> <question-title> Examine the Error message </question-title>
>
> 1. Can you guess what went wrong based on the error message?
> 2. Is this something we can fix? how?
>
> > <solution-title></solution-title>
> > 1. The error message says `could not convert string to float: 'NA'`. This suggests there is a line in the input file that contains unexpected value (`NA`). This is a common way to denote a missing value, but if we assume this column to be a number and use it in our calculation
> >    things can go wrong.
> >
> >    ```
> >    Failed to convert some of the columns in line #1859 to their expected types.
> >    The error was: "could not convert string to float: 'NA'" for the line:
> >    "19504	Cha Yong-Hwa	F	NA	145	39	North Korea	PRK	2008 Summer	2008
> >     Summer	Beijing	Gymnastics	Gymnastics Women's Individual All-Around	NA"
> >    ```
> >
> >    Apparently, no birth year was known for this athlete from North Korea
> >
> > 2. Yes, since the problem is with our input file, this is something we can fix ourself.
> >    - One solution could be to remove all lines that contain `NA` in the birth year column.
> >    - Another would be to replace all `NA` values with `nan` (not a number), which is the appropriate way to indicate missing values in numeric columns
> >    - In our case, there is an easier option: we can tell the **Compute** {% icon tool %} tool how to deal with such cases.
> >
> {: .solution}
{: .question}

The error is caused because Galaxy is trying to interpret the birthyear column as a number, but cannot do this for columns
containing an "NA" value. We can tell the **Compute** {% icon tool %} tool not autodetect the column type, and tell it what to


So now that we know what caused the error, let's fix it by re-running our tool once more, with different error-handling settings.


> <hands-on-title> Re-run the tool with error handling parameters </hands-on-title>
>
> 1. **Re-run** {% icon dataset-rerun %} the failed (red) dataset
>    - Expand the **Error Handling** section at the bottom of the tool form
>      - {% icon param-toggle %} *"Autodetect column types"*: `No`
>      - *"If an expression cannot be computed for a row"*: `Skip the row`
>    - Change the *"Expression"* parameter to: `int(c10)-int(c4)`
>      - the `int()` part tells the tool to turn the value into an integer (whole number). Since we told the tool to not autodetect anymore, we need to tell it how to interpret the values in the column.
>    - Run the tool
>
>
>    ![expression of the tool form]({% link topics/introduction/images/galaxy-intro-rdm/compute-error-handling-expression.png %})
>
>    ![error handling section of the tool form]({% link topics/introduction/images/galaxy-intro-rdm/compute-error-handling.png %})
>
>
> > <question-title> </question-title>
> >
> > 1. What age is the first Olympian in this file, *Ragnhild Margrethe Aamodt*?
> >
> > > <solution-title></solution-title>
> > > 1. Age 27. The age column is the last one.
> > {: .solution}
> {: .question}
{: .hands_on}

If this solution seemed a bit cryptic, don't worry too much, there are always multiple ways to solve the problem. The important thing is that you ran into a problem, looked at the error, and then solved it.

If you get an error message that you don't understand, or don't know how to solve, you can always ask for help in one of our **support channels**.

{% snippet faqs/galaxy/support.md %}


#### Starring your favourite tools

Since Galaxy has so many tools to choose from, once you find one that is useful for you, you will likely want to use it more often.
To make it easier to find your favorite tools, you can star them.

> <hands-on-title> Star/Favourite a tool </hands-on-title>
>
> 1. **Star** {% icon galaxy-star %} the **Compute** {% icon tool %} tool
>
>    {% snippet faqs/galaxy/tools_favorite.md %}
>
> 2. You can access your favorite tools by clicking on the {% icon galaxy-star %} icon at the top of the **Tool Panel**
>    - this will filter the tool panel for the tools you have starred and your most-used tools
>
{: .hands_on}




### Optional: Use OpenRefine instead

Galaxy also offers various *Ineractive Tools*. For example, we could have performed these preprocessing steps with OpenRefine as well. Using these tools is not quite as reproducible as using standard Galaxy tools, but it is great for the exploratory analysis phase of research.

TODO: finish this section


### Keeping your history clean

If you have failed items in your history, you might want to delete them. This helps keep your history organized.


> <hands-on-title> Delete failed dataset </hands-on-title>
>
> 1. Click on the **trashcan icon** {% icon galaxy-delete %} on the failed (red) dataset
>
>
> > <tip-title> Deleted by accident? </tip-title>
> >
> > Did you accidentally delete a dataset you didn't mean to delete? Not to worry, your data is not gone yet.
> > You can show these deleted datasets in your history, and **undelete** them.
> >
> > 1. Click on  **include deleted** {% icon galaxy-delete %} at the top of your history
> >    - so not on the dataset, but at the top of the history panel
> >    - you will see the deleted dataset appear in your history again
> >   - if you expand the deleted dataset, the delete icon has turned into an undelete icon
> >
> >    ![history option to include deleted datasets]({% link topics/introduction/images/galaxy-intro-rdm/history-include-deleted.png %})
> >
> {: .tip}
{: .hands_on}

Your dataset is now gone from your history. But deleting it does not remove it completely yet. So if you delete something by accident, you can still view it and undelete it.

You can also delete datasets in bulk

{% snippet faqs/galaxy/datasets_deleting.md %}

Sometimes you really want to permanently delete a dataset, for example to free up your storage quota. By default you get 250 GB storage (exact number may depend on your Galaxy), and usually more can be requested temporarily. If you are running out of storage space, you can *purge* (permanently delete) datasets as well. This cannot be undone.

{% snippet faqs/galaxy/datasets_purging_datasets.md %}

We recommend always keeping your history clean, and deleting any failed steps.


> <comment-title> More data cleaning and preprocessing tools </comment-title>
>
> Galaxy offers a wide range of basic file manipulation tools that are very helpful for data cleaning.
> Operations such as file transformations, filtering, sorting, grouping, joining, splitting, etc are all possible inside Galaxy
>
> For more practice with such tools, please see our [Data Manipulation Olympics tutorial]({% link topics/introduction/tutorials/data-manipulation-olympics/tutorial.md %})
>
{: .comment}


### Scaling up

Now that we have preprocessed our data, we can continue our analysis, but before we do that, let's explore some more
Galaxy RDM features that can help you manage your research data and analyses.


#### Multiple histories

You can have multiple histories in Galaxy, to organize your different analyses. We will now start a second history,
and show you how you can switch between histories and move data from one history to another.


> <hands-on-title> Create a second History </hands-on-title>
>
> 1. Create a new History
>
>    {% snippet faqs/galaxy/histories_create_new.md %}
>
> 2. **Name** {% icon galaxy-pencil %} your history
>    - call it "Multi-Olympics Analysis"
>
{: .hands_on}

You have now created a new, empty history. You can easily switch back and forth between histories as needed

{% snippet faqs/galaxy/histories_switch.md %}

We will continue our analysis in this new history, and use collections and dataset tags to analyze multiple datasets simultaneously,
and keeping our data organized.

To avoid re-uploading our Olympics dataset and duplicating that data, we can simply copy the files from our previous history

> <hands-on-title> Copy datasets from another history  </hands-on-title>
>
> 1. View your histories side by side. Instructions are in the box below:
>
>    {% snippet faqs/galaxy/histories_side_by_side_view.md %}
>
>    ![multi-history view]({% link topics/introduction/images/galaxy-intro-rdm/multi-history.png %})
>
> 2. **Drag-and-drop** datasets between histories
>    - drag the Winter Olympics file to the new history
>    - do the same for the Summer Olympics file
>
>    ![multi-history view after file copy]({% link topics/introduction/images/galaxy-intro-rdm/multi-history-after.png %})
>
{: .hands_on}

We now have both our datasets in our new history. By doing it this way, rather than re-uploading the files, we do not increase our storage usage.

#### Dataset tags

You may have noticed in our first history that the results from the **Compute** {% icon tool %} tool were named *Compute on dataset 1* and *Compute on dataset 3*. To make it a bit more clear for ourselves which dataset was generated from which input file, we can add **dataset tags** {% icon galaxy-tags %}

> <hands-on-title> Add dataset tags </hands-on-title>
>
> 1. Add two dataset tags to the Winter Olympics file
>    - Make sure all tags start with a hashtag (`#`), then they will also be added to any datasets derived from it during analysis.
>    - tag 1: `#winter`
>    - tag 2: `#Vancouver`
>    - tag 3: `#2010`
>
>    {% snippet faqs/galaxy/datasets_add_tag.md %}
>
> 2. Do the same for the Summer Olympics file:
>    - tag 1: `#summer`
>    - tag 2: `#Beijing`
>    - tag 3: `#2008`
>
>    ![datasets with tags added]({% link topics/introduction/images/galaxy-intro-rdm/datasets-tagged.png %})
>
{: .hands_on}


#### Dataset collections

In order to easily run analysis on multiple datasets at once, we can create *dataset collections* in Galaxy:

> <hands-on-title> Create a collection </hands-on-title>
>
> 1. Create a collection out of our two olympic datasets
>
>    {% snippet faqs/galaxy/collections_autobuild_list.md %}
>
>    ![dataset collection builder]({% link topics/introduction/images/galaxy-intro-rdm/collection.png %})
>
> 2. Your history now has a single item in it
>    - it tells you what is inside *"a list with 2 tabular datasets"*
>
>    ![collection history item]({% link topics/introduction/images/galaxy-intro-rdm/collection-new.png %})
>
> 3. **Click on the collection** to see the files inside it
>
>    ![collection contents]({% link topics/introduction/images/galaxy-intro-rdm/collection-files.png %})
>
> 4. **Return to the history view** by clicking the link at the top of the history panel
>    - The link will be called something like *"History: Multi-Olympics Data Analysis:*
>
{: .hands_on}


We can now treat this collection the same way as a single dataset. If we use a collection as input of a tool, that tool will be run on each of
the datasets inside the collection. The result will again be a collection, this time with all the result files.


#### Run a tool on a collection

Now that we have set up our inputs as a collection with tags, lets see how to run the **Compute** {% icon tool %} tool on both datasets in the collection at once.

Remember that you starred {% icon galaxy-star %} the compute tool, so you can use that to easily find it again now!

> <hands-on-title> Run a tool on a collection </hands-on-title>
>
> 1. {% tool [Compute - on a row](toolshed.g2.bx.psu.edu/repos/devteam/column_maker/Add_a_column1/2.1) %} with the following parameters
>    - {% icon param-collection %} *"Input file"*: `Olympics Dataset` (collection)
>      - In front of this parameter, click on the {% icon param-collection %} icon to switch to collection input
>    - *"Input has a header line with column names?"*: `Yes`
>    - *"Expressions"*
>      - {% icon plus %} Insert Expressions
>        - *"Add expression"*: `int(c10)-int(c4)`
>        - *"Mode of the operation"*: `Append`
>        - *"The new column name"*: `age`
>    - Expand the **Error Handling** section at the bottom of the tool form
>      - {% icon param-toggle %} *"Autodetect column types"*: `No`
>      - *"If an expression cannot be computed for a row"*: `Skip the row`
>
>    {% snippet faqs/galaxy/tools_select_collection.md %}
>
> 2. **View** {% icon galaxy-eye %} the results
>
> > <question-title> What is our output? </question-title>
> >
> > 1. How many outputs were created? Are the files the same as before?
> > 2. What happened with the tags?
> >
> > > <solution-title></solution-title>
> > > 1. One output collection was created, with two files inside. The files themselves are the same as before.
> > > 2. The tags from our input datasets were also added to the results
> > >
> > {: .solution}
> {: .question}
>
{: .hands_on}

Collections allow you to easily run tools on multiple datasets at once. We have 2 datasets in our collection, but you can have as many as
you like, even hundreds or thousands.

Now that we have everything in Galaxy set up for analysis, and our data pre-processed to the right format, we can start to
answer our research question, ***"What is the age distribution of Olympic athletes?"***.




## Analyse: Calculate results


![The RDM lifecycle with the analyse stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-analyse.png %}){: style="width:50%"}


> <comment-title> Domain-specific analysis tools </comment-title>
>
> Because this is an intro tutorial, our "analysis" will be quite basic. But Galaxy offers thousand of tools covering
> a wide range of scientific domains. From life sciences, to ecology, climate, astronomy, digital humanities, and many more.
>
> Galaxy has a lot of computational power behind it, so whether you need a simple calculation or a complex algorithm requiring a supercomputer,
> Galaxy can handle it.
>
> If you are interested in a specific domain, have a look at the following resources:
> - [Galaxy Special Interest Groups (SIGs)](https://galaxyproject.org/community/sig/)
> - Galaxy Labs (aka subsites or subdomains)
>
>   {% snippet faqs/galaxy/galaxy_labs.md %}
>
> And the following GTN resources:
> - [GTN Tutorials by Topic](https://training.galaxyproject.org)
> - [GTN Learning Pathways](https://training.galaxyproject.org/learning-pathways)
>
{: .comment}


### Plan our approach

Recall that our research question in this tutorial is ***"What is the age distribution of Olympic athletes?"***

> <question-title> What to do? </question-title>
>
> 1. How would you approach answering our research question?
> 2. Can you find tools in Galaxy that might help you do this?
>
> > <solution-title></solution-title>
> > 1. There are several things we might like to compute in order to answer our question, perhaps
> >    - What is the average age of our Olympians?
> >    - What is the standard deviation?
> >    - What ages are the oldest and youngest Olympians?
> >    - What does the histogram look like for the age distribution?
> >    - Create a boxplot for the age distribution
> >
> > 2. If you already know the name of the tool you want to use, you can simply enter this in the search bar. But often you might not know the name of the tool, then just search for some related keywords
> >
> >    Try searching for terms like:
> >    - statistics, mean, average, minimum, maximum, standard deviation, summary, column, histogram, boxplot
> >
> >    The tool **Summary Statistics - for any numerical column** {% icon tool %} looks interesting!
> >
> >    As does the **Histogram - of a numeric column** {% icon tool %}
> >
> {: .solution}
{: .question}

Let's do some analysis based on our plan.

### Get summary statistics for our age column

> <hands-on-title> Summary Statistics </hands-on-title>
>
> 1. {% tool [**Summary Statistics** for any numerical column ](Summary_Statistics1) %} with the following parameters:
>    - {% icon param-collection %} *"Summary statistics on"*: output from **Compute** {% icon tool %}
>      - remember to switch to collection input {% icon param-collection %}
>    - *"Column or expression"*: `c16`
>
> 2. **View** {% icon galaxy-eye %} the results
>    - it should look something like this:
>
>      ```
>      #sum 	mean 	stdev 	0% 	25% 50% 75% 100%
>      114999 	26.1243 5.01207 15 	23 	25 	29 	51
>      ```
>
> > <question-title> </question-title>
> >
> > 1. Which of these two Olympics game had the youngest contestants on average?
> > 2. What was the age of the oldest contestant in each Olympics?
> >
> > > <solution-title></solution-title>
> > > 1. The 2008 Summer Olympics. Compare the *mean* of each output. For the 2010 Winter games this was 26.1243, and for the 2008 Summer games it was 25.7341
> > > 2. The value of the 100th percentile indicates the highest value encountered. For 2008 this was 67 year, for 2010 it was 51.
> > >
> > {: .solution}
> {: .question}
>
{: .hands_on}

This is great, we know know some summary statistics for the age distribution of the Olympics. Let's see if we can also create a visual representation.


### Create a histogram

A picture is worth a 1000 words, so let's see if we can plot the age distribution as well.
We already created a boxplot before, let's try a histogram this time. We will also use a tool rather than a Galaxy visualisation,
so that we get an output file with the plot in our history.

The tool we are goint to use for this is **Histogram with ggplot2** {% icon tool %}. This tool will plot every compatible column in the
input dataset. Since we are only interested in the age column, we will extract this colum first, and then plot it.

In order to make it easier to compare

> <hands-on-title> Create a Histogram Plot </hands-on-title>
>
> 1. {% tool [Remove columns - by heading](toolshed.g2.bx.psu.edu/repos/iuc/column_remove_by_header/column_remove_by_header/1.0) %} with the following parameters:
>    - {% icon param-collection %} *"Tabular file"*: output from **Compute** {% icon tool %} (collection)
>    - *"Header name"*: `age`
>    - {% icon param-toggle %} *"Keep named columns"*: `Yes`
>
> 2. **View** {% icon galaxy-eye %} the outputs
>    - make sure the output is as expected (a file containing only the age column)
>
> 3. {% tool [Histogram with ggplot2](toolshed.g2.bx.psu.edu/repos/devteam/histogram/histogram_rpy/1.0.5) %} with the following parameters:
>    - *"Plot title"*: enter a good title, e.g. `Age distribution of athletes`
>    - *"Label for x axis"*: `Age`
>    - *"Label for y axis"*: `Count`
>    - *"Bin width for plotting"*: `1`
>
> 4. **View** {% icon galaxy-eye %} the resulting plots side by side using the **Window Manager** {% icon galaxy-scratchbook %}
>
>    {% snippet faqs/galaxy/features_scratchbook.md %}
>
{: .hands_on}


The Window Manager is an easy way to quickly compare two datasets.

![the histograms side by side]({% link topics/introduction/images/galaxy-intro-rdm/histogram-scratchbook.png %})

But this doesn't scale to a large number of datasets. So as the final step of our analysis, let's create a montage of our histograms.


> <hands-on-title> Create a montage of plots </hands-on-title>
>
> 1. {% tool [**Image Montage** - with ImageMagick](toolshed.g2.bx.psu.edu/repos/bgruening/imagemagick_image_montage/imagemagick_image_montage/7.1.2-2+galaxy1) %} with the following parameters
>    - {% icon param-collection %} *"Image"*: Output from **Histogram** {% icon tool %}
>    - *"# of images wide"*: `2`
>    - *"Add a Title to the image"*: `Age distribution of athletes in Olympic Games`
>    - *"Add the name of the files as image labels."*: `Yes`
>    - *"Point size of the labels and/or title"*: `60`
>
{: .hands_on}


![montage of histograms]({% link topics/introduction/images/galaxy-intro-rdm/montage.png %})


Awesome, we now have a pretty good answer to our question. We have some basic summary statistics for each Olympics, and a montage of histogram plots.

Next, we would like to repeat all this for **all** Olympic games.

Note that we chose our montage to be 2 images wide because we only had 2, but when we run it on more datasets at once we might want to change this. We will do this later.


### Extract workflow from our history

To make it easy to repeat this entire analysis without a lot of clicking, we will create a **workflow** based on our analysis history.

> <hands-on-title>Extract workflow from history </hands-on-title>
>
> 1. **Clean up** your history: remove any failed (red) jobs from your history by clicking the {% icon galaxy-delete %} button.
>
>    This will make the creation of the workflow easier.
>
> 2. Click {% icon galaxy-history-options %} (**History options**) at the top of your history panel and select **Extract workflow**.
>
>    !['Extract Workflow' entry in the history options menu]({% link topics/introduction/images/galaxy-intro-rdm/extract-wf.png %})
>
>    The central panel will show the content of the history in reverse order (oldest on top), and you will be able to choose which steps to include in the workflow.
>
>    ![Selection of steps for Extract Workflow from history.]({% link topics/introduction/images/galaxy-intro-rdm/workflow-extract.png %})
>
> 3. Replace the **Workflow name** to something more descriptive, for example: `Olympic Age distribution`.
>    - Here you can also uncheck any steps you forgot to delete when you cleaned up your history
>
> 4. Click the **Create Workflow** button near the top.
>
>    You will get a message that the workflow was created.
>
{: .hands_on}

Next, we will run this workflow on *all* Olympic games.


### Run workflow on all Olympics


> <hands-on-title>New history</hands-on-title>
>
> 1. **Create** a new history
>
>    {% snippet faqs/galaxy/histories_create_new.md %}
>
> 2. **Rename** {% icon galaxy-pencil %} your history, *e.g.* "All Olympics"
>
>    {% snippet faqs/galaxy/histories_rename.md %}
>
> 3. **Upload** the zip file with all olympic datasets from Zenodo
>
>    ```
>    https://zenodo.org/records/18803585/files/olympics-all.zip
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
> 4. {% tool [**Unzip** - a file](toolshed.g2.bx.psu.edu/repos/imgteam/unzip/unzip/6.0+galaxy3) %} with the following parameters:
>    - {% icon param-file %} *"Input file"*: `olympics-all.zip`
>
> > <question-title> </question-title>
> >
> > 1. How many Olympic games do we have data for?
> >
> > > <solution-title></solution-title>
> > > 1. Our collection contains 51 datasets, one per Olympics
> > {: .solution}
> {: .question}
>
{: .hands_on}


Next, let's view the workflow in the workflow editor

> <hands-on-title>View the workflow in the editor</hands-on-title>
>
> 1. Click the {% icon galaxy-workflows-activity %} **Workflows** activity in the activity bar.
>
>    Here you have a list of all your workflows _(the **My Workflows** tab is active by default)_.
>
>    ![workflows table]({% link topics/introduction/images/galaxy-intro-rdm/workflows.png %})
>
>    You can see all available actions for the workflow on the workflow card, e.g. copy, download, share, edit and run
>
> 2. Click the {% icon galaxy-wf-edit %} (*Edit*) button on the bottom right of the workflow card.
>
> 3. Play around with the editor
>    - You can move boxes around
>    - You can add tools and make connections between tools
>    - You can click on a tool and change parameters
>
>    ![workflows editor]({% link topics/introduction/images/galaxy-intro-rdm/workflow-editor.png %})
>
>    **We will only make 1 change:** since we will have many more histograms, lets make the montage image 4 plots wide
>
> 4. Click on the Montage tool
>    - a panel with the tool's configuration will open on the right
>    - change the value for **# of images wide** to 4
>
>     ![workflows table]({% link topics/introduction/images/galaxy-intro-rdm/workflow-editor-parameter.png %})
>
> 5. **Save** {% icon dataset-save %} the workflow via the {% icon dataset-save %} icon at the top right
>
> 6. Exit the editor by clicking on the Home button (Galaxy logo) at the left of the top menu bar
>
{: .hands_on}


Now it's time to run our workflow

> <hands-on-title>Run the workflow</hands-on-title>
>
> 1. Click the {% icon galaxy-workflows-activity %} **Workflows** activity in the activity bar.
>
> 2. Click the {% icon workflow-run %} (*Run workflow*) button on the bottom right of the workflow card.
>
>    The central panel will change to allow you to configure and launch the workflow.
>
>    ![workflow run menu]({% link topics/introduction/images/galaxy-intro-rdm/workflow-run.png %})
>
> 3. Make sure the input of the workflow is our collection with 51 datasets.
>
> 4. Click **Run Workflow** {% icon workflow-run %} at top right
>    - You will now see the *workflow invocation* screen
>    - Here you can see the progress of the workflow
>    - You can find all your previous workflow runs (invocations) in the Activity bar under Workflow Invocations {% icon galaxy-panelview %}
>
>    ![workflow invocation]({% link topics/introduction/images/galaxy-intro-rdm/workflow-invocation.png %})
>
{: .hands_on}

Our analysis will now be run on all 51 olympics files. This may take a bit of time (~5-10 minutes or more depending on how busy Galaxy is at the moment), so now is a good time to **grab a coffee**. You can also already proceed to the next section while you wait.

Once your workflow is finished, you should get a final montage image with 51 histograms

![final montage image]({% link topics/introduction/images/galaxy-intro-rdm/montage-all.png %} "Montage of histograms for all 51 Olympic games in our dataset")


TODO: question box?



## Preserve: Export data, history, and workflow

![The RDM lifecycle with the preserve stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-preserve.png %}){: style="width:50%"}

```
- history download
- workflow download
- RO-crate export?
- Daniela's suggestion: export to repository using https://usegalaxy.eu/root?tool_id=export_remote
- other?
```


## Share: Share or publish data and workflow

![The RDM lifecycle with the share stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-share.png %}){: style="width:50%"}

```
- share workflow
- share history
- show published histories
- discuss workflowhub, IWC

- examples of publications citing workflows/histories/tutorials
```


## Reuse: Find and run workflows shared by others

![The RDM lifecycle with the reuse stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-reuse.png %}){: style="width:50%"}

```
- download a workflow from WorkflowHub and run it
	- CYOA choice between Voronoi tuto workflow and digital humanities workflow?
- import the data for it (can we show a third way to import data?)
- run and view results

- show account options while we wait (e.g. permanently deleting, quota and how to request more storage)
```

The ultimate goal of preserving and sharing your research data and analyses, is to enable others to repeat your analysis and reuse your work.
To illustrate this, we will now show you how you can reuse a shared workflow from the WorkflowHub.


### The workflow

We will now walk you through reusing the *Voronoi segmentation* workflow
you may recognize from the [video at the start of this tutorial](#watch)

This [workflow has been made available via WorkflowHub](https://workflowhub.eu/workflows/1522). We will import this workflow into Galaxy, upload a dataset, and run the workflow in Galaxy.

> <details-title> Voronoi Segmentation </details-title>
>
> **From Wikipedia:** In mathematics, a Voronoi diagram is a partition of a plane into regions close to each of a given set of objects. It can be classified also as a tessellation. In the simplest case, these objects are just finitely many points in the plane (called seeds, sites, or generators). For each seed there is a corresponding region, called a Voronoi cell, consisting of all points of the plane closer to that seed than to any other.
>
> ![example of voronoi diagram]({% link topics/introduction/images/galaxy-intro-rdm/voronoi-example.png %})
{: .details}

![screenshot of the voronoi workflow from workflowhub]({% link topics/introduction/images/galaxy-intro-rdm/voronoi-wf.png %})


For more information about this workflow and a full walkthrough of all its steps, see also the [full GTN tutorial]({% link topics/imaging/tutorials/voronoi-segmentation/tutorial.md %})


https://workflowhub.eu/workflows/1522
https://zenodo.org/records/15281843/files/images_and_seeds.zip

