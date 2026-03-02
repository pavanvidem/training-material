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

To keep this tutorial accessible for people with different backgrounds, we perform a toy analysis on a tabular dataset, namely a table of all athletes competing in the Olympics. The question we ask ourselves is "What is the age distribution of Olympic ahtletes?".


> <agenda-title></agenda-title>
>
> In this tutorial, we will:
>
> 1. TOC
> {:toc}
>
{: .agenda}


## Overview

Galaxy can be used at different stages of the data life cycle, covering the steps from data collection to data reuse.


![RDM life cycle]({% link topics/introduction/images/galaxy-intro-rdm/rdm-overview.png %}){: style="width:50%"}

TODO: reference https://rdmkit.elixir-europe.org/galaxy_assembly , add or link to life cycle description


In this tutorial, we will provide a hands-on introduction to the Galaxy platform across the different stages of the research data life cycle.


## The Galaxy Web Interface

Before we go into the stages of the RDM life cycle, let's start with the basics and log into Galaxy and explore the graphical user interface.

### Create an account on a Galaxy instance/server
If you already have an account, skip to the next section!

In Galaxy, *server* and *instance* are often used interchangeably. These terms basically mean that different regions have different Galaxy servers/instances, with slightly different tool installations and appearances. If you don't have a specific server/instance in mind, we recommend registering at one of the main public servers/instances, detailed below.

{% snippet faqs/galaxy/account_create.md %}

TODO: highlight single sign-on options

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

```
- import olympics winter data from Zenodo via URL
  - show dataset attributes, rename file
- import olympics summer data from Zenodo via repository browse
- add dataset tags


- discuss different data import options
    - import from popular data repositories (SRA/NCBI)
    - from BYOS
    - from shared data library
```

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
>   - many more (See "Get Data" section of the Tool panel in Galaxy)
> - **Bring-your-own-data** (e.g. Dropbox, Gdrive, OneData, eLabFTW)
>
>   {% snippet faqs/galaxy/manage_your_repositories.md %}
>
> - Connections to your **LIMS** system
>
>   {% snippet faqs/galaxy/importing-data-from-sierra-lims.md %}
>
> TODO: link to pages that give more info on these?
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

![galaxy center panel view showing a single dataset olympics-2010-winter.tsv. ]({% link topics/introduction/images/galaxy-intro-rdm/file-preview.png %} "Preview of the dataset in Galaxy. Each row corresponds to an ahtlete, and each column provides further information about this athlete including birthyear, weight, medals.")
{: .hands_on}


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
>
>    ![dataset details]({% link topics/introduction/images/galaxy-intro-rdm/dataset-details.png %})
>
>    - Scrolling down you will also see details of the upload job that performed the import. We will look more closely at this later.
>
> 3. **Rename** the file to include the city of the Olympic. You can do this by **editing the dataset attributes**
>    - This can be done by clicking on the **Edit** tab at the top of your screen, or the pencil icon {% icon galaxy-pencil %} on the expanded dataset.
>    - For example, rename it to `2010 Winter Olympics Vancouver`
>
>    {% snippet faqs/galaxy/datasets_rename.md %}
>
{: .hands_on}

- TODO: expand history item, look at infor there
- TODO: go to dataset details, highlight size, hash and other metadata
- TODO: change file name to include city (Oslo)
- TODO: add propagating tags "winter" and "2010" (or wait until we create the new hisory, after people can observe that it is hard to distinguish where each new dataste came from)



## Process: Data preparation and QC

![The RDM lifecycle with the process stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-process.png %}){: style="width:50%"}

```
- compute age column for winter olympics
- show tool run provenance, mention this will be used to extract workflow at end of tutorial

- rerun tool on summer olympics
  - intentional error due to NA value orso
  - show bug report stdout/stderr
  - set error handling params on compute tool to deal with this (or remove lines?)

- link to [data manipulation olympics tutorial](https://gxy.io/GTN:T00184) for more data prep tools

- optional section: show OpenRefine to perform same tasks

- history system: create new history, show switching, drag & drop
- scaling: create collection in new history, put both files in, run the compute step again on collection
```

### Use a tool

Let's calculate the age from the birthyear of the athletes and year of the Olympic games.

> <hands-on-title>Use a tool</hands-on-title>
>
{: .hands_on}

This tool will run and a new output dataset will appear at the top of your history panel.

{% snippet faqs/galaxy/tutorial_mode.md %}


### Tool provenance



## Analyse: Calculate results


![The RDM lifecycle with the analyse stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-analyse.png %}){: style="width:50%"}

```
- sort table by age column, ascending
- get top 5 youngest ahtletes
- show a visualisation, age histogram?

- discuss plethora of domain-specific tools, link to community pages and subdomains

- extract workflow from history, remove OpenRefine and Rstudio steps if done
- show workflow editor, edit input collection name
- import zip file of olympics data from other years, unzip to collection, add tags to collection elements (collection operations tool, based on file), run workflow on this

- optional section: show Rstudio Interactive tool, create a plot in R
  - export plot to galaxy
  - export R history
```


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


## Reuse: Find and run workflow shared by others

![The RDM lifecycle with the reuse stage highlighted]({% link topics/introduction/images/galaxy-intro-rdm/rdm-reuse.png %}){: style="width:50%"}

```
- download a workflow from WorkflowHub and run it
	- CYOA choice between Voronoi tuto workflow and digital humanities workflow?
- import the data for it (can we show a third way to import data?)
- run and view results

- show account options while we wait (e.g. permanently deleting, quota and how to request more storage)
```


Workflows shared with you by others can easily be rerun.  To illustrate this fact, we will now get a workflow from WorkflowHub, import it into Galaxy, and run it.

https://zenodo.org/records/15281843/files/images_and_seeds.zip

