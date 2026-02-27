---
layout: tutorial_hands_on
title: Introduction to Galaxy as an RDM platform
zenodo_link:
level: Introductory
questions:
- Which RDM features does Galaxy offer?
objectives:
- Familiarize yourself with the basics of Galaxy
- Learn how to import data
- Learn how to process and analyze data
- Learn how to share your work

time_estimation: 2H
key_points:
subtopic: core
priority: 3

contributions:
  authorship:
    - shiltemann
  funding:
    - uni-freiburg
    - nfdi4plants

---


This tutorial aims to familiarize you with the Galaxy user interface, with a special focus on highlighting Galaxy's many RDM (Research Data Management) features.

{% snippet faqs/galaxy/analysis_results_may_vary.md %}

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

TODO: reference https://rdmkit.elixir-europe.org/galaxy_assembly ?


In this tutorial, we will provide a hands-on introduction to the Galaxy platform across the different stages of the research data life cycle.


## Basics of Galaxy

### Create an account on a Galaxy instance/server
If you already have an account, skip to the next section!

In Galaxy, *server* and *instance* are often used interchangeably. These terms basically mean that different regions have different Galaxy servers/instances, with slightly different tool installations and appearances. If you don't have a specific server/instance in mind, we recommend registering at one of the main public servers/instances, detailed below.

{% snippet faqs/galaxy/account_create.md %}

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
* The Activity Bar on the left: _This is where you will navigate to the resources in Galaxy (Tools, Workflows, Histories etc.)_
* Currently active "Activity Panel" on the left: _By default, the {% icon tool %} **Tools** activity will be active and its panel will be expanded_
* Viewing panel in the middle: _The main area for context for your analysis_
* History of analysis and files on the right: _Shows your "current" history; i.e.: Where any new files for your analysis will be stored_

![Screenshot of the Galaxy interface with aforementioned structure](../../images/galaxy_interface.png)

The first time you use Galaxy, there will be no files in your history panel.

### The Galaxy History

Your "History" is in the panel at the right. It is a record of the actions you have taken. It tracks the provenance of all datasets imported to or created in Galaxy

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
> 3. Type in a new name, for example, "My Analysis"
> 4. Click **Save**
>
> > <comment-title>Renaming not an option?</comment-title>
> > If renaming does not work, it is possible you aren't logged in, so try logging in to Galaxy first. Anonymous users are only permitted to have one history, and they cannot rename it.
> {: .comment}
>
{: .hands_on}



## Collect: Data import

![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-collect.png %}){: style="width:50%"}

```
- import olympics summer data from Zenodo via URL
- import olympics winter data from Zenodo via repository browse
- add dataset tags


- discuss different data import options
    - import from popular data repositories (SRA/NCBI)
    - from BYOS
    - from shared data library
```



## Process: Data preparation and QC

![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-process.png %}){: style="width:50%"}

```
- compute age column for summer olympics
- show tool run provenance, mention this will be used to extract workflow at end of tutorial

- rerun tool on winter olympics
  - intentional error due to NA value orso
  - show bug report stdout/stderr
  - set error handling params on compute tool to deal with this (or remove lines?)

- optional section: show OpenRefine to perform same tasks

- history system: create new history, show switching, drag & drop
- scaling: create collection in new history, put both files in, run the compute step again on collection
```



## Analyse:


![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-analyse.png %}){: style="width:50%"}

```
- sort table by age column, ascending
- get top 5 youngest ahtletes
- show a visualisation, age histogram?

- discuss plethora of domain-specific tools, link to community pages and subdomains

- extract workflow from history, remove OpenRefine and Rstudio steps if done
- show workflow editor, edit input collection name
- import zip file of olympics data from other years, unzip to collection, run workflow on this

- optional section: show Rstudio Interactive tool, create a plot in R
  - export plot to galaxy
  - export R history
```


## Preserve

![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-preserve.png %}){: style="width:50%"}

```
- history download
- workflow download
- RO-crate export?
- other?
```


## Share

![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-share.png %}){: style="width:50%"}

```
- share workflow
- share history
- show published histories
- discuss workflowhub, IWC

- examples of publications citing workflows/histories/tutorials
```


## Reuse

![]({% link topics/introduction/images/galaxy-intro-rdm/rdm-reuse.png %}){: style="width:50%"}

```
- download a workflow from WorkflowHub and run it
	- CYOA choice between Voronoi tuto workflow and digital humanities workflow?
- import the data for it (can we show a third way to import data?)
- run and view results
```


Workflows shared with you by others can easily be rerun.  To illustrate this fact, we will now get a workflow from WorkflowHub, import it into Galaxy, and run it.

https://zenodo.org/records/15281843/files/images_and_seeds.zip

