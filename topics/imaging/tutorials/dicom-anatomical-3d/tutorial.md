---
layout: tutorial_hands_on

title: "Segmentation of Anatomical Structures in Medical 3-D Images"
zenodo_link: https://zenodo.org/records/18225140
level: Introductory
subtopic: analyses
#questions:
#  - "How do I use Galaxy with imaging data?"
#  - "How do I convert images using Galaxy?"
#  - "How do I display images in Galaxy?"
#  - "How do I filter images in Galaxy?"
#  - "How do I segment simple images in Galaxy?"
#objectives:
#  - "How to handle images in Galaxy."
#  - "How to perform basic image processing in Galaxy."
#key_points:
#- The **Image Info** tool can provide valuable metadata information of an image.
#- TIFF files cannot viewed directly in most web browser, so a visualization plugin must be used.
#- For visualization, images with a bit-depth more than 8-bit have to be histogram equalized.
time_estimation: "1H"
#follow_up_training:
#  -
#    type: "internal"
#    topic_name: imaging
#    tutorials:
#      - hela-screen-analysis
contributions:
  authorship:
    - kostrykin
  funding:
    - deNBI
tags:
  - Image segmentation
  - Conversion
  - Overlay

---

Image data in medical imaging is often stored and exchanged in the DICOM file format. A DICOM dataset is a file that contains rich metadata (patient, study info) and the actual medical image pixel (for 2-D images) or voxel data (for 3-D images). The image data can be single-channel or multi-channel, and it can also be organized in multiple frames (e.g., spatial tiles of a mosaic, z-slices of a 3-D image, or time steps).

Even though, technically, DICOM datasets can contain heterogeneous frames for different axes (e.g., z-slices and temporal frames), in practice, the frames of a DICOM dataset are usually designated for only one specific purpose and axis (e.g., z-slices or temporal frames). For such cases, DICOM series are more widely adopted: A list of DICOM datasets can form a DICOM series to store multi-dimensional data (e.g., a list of 3-D images for consecutive time steps, or just the z-slices of a 3-D image, where each slice is a single DICOM dataset).

In this tutorial, we will show how DICOM series and DICOM datasets can be converted to TIFF, which is a general-purpose image file format that is well-supported by a majority of tools in Galaxy. We will use that to showcase segmentation and visualization of anatomical structures in 3-D image data from computer tomography (CT). The Galaxy Image Analysis tools and techniques utilized in this tutorial can also be adapted to other imaging modalities (e.g., 3-D cell imaging).

> <agenda-title></agenda-title>
>
> In this tutorial, we will deal with:
>
> 1. TOC
> {:toc}
>
{: .agenda}

# Getting the Image Data

In this tutorial, we will be using a publicly available CT dataset of a torso, scanned between the thorax chest and the pelvis.


