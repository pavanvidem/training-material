---

layout: tutorial_hands_on
title: "Nb2workflow: Generating Tools From JuPyter notebooks"
key_points:
  - The Nb2workflow is an automated Galaxy tool generator for scientists and developers who routinely write jupyternotebooks.

objectives:
 - Learn why you might want to use the Nb2workflow 
 - Install and explore the simple examples provided.
 - Modify and re-generate them to see how the changes affect the tool
 - Generate new simple Galaxy tools using your own scripts

questions:
 - What options exist for new-to-Galaxy developers to convert functioning jupyter notebooks scripts into Galaxy tools?

time_estimation: 1H
subtopic: tooldev

requirements:
    topic_name: introduction
    tutorials:
      - galaxy-intro-short
      - galaxy-intro-101-everyone
    topic_name: dev
    tutorials:
      - tool-integration


contributors:
  - Andrei-EPFL

---

Nb2worfklow and this tutorial are for developers and researchers interested in Galaxy, who routinely develop their own analysis in jupyter notebooks.


> <agenda-title></agenda-title>
>
> 1. TOC
> {:toc}
>
{: .agenda}


# Annotating JuPyter notebooks


Once you built the Jupyter notebook one can check the [guide-development](https://odahub.io/docs/guide-development/) to properly adapt it for nb2workflow. Nevertheless check the hands-on below.


> <hands-on-title>Annotating Jupyter notebooks</hands-on-title>
> 
> # Save figure and create data product
>
> ```
> git clone https://github.com/esg-epfl-apc/nb2galaxy-example-repo.git
> cd nb2galaxy-example-repo
> ```
> Open the following jupyter notebook
> ```
> example_nb2workflow.ipynb
> ```
> Save the figure as ```Image.png``` file and import ```from oda_api.data_products import PictureProduct```.
> Read the file using  ```bin_image = PictureProduct.from_file('Image.png')```
> # Annotate
> Annotate the parameters in the second cell with ```#http://odahub.io/ontology#float``` or ```#http://odahub.io/ontology#integer```. Annotate the entire second cell with the tag *parameters*.
> ![text](../../images/nb2workflow-annotating-nb-inputs.png "Text")
> Annotate the ```bin_image``` parameter with ```#http://odahub.io/ontology#ODAPictureProduct```. Annotate the cell containing only this parameter with the tag *outputs*.
> ![text2](../../images/nb2workflow-annotating-nb-outputs.png "Text2")
> Check the following jupyter notebook as a finished example:
> ```
> example_nb2workflow_annotated.ipynb
> ```
{: .hands_on}

# Converting a notebook repository into a Galaxy tool.

A workflow developed following the [Development Guide](guide-development.md) can be automatically converted into a [Galaxy](https://github.com/galaxyproject/galaxy) tool.

To trigger the conversion, one must add `galaxy-tool` as a "Project topic" in the GitLab project settings. The bot monitoring the [GitLab group](https://gitlab.renkulab.io/astronomy/mmoda) then attempts to convert the repository into a Galaxy tool and creates a corresponding pull request in the [tools-astro repository](https://github.com/esg-epfl-apc/tools-astro). This process is handled by the `galaxy.py` module from the [nb2workflow](https://github.com/oda-hub/nb2workflow) package. If the automatic conversion process fails, one can debug it using `nb2galaxy` CLI directly. Run `nb2galaxy --help` to see the available command-line options.

## Resolution of the dependencies

Galaxy tools require dependencies to be available as Conda packages (not PyPI packages). Therefore, the conversion module attempts to reconcile the dependencies listed in both `requirements.txt` and `environment.yml` using Conda. First, each package from `requirements.txt` is searched by Conda. If the package with the same name exists in the configured conda channels (only `conda-forge` by default), it is included in the final list of packages for reconciliation. Otherwise, the package is ignored and a comment is added to the generated tool represented as an XML file.

In the end, all dependencies are resolved together to obtain fixed versions of the required packages that are written in the tool's XML file.

## Additional configuration

A Galaxy ToolShed definition file `.shed.yml` can be manually included. If this file is not present, one is auto-generated with default options.
Here is an example of a minimal, automatically generated `.shed.yml` file:

```yaml
categories:
- Astronomy
description: Tool Name
homepage_url: null
long_description: Tool Name
name: tool_name_astro_tool
owner: astroteam
remote_repository_url: https://github.com/esg-epfl-apc/tools-astro/tree/main/tools
type: unrestricted
```
It is recommended to provide the `description` and `long_description`, as they define how the tool appears in the Galaxy ToolShed. However, these values - along with other Galaxy-specific options - can be configured via the YAML frontmatter block in `galaxy_help.md`, as well (see below).

Additional configuration options part of the YAML frontmatter block are:
- `description: <string>` - a short description of the tool which can be used in `.shed.yml` if this file is not explicitly provided (default value: the tool name);
- `long_description: <string>` - similar to `description: <string>`, but provides a more detailed explanation;
- `additional_files: <list|string>` - specifies extra files (e.g. helper modules and data files) from the GitLab repository required by the tool, since by default, `nb2galaxy` converts only the notebooks to scripts and adds them to the tool directory. The paths can be a single string or a list of strings and should follow the [glob](https://docs.python.org/3/library/glob.html#glob.glob) syntax (with `recursive=True`). **Note**: In Galaxy, the tool directory and the workdir (i.e. from where the tool is executed) are different. To address this, the conversion module sets two environment variables: `GALAXY_TOOL_DIR` and `BASEDIR`, both pointing to the tool root directory.

In addition to these configuration options, the `galaxy_help.md` file - located in the root directory of the repository - has to include the tool documentation (delimited by `---` from the YAML block) that is automatically extracted and added into the tool XML file.

Finally, a `citations.bib` file that contains tool references must be created. If an entry contains a DOI, only the DOI is used in the Galaxy tool XML file (this is the preferred method). Otherwise, the full BibTeX citation is included.

## Tool linting, testing and preview

The [tools-astro repository](https://github.com/esg-epfl-apc/tools-astro) is set up with CI/CD pipelines that automatically lint the tool definition files and run tests to ensure the tool, with the default parameters, executes correctly and without exceptions. One can inspect the pipeline results and then fix issues or improve the original GitLab repository accordingly.

Additionally, when the bot creates a GitHub pull request, it adds a `test-live` label. This triggers a CI/CD workflow that publishes a preview of the tool to our [staging server](https://galaxy.odahub.fr), under the **Astro tools (staging)** section. This allows the developer of the tool to preview and manually test it on the live Galaxy instance.
