# Cloud-Computing-Course-Project-Portfolio-2024

## Project 1: Data Wrangling 

### Title
Data Wrangling utilizing AWS-Based Services for Vancouver Street Trees dataset.

### Objective 
In this project I assume the role of a member of the data team woking for the City of Vancouver. The aim was to design and implement Data Analytical Platform (DAP) hosted on AWS for the street-trees-dataset.
The primary objective of the wrangling is to perform data ingestion, profiling, cleaning, and transformation using AWS services to ensure that the dataset is clean, structured, and ready for further descriptive and exploratory analysis. The wrangling process demonstrats practical usage of AWS tools such as S3, Glue DataBrew, and Glue pipelines to prepare the dataset for effective analytics.

### Background
The City of Vancouver maintains a list (excluding private owned trees and park trees) containing information on public trees planted along boulevards across Vancouver. The dataset includes inforamtion regarding the tree genus, species, dimensions, planting history, location, and geographical coordinates. The dataset is updated daily on weekdays but it may be several years between updates for some attributes.
Even with high update frequency, the dataset contains missing values, inconsistencies, incomplete records, and duplication issues which might make analysis challenging. This project prepares this data for meaningful analysis by integrating it into an AWS-based services. The insight generated from future analysis of this data can facilitate informed decision making regarding urban forestry, improving tree maintenance strategies and resource allocation in alignment with the city’s sustainability goals.