> <hands-on-title>Data upload</hands-on-title>
>
> 1. If you are logged in, create a new history for this tutorial
>
>    {% snippet faqs/galaxy/histories_create_new.md %}
>
> 2. Import the following dataset from [Zenodo]({{ page.zenodo_link }}) or from the data library (ask your instructor).
>
>    ```
>    {{ page.zenodo_link }}/files/PCIR_98890234_20010101_7.zip
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
>    {% snippet faqs/galaxy/datasets_import_from_data_library.md %}
>
> 3. {% tool [Unzip](toolshed.g2.bx.psu.edu/repos/imgteam/unzip/unzip/6.0+galaxy2) %} with the following parameters:
>    - {% icon param-file %} *"Input file"*: `PCIR_98890234_20010101_7.zip`
>    - *"What to extract"*: `All files`
>
> 4. Rename {% icon galaxy-pencil %} the dataset collection to `DICOM series`
>
>    {% snippet faqs/galaxy/datasets_rename.md %}
>
> 4. Change the datatype of {% icon galaxy-pencil %} `DICOM series` to `Auto-detect`
>    
>    <!-- For some reason, there is no "Auto-detect" option when using the non-bulk version of the "Change data type" function of the collection -->
>    {% snippet faqs/galaxy/bulk_change_datatype.md datasets_description="the `DICOM series` dataset collection"  n="1" datatype="Auto-detect" %}
{: .hands_on}

# Pre-processing

In this DICOM series, each DICOM dataset contains the z-slice of the 3-D image. The z-axis is the longitudinal axis (i.e. from the bottom to the top of the torso). The metadata of each DICOM dataset in the series contains the z-position of the slice, so the order of the individual DICOM datasets in the collection does not matter (note that for other images, in general, this information might be missing in the metadata, in which case the order of the datasets might be crucial).

Our first step will be to convert the DICOM series into a single TIFF image, that represents the whole 3-D dataset.

> <hands-on-title>Convert DICOM series to TIFF image</hands-on-title>
>
> 1. {% tool [Convert DICOM to TIFF](toolshed.g2.bx.psu.edu/repos/imgteam/highdicom_dicom2tiff/highdicom_dicom2tiff/0.27.0+galaxy0) %} with the following parameters to convert the DICOM series (list of DICOM datasets) into a list of TIFF images:
>    - {% icon param-files %} *"DICOM dataset"*: `DICOM series` collection
> 2. {% tool [Concatenate images or channels](toolshed.g2.bx.psu.edu/repos/imgteam/concat_channels/ip_concat_channels/0.5) %} with the following parameters to concatenate the stack of TIFF images along the z-axis into a single 3-D image:
>    - {% icon param-files %} *"Images to concatenate"*: Output of {% tool [Convert DICOM to TIFF](toolshed.g2.bx.psu.edu/repos/imgteam/highdicom_dicom2tiff/highdicom_dicom2tiff/0.27.0+galaxy0) %}
>    - *"Concatenation axis"*: `Z-axis (concatenate images as slices of a 3-D image or image sequence)`
>    - *"Scaling of values"*: `Preserve range of values`
>    - *"Sort images before concatenating"*: `Sort images by their position along the Z-axis`
{: .hands_on}

Some of the tools that we will be using in our analysis require that the voxel size of the image is isotropic. To ensure that this is the case, we will re-sample the image data to an isotropic voxel size:

> <hands-on-title>Re-sample image data for isotropic pixel/voxel sizes</hands-on-title>
>
> 1. {% tool [Scale image](toolshed.g2.bx.psu.edu/repos/imgteam/scale_image/ip_scale_image/0.25.2+galaxy0) %} with the following parameters to re-sample the image data to an isotropic voxel size:
>    - {% icon param-file %} *"Input image"*: Output of {% tool [Concatenate images or channels](toolshed.g2.bx.psu.edu/repos/imgteam/concat_channels/ip_concat_channels/0.5) %}
>    - *"How to scale?"*: `Scale to spatially isotropic pixels/voxels`
>    - *"Method"*: `Down-sample (might lose information)`
{: .hands_on}

If the image data already would have been isotropic, this tool would yield the original data.

CT image data, such as the dataset that we are using in this tutorial, typically have intensity values that correspond to the Hounsfield scale. Using Hounsfield Units (HU) is advantageous, because it allows to directly identify specific materials or tissues solely based on the image intensities. Typical HU values are –1000 HU for air, –700 to –200 HU for skin tissue, –200 to –50 HU for fat tissue, 0 HU for water, –20 to 140 HU for muscle tissue, and 220 to 3071 HU for bone tissue (e.g., {% cite chougule2018clinical %}).

However, the image data may also contain values outside of the range of –1000 to 3071 HU that correspond to, for example, parts of the CT imaging setup, or imaging artifacts. To effectively “erase” those parts from the image data, we will clip the image intensities to the meaningful range of –1000 to 3071 HU as a final step of pre-processing:

> <hands-on-title>Clipping the image image intensities</hands-on-title>
>
> 1. {% tool [Clip image intensities](toolshed.g2.bx.psu.edu/repos/imgteam/clip_image/clip_image/0.7.3+galaxy0) %} with the following parameters:
>    - {% icon param-file %} *"Input image"*: Output of {% tool [Scale image](toolshed.g2.bx.psu.edu/repos/imgteam/scale_image/ip_scale_image/0.25.2+galaxy0) %}
>    - *"Lower bound"*: `-1000`
>    - *"Upper bound"*: `3071`
{: .hands_on}

# Exploring the Image Data

xxx

# Skeletal Segmentation

xxx

# Vessel Segmentation

xxx

The obtained visualization shows the segmentation of the aortic bifurcation.

Vessel segmentation is generally challenging and still an active field of research. Segmentation of whole vascular trees may require more sophisticated methods (e.g., {% cite Glsn2008 %}, {% cite Biesdorf2015 %}).
