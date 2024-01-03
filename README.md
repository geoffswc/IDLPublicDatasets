## IDLPublicDatasets

### Overview

This repository contains code, documentation, and data for a pilot project between the IDL and DSOS teams at the UCSF Library to generate transcriptions and annotations for video media stored in the industry documents library. These transcriptions and annotations are provided as a downloadable CSV file and as a BigQuery table.

### Process

The process for generating and formatting transcripts and annotations relies heavily on Google Vertex AI and the IDL website.

#### 1. Identify a dataset.

This pilot uses a set of deposition related videos from the Industry Documents Library Tobacco and Opioid collections. To view these documents in the IDL application, search you can search on "deposition": https://archive.org/details/industry-archives?query=deposition (144 records at the time of this pilot, more may have been added since then). 

#### 2. Read the metadata and file locations for the video media

To access the metadata and url for video files for this query, we'll use the search API from the internet archive. Code for this step is available in the IADownloads/Search_IA.ipynb notebook. This workbook will run the deposition query from step 1 and download metadata and file list files in XML format from the internet archives into a set of local files.

#### 3. Extract metadata and URL links into a tabular data format

To create a dataset with transcriptions, annotations, and metadata, we'll need to extract the metadata into a separate pandas dataframe. Next, to extract transcripts and annotations from these depositions using Google Vertex AI, we'll need a list of URIs for each video in the document list. These two steps are accomplished in the Generate_Metadata_URL_Table.ipynb workbook.