### Dataset
The dataset provides information details on over 180,000 public trees. For this analysis, (given the resouces constraints of utilizing the AWS services as a student) a subset of 526 records was selected, focusing just on trees with genus-Malus and species-XX. Key features of the dataset include:
- TREE_ID: Unique numerical identifier for each tree.
- CIVIC_NUMBER: Street address of the tree's location.
- STD_STREET: Name of the street where the tree is located.
- COMMON_NAME: Common name (e.g., Scotch Pine).
- ASSIGNED: Indicates if the address is made up (Y/N).
- ROOT_BARRIER: Whether a root barrier is installed (Y/N).
- PLANT_AREA: Location type (e.g., behind sidewalk, tree grate).
- ON_STREET_BLOCK: Street block where the tree is located.
- ON_STREET: The name of the street the tree is physically on.
- NEIGHBOURHOOD_NAME: Local area where the tree is planted.
- STREET_SIDE_NAME: Side of the street (Even, Odd, Median).
- HEIGHT_RANGE: Height range of the tree in feet.
- Height_Range_id: An ID representing the tree’s height range, coresspoinding to a specific height interval. (e.g.,0,1)
- DIAMETER: Tree trunk diameter at breast height (in inches).
- CURB: Whether the tree is on a curb (Y/N).
- DATE_PLANTED: Date the tree was planted.
- geo_point_2d: Geospatial coordinates in [latitude, longitude] format.

  Screenshot for Reference: ![Screenshot (464)](https://github.com/user-attachments/assets/745ef262-38e7-4fe1-a45c-63395e878940)

### Tools and Technologies 
AWS S3 for string the raw data input and the result outputs of the other AWS services.
AWS Glue Databrew for profiling and creating cleaning recipe for the data.
AWS Glue, using the visual ETL feature to create a pipeline for data aggregation without any coding. 
AWS Athena can be used for further data transformation using SQL querying. 

### Methodology 
#### 1] Data Ingestion (AMAZON S3 Service)
- Collected raw data from the City of Vancouver’s open data platform in Excel format.
- Screenshot showing the city of Vancouver website for reference.
- ![s1](https://github.com/user-attachments/assets/510ec0b0-fa1f-4b2d-8b58-649ce1540093)
  
- Uploaded the raw dataset to AWS S3 in the bucket cov-street-trees-project1.
- Bucket versioning was enabled to prevent against loss of data.
- Organized subfolders based on genus and species for structured storage to mimic a robust ingestion environment, where new data can be added seamlessly.
- Within the bucket, I created folders to organize the data based on 'genus_name=malus' and 'species_name=xx'. The dataset was uploaded in XLSX format. 
- Used S3 standard storage class for frequent data access, structured organization and long-term storage. Since the dataset is updated daily, and more species and genus data can be added in the future.
-  Screenshot showing the S3 bucket structure and uploaded raw dataset.
-  ![s2](https://github.com/user-attachments/assets/a79ac4ae-4295-4e93-9c26-40a4d080a128)



   
#### 2] Data Profiling (AWS Glue Databrew) 
- After sucessful ingestion, the dataset was connected to AWS Glue DataBrew for running a profiling job.
- The street-trees.xlsx file from the raw data bucket was connected under the name cov-rawdata-street-tree-project1.
- Screenshot of the connected dataset for reference.
- ![s4](https://github.com/user-attachments/assets/f25a2394-637d-475b-a451-4dd2af6c09d0)

- A new bucket 'cov-transformed-street-tree-project1' was created to store the results of the job runs.
- A profiling job was run to identify data quality issues.
- Insights:
- - 19 string columns, 2 numeric columns, and 1 geospatial column.
  -  24 % Missing data in 'Cultivar_Name' feature and 25% in 'Date_Planted'.
  -  Job confirmed that 98% of the cells contained valid data 
  -  There were no duplicate records in the dataset.
  -  Outliers were identified in the 'Height_Range_ID' and 'Diameter' columns.
  -  Central tendency values, distribution metrics and correlation values were visualized for further descriptive analysis.
  -  Screenshots of the profiling results for refrence.
  -  ![s5](https://github.com/user-attachments/assets/ace6526a-0fc3-43e7-845f-9daf7fd27533)
  -  ![s7](https://github.com/user-attachments/assets/fa5f566f-6527-4e8b-a96e-da66ae87fc60)
  -  ![s6](https://github.com/user-attachments/assets/0106671b-35ca-4f82-8740-a0f419a05845)

- The profiling results were saved in the 'data-profiling' folder of the 'cov-transformed-street-tree-project1' bucket.
- Screenshot of the saved output for reference.
- ![s8](https://github.com/user-attachments/assets/e897c9a6-ab8f-4e5f-ab24-dfed05da37cb)



#### 3] Data Cleaning (AWS Glue DataBrew)
- Utilizing the insights from the profiling, a cleaning project was created.
-  A cleaning recipe 'cov-cleaning-street-data-project1-recipe' was configured.
-  The following transformations were applied:
- - Replaced the empty cells in 'Cultivar_Name' with "Not Available". 
  - Replaced missing dates in Date Planted with a placeholder value "0000-00-00"
  - Handled outliers in 'Height_Range_ID' and 'Diameter' columns by removing them.
  - Ensured no duplicate values present in the 'Tree_ID' column.
  - Published the cleaning recipe to automate these steps for future datasets that have the same schema.
  - Screenshot of the cleaning job and the attached recipe.
  - ![s10](https://github.com/user-attachments/assets/4e4773b2-6856-4281-8c97-83cf9236fcc8)
  - ![s9](https://github.com/user-attachments/assets/c43fef60-1045-4c35-9486-212683475f04)

- Cleaned data was stored in the 'data-cleaning' folder of the transformed bucket, as two outputs.
- - A CSV file, in 'user' folder, for easy access for individual users viewing the data.
  - A SNAPPY compressed PARQUET file, in 'system' folder, for being read by the other AWS services.
  - Screenshot of cleaning job runs and folders for reference.
  - ![s12](https://github.com/user-attachments/assets/1a16cd23-f5b4-44e1-8376-5082bf92b7b6)
  - ![s11](https://github.com/user-attachments/assets/35f2818a-d881-455e-8af1-9ef0bc3c1029)

  #### Data Transformation (AWS Glue)
    - The cleaned PARQUET file was used as input for creating an AWS Glue ETL pipeline.
    - Pipeline Design:
    - - The data was extracted form S3 bucket as input.
      - Then the columns unnecessary for aggreagation analysis were dropped using the Change Schema node.
      -  The  tree_counts wwere aggragated by street side using the Aggregate node.
      -  Finally, the output was again saved in the S3 buckets, as two outputs, a user friendly CSV file and a system readable PARQUET file.
   - Screenshots of the pipeline and saved outputs for reference.
   - ![s14](https://github.com/user-attachments/assets/4991efdc-11a3-42db-bdb4-64903beecc19)
   - ![s13](https://github.com/user-attachments/assets/727303a2-b479-48b1-af99-95dd5c2743a6)

 ### Deliverables
 - Cleaned and Processed Data in CSV and Parquet formats, avaliable for being used by other AWS services for further descriptive, exploratory, diagnostic analysis.
 - A step-by-step demonstration of the AWS console documenting the process to be followed for future ingested datasets.
 -  An established cleaning recipe, available for reuse by new connected datasets that follow the same schema.
 -   A pipeline that provides the aggregate breakdown of the number of trees planted for each street-side. 

 ### Timeline
 Total estimated time for project completion: 3 weeks
 - Data extraction and ingestion: 1 week.
 -  Data profiling and Cleaning: 1 week.
 -  Data pipeline design and implementaion: 1 week. 

 ### Impact 
This project has provide me with hands-on experience with Amazon Web Services usage for data wrangling, taking the 'street-trees' from the city of Vancouver as an example. The dataset prepared after the wrangling process can be utilized for enhancing the urban forestry analytics for the City of Vancouver. Additionally, this project showcases the scalable methods for integrating, processing, and securing data in cloud platforms.


##    
##   
## Project 2: 



  



