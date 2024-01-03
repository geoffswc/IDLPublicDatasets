## IDLPublicDatasets

### Overview

This repository contains code, documentation, and data for a pilot project between the IDL and DSOS teams at the UCSF Library to generate transcriptions and annotations for video media stored in the industry documents library. These transcriptions and annotations are provided as a downloadable CSV file and as a BigQuery table.

### Process

The process for generating and formatting transcripts and annotations relies heavily on Google Vertex AI and the IDL website.

#### 1. Identify a dataset.

This pilot uses a set of deposition related videos from the Industry Documents Library Tobacco and Opioid collections. To view these documents in the IDL application, search you can search on "deposition": https://archive.org/details/industry-archives?query=deposition (144 records at the time of this pilot, more may have been added since then). The query results in 144 records and 559 related video files. 

#### 2. Read the metadata and file locations for the video media

To access the metadata and url for video files for this query, we'll use the search API from the internet archive. Code for this step is available in the IADownloads/Search_IA.ipynb notebook. This workbook will run the deposition query from step 1 and download metadata and file list files in XML format from the internet archives into a set of local files.

#### 3. Extract metadata and URL links into a tabular data format

To create a dataset with transcriptions, annotations, and metadata, we'll need to extract the metadata into a separate pandas dataframe. Next, to extract transcripts and annotations from these depositions using Google Vertex AI, we'll need a list of URIs for each video in the document list. These two steps are accomplished in the Generate_Metadata_URL_Table.ipynb workbook.

#### 4. Use a Cloud Function to automate transcription and annotation

Google Cloud provides a method to trigger a python script that runs transcription and annotation for files when they are uploaded to a storage bucket. This approach takes advantage of cloud based processing and avoids the need to write code to manage network or load balancing/processing. The python script is located in cloud_functions/main.py. 

#### 5. Upload the list of URIs 

Create a data transfer to google cloud storage by providing a list of public URIs to the video media for each record in the query. This will automate the process for generating transcripts and annotations through Vertex Video Intelligence. 

#### 5. Download the annotations and transcriptions

Results for transcriptions and annotations are directed to a Google Cloud Storage Bucket. Results are provided as JSON files and contain very granular detail about embedded text, images, gestures, people, logos, and transcripts. Because the resultset is only ~10mb, we will download these into the output-idl-json-files.archive.org directory and process them locally. However, this step could be done on the cloud using the Vertex AI workbench (this would be essential if the files were large, but we can process 10mb locally and avoid cloud computing charges for this step). 

#### 6. Extract the relevant annotations and transcriptions from the JSON files

The level of detail is very high for the JSON output from video intelligence. Although we should keep these files and and make them available to researchers, we do want a more condensed CSV format as a public dataset download link and/or as a set of columns in a BigQuery dataset. The Create-Pandas-Annotations workbook contains code to extract the relevant annotations and transcripts from the JSON files and create a CSV output.

#### 7. Prepare a CSV file for BigQuery

A few modifications are required to prep a csv file to work as a dataset in BigQuery (escaping certain characters and other minor cleanup). The Prep-BigQuery-Dataset contains code for this step.

#### 8. Upload the file to BigQuery as a dataset and run a Query.

This step takes place on Google Cloud and doesn't involve code that can be stored in this repo. It's all SQL and runs through the cloud console.

### Costs

To manage costs, we only processed the first 100 video files. 

FIRST 100 Deposition Files
* TOTAL TIME (hrs):  88.03
* TOTAL COST: $ 2366.34
* LABEL_DETECTION ($0.10/min): $ 528.2
* LOGO_RECOGNITION ($0.15/min): $ 792.3
* TEXT_DETECTION ($0.15/min): $ 792.3
* SPEECH_RECOGNITION ($0.048/min): $ 253.54

It's important to note that the transcription (speech_recognition) is only a little more than 10% of the total cloud computing costs.
Costs are overwhelmingly related to use of the video intelligence to generate transcripts and annotations. Storage and other cloud computing costs are very small (around $3-$4), so the costs of processing these files on the cloud rather than locally won't be a influencing factor in cost related decisions. 

Estimated Costs for all Deposition Files
* TOTAL TIME (hrs):  705.0
* TOTAL COST: $ 18950.36
* LABEL_DETECTION ($0.10/min): $ 4229.99
* LOGO_RECOGNITION ($0.15/min): $ 6344.99
* TEXT_DETECTION ($0.15/min): $ 6344.99
* SPEECH_RECOGNITION ($0.048/min): $ 2030.4

### Further Information

For a full review of this process including steps for creating and querying a BigQuery dataset, please see the video on the CLE page.
(video will be posted after presentation)


