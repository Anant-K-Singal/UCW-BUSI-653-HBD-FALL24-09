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


### Tools and Technologies 
AWS S3 for string the raw data input and the result outputs of the other AWS services.
AWS Glue Databrew for profiling and creating cleaning recipe for the data.
AWS Glue, using the visual ETL feature to create a pipeline for data aggregation without any coding. 
AWS Athena can be used for further data transformation using SQL querying. 

![Screenshot 2024-12-13 142214](https://github.com/user-attachments/assets/7b32fe94-b369-4e14-a9b1-e522508b9c82)


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
- A new bucket 'cov-transformed-street-tree-project1' was created to store the results of the job runs.
- A profiling job was run to identify data quality issues.
- Insights:
- - 19 string columns, 2 numeric columns, and 1 geospatial column.
  -  24 % Missing data in 'Cultivar_Name' feature and 25% in 'Date_Planted'.
  -  Job confirmed that 98% of the cells contained valid data 
  -  There were no duplicate records in the dataset.
  -  Outliers were identified in the 'Height_Range_ID' and 'Diameter' columns.
  -  Central tendency values, distribution metrics and correlation values were visualized for further descriptive analysis.
- - The profiling results were saved in the 'data-profiling' folder of the 'cov-transformed-street-tree-project1' bucket.
  -  Screenshots of the profiling results for refrence.
  -  ![s7](https://github.com/user-attachments/assets/fa5f566f-6527-4e8b-a96e-da66ae87fc60)





#### 3] Data Cleaning (AWS Glue DataBrew)
- Utilizing the insights from the profiling, a cleaning project was created.
-  A cleaning recipe 'cov-cleaning-street-data-project1-recipe' was configured.
-  The following transformations were applied:
- - Replaced the empty cells in 'Cultivar_Name' with "Not Available". 
  - Replaced missing dates in Date Planted with a placeholder value "0000-00-00"
  - Handled outliers in 'Height_Range_ID' and 'Diameter' columns by removing them.
  - Ensured no duplicate values present in the 'Tree_ID' column.
  - Published the cleaning recipe to automate these steps for future datasets that have the same schema.
- Cleaned data was stored in the 'data-cleaning' folder of the transformed bucket, as two outputs.
- - A CSV file, in 'user' folder, for easy access for individual users viewing the data.
  - A SNAPPY compressed PARQUET file, in 'system' folder, for being read by the other AWS services.
- Screenshot of  the recipe for reference.
  - ![s10](https://github.com/user-attachments/assets/4e4773b2-6856-4281-8c97-83cf9236fcc8)



  #### 4] Data Transformation (AWS Glue)
    - The cleaned PARQUET file was used as input for creating an AWS Glue ETL pipeline.
    - Pipeline Design:
    - - The data was extracted form S3 bucket as input.
      - Then the columns unnecessary for aggreagation analysis were dropped using the Change Schema node.
      -  The  tree_counts wwere aggragated by street side using the Aggregate node.
      -  Finally, the output was again saved in the S3 buckets, as two outputs, a user friendly CSV file and a system readable PARQUET file.
   - Screenshots of the pipeline reference.
   - ![s13](https://github.com/user-attachments/assets/727303a2-b479-48b1-af99-95dd5c2743a6)

### Deliverables
 - Cleaned and Processed Data in CSV and Parquet formats, available for being used by other AWS services for further descriptive, exploratory, diagnostic analysis.
 - A step-by-step demonstration of the AWS console documenting the process to be followed for future ingested datasets.
 - An established cleaning recipe, available for reuse by new connected datasets that follow the same schema.
 - A pipeline that provides the aggregate breakdown of the number of trees planted for each street-side. 

### Timeline
 Total estimated time for project completion:**3 weeks**
 - **Week 1:** Data extraction and ingestion
 - **Week 2:**  Data profiling and Cleaning
 - **Week 3:** Data pipeline design and implementaion

### Impact 
This project has provide me with hands-on experience with Amazon Web Services usage for data wrangling, taking the 'street-trees' from the city of Vancouver as an example. The dataset prepared after the wrangling process can be utilized for enhancing the urban forestry analytics for the City of Vancouver. Additionally, this project showcases the scalable methods for integrating, processing, and securing data in cloud platforms.






## Project 2: Descriptive Analysis

### Title
Descriptive Analysis of Street Tree Dataset for The City of Vancouver

## Objective
In this project I assume the role of a member of the data team working for the City of Vancouver. The aim was to design and implement Data Analytical Platform (DAP) hosted on AWS for the street-trees-dataset. The primary goal of this project is to conduct a descriptive analysis on the historical data focusing on the genus "Malus" and species "XX" to summarize key characteristics of the trees planted, identify distribution patterns, and provide insights that can assist in future urban planning and maintenance strategies.


### Dataset
The dataset provides information details on over 180,000 public trees. For this analysis, (given the resouces constraints of utilizing the AWS services as a student) a subset of 526 records was selected, focusing just on trees with genus-Malus and species-XX. The Dataset contains 21 features, including: 
- **Tree Details:** Genus, species, common name, height, and diameter.
- **Location Data:** Geographic coordinates, address, neightbourhood, street-side details etc.
- **Maintenance Information:** Root barrier status, planting area type, and curb presence.
- **Time Data:** Planting date, person who planted, historical updates.


### Tools and Techniques
- AWS S3 for data ingestion, storage and organization.
- AWS DataBrew for data profiling and cleaning.
- AWS Glue for designing pipelines and transforming data.
- AWS Athena for querying datasets.
- Visualization and graphing tools like MS-Excel.

![Screenshot 2024-12-13 180000](https://github.com/user-attachments/assets/c3022d50-379c-4a49-b8f6-d0e78d864f2b)


### Methodology
#### Data Processing (AWS S3, Glue Databrew) 
- The dataset was sourced from the City of Vancouver’s open data portal and stored in AWS S3 buckets for processing.
- The data was organized into folders based on genus and species for scalability.
- Connected the dataset from the S3 bucket to Glue DataBrew for running a data profiling job.
- The results of the profiling was used to identify issues such as missing values, duplicate rows, and cardinality.
- The dataset showed 98% completeness, with only 2% of overall data containing empty cells.
- No duplicate rocords were found. Dataset cardinality was high with  less than 1% outliers. Cultivator_Name column had 24% missing values and Date_Planted had 25% values missing.
- - Screenshots from the profiling for reference.
- ![Screenshot 2024-12-13 180147](https://github.com/user-attachments/assets/d5a8026b-1ce8-4a76-9672-25cd54bdad7f)
- A cleaning project was created in Databrew, and a cleaning recipe was developed for the dataset.
- Replaced missing values with "Not available" and "0000-00-00" in the Cultivator_Name and Date_Planted columns respectively. Outliers were removed from the HEIGHT_RANGE_ID and DIAMETER columns.
- Published the cleaning recipe which can be reused for future datasets with same schema.
- Saved outputs in two formats: User friendly CSV file and System readable PARQUET file.


#### Data Trasformation (AWS Glue)
- Designed pipelines using the Visual ETL feature form AWS Glue. The pipelines can be used to preform descriptive aggregations on the dataset without any coding.
- **Pipeline design:** The data is extracted from S3 raw bucket, then the columns unnecessary for aggregation are dropped. The aggregation like count, average, median etc. is carried over the required attributes in accordance with the distribution feature.
- Using the pipeline the aggregated data of the number of trees by street side (Even, Odd, Park, Median) was calculated.
- Another pipeline was used to find the aggregation for the distribution of trees accoring to height classifications (using the Height_Range_ID feature)
- The results from the pipelines were stored back as CSV and Parquet files in designated folders within the S3 Transformed bucket.
- Using AWS Glue, a data catalog for the S3 bucket datasets can be created. This will enable maintaining metadata (e.g., schema, tags, formats) for quick understanding of related datases.
- Analysts can run specific SQL queries to compute summary statistics (like averages, counts, distributions), for a specific subsection of the dataset and answer specific analysis questions using AWS Athena, making it ideal for exploratory analysis.
- Screenshot of the pipeline for reference.
- ![Screenshot 2024-12-13 181441](https://github.com/user-attachments/assets/7f27a81b-a6de-44f6-8ad6-a2010682ab0c)
- Screenshts of an example SQL queries that can be used in AWS Athena for reference.
- ![s28](https://github.com/user-attachments/assets/bb67ea1a-1d27-4df6-8180-eb91bffd573f)



#### Statistics and Visualizations (AWS Glue Databrew, MS-Excel)
- The aggregate outputs from the data pipelnes can be used as inputs for visualization tools.
- The bar graph showing the count of Malus XX trees on each street side (Even, Odd, Park, Median) is created using MS-Excel.
- The Stacked bar graph depicting the distribution of tree heights across different street sides is created.
- The summary statistics are visualized as boxplots for all attributes in the Databrew profiling job.
- The correlation coefficients and correlation matrix is generated by the profiling.
- **Insights:**
- - The maximum number of trees are planted on the even street side (233 trees), while an extremly low number is planted on the median side (3 trees).
  - Most trees are classified within the lower height classes, with very few trees exceeding to the high height ranges.
  - Most trees have diameter on the lower end, only a very few trees are thick and haave high diameter.
  - Larger height ranges are more prevalent in specific areas, while medium or small-sized trees dominate in others.
  - Trees planted on the "Park" street side appear to have more uniform distribution across height ranges, indicating that parks host a mix of mature and younger trees.
  - Screenshot of the boxplots and feature distribution obtained from databrew for reference.
  - ![s6](https://github.com/user-attachments/assets/c7a8f549-abc6-40e9-a3c7-747043483106)
  - Screenshots of the bar charts made using spreadsheet application for reference.
  - ![Screenshot 2024-12-13 181230](https://github.com/user-attachments/assets/e64e125f-3802-4a4b-a194-fe624bb3588f)
  - ![Screenshot 2024-12-13 181209](https://github.com/user-attachments/assets/5b6a4cb3-0c71-40a4-b4a3-973ba34a7f19)

#### Recommendations
- Increase tree planting on the median street sides where close to no trees are planted. Introduce ornamental species to enhance aesthetic appeal and environmental benefits, in case Malus xx species are incompatible.
- Improve data collection processes, particularly for missing attributes like Cultivar Name. This will ensure accountability and increase the easy and quality of maintenance.
- Align tree planting plans with infrastructure limitations, especially on streets, to prevent future conflicts with utilities and roadways. Focus on medium-height species for constrained spaces while reserving larger trees for open areas.
- Establish a robust monitoring and data update schedule to capture changes in tree growth, health, and distribution. This will support ongoing analysis and better inform future decisions.
- Use publicly accessible tree data to collaborate with local researchers, urban planners, and policymakers to develop innovative solutions and optimize planting strategies for both ecological and urban needs.


### Timeline
 Total estimated time for project completion:**5 weeks**
 - **Week 1:** Data extraction, ingestion, profiling and Cleaning. 
 - **Week 2:**  Data pipeline design and implementaion
 - **Week 3:** Conducting descriptive analysis, developing visualizations and key insights.
 - **Week 4:** Preparing deatiled report of the findings and providing actionable reccomendations.
 - **Week 5:** Presentaion of the key insights from the report to the stakeholders.


### Deliverables
- A detailed descriptive analysis report summarizing the historical data, documenting the findings nad insights, and providing actionable recommendations.
- Setting up processes that can automate the profiling, cleaning and aggregation for datasets of diffrent species of trees.
- Development of a data analytical platform using Amazon's Web based cloud services, that can provide a shared space for the data team of city of vancouver to conduct data analysis. 
- Data visualizations like line graphs, bar charts, box-plots, pie charts etc. of key insights from analysis to present them to the stakeholders. 

### Impact
The descriptive analysis would provide the City of Vancouver with crucial insights into the distribution and characteristics of its planted street trees, enabling data-driven urban planning and environmental management. By identifying the planting trends, underrepresented areas, the city can optimize their tree-planting initiatives and allocate maintenance resources efficiently. This would support development of  greener neighborhoods and the progress towards the city’s sustainability goals.




## Project 3: Exploratory Data Analysis

### Title
Exploratory Data Analysis on Public Street Trees Data using Amazon Web Services.

### Objective
The primary objective of this project is to explore the public street tree dataset provided by the City of Vancouver to gain insights into the characteristics and patterns of various trees species of genus "Malus" planted across various street sides, neighbourhoods, blocks and regions. By leveraging AWS tools, we analyze the distribution of trees based on attributes such as height, species, and location placement to identify trends that can provide insights to inform urban forestry planning decisions.

### Background
Let us assume the role of a  data team member for the City of Vancouver. The aim is to design a Data Analytical Platform (DAP) using Amazon cloud services to conduct exploratory analysis on the stret trees plantaion data focusing on the different species of genus "Malus". The analysis goalis to explore the key characteristics of the trees planted, identify distribution patterns, and provide insights that can assist city of vancouvers development goals.

### Dataset 
The dataset consists of 850 records and 21 features for the 'Malus' genus trees, extracted from the open portal or city of vancouver containing over 180,000 public tree information excluding private and park trees. The schema of the dataset is as follows: 
- TREE_ID: Unique numerical identifier for each tree.
- CIVIC_NUMBER: Street address of the tree's location.
- SPECIES_NAME: NAme of the species of the tree from genus-Malus.
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


### Tools and Techniques
- AWS S3 for data ingestion, storage and organization.
- AWS DataBrew for data profiling and cleaning.
- AWS Glue for designing pipelines and transforming data.
- AWS Athena for querying datasets.
- Visualization and graphing spreadsheet applications like MS-Excel.
- ![Screenshot 2024-12-14 221135](https://github.com/user-attachments/assets/5246a771-c683-4888-87b7-cf1ceb74a607)


### Methodology 
#### Data Collection and Preparation (AWS S3, Glue Databrew) 
- The dataset was extracted from the open data portal of the city of vancouver, after filtering out the data records just for genus-Malus.
- The extrwcted dataset was ingested into AWS using Amazon S3 into a bucket dedicated for raw data storage.
- A logical bucket organization scheme was established, creating folder hierarchy for genus and species sun-folders to emulate a scalable data ingestion framework.
- A profiling job was carried out after connecting it to AWS Glue DataBrew and results insights aided to identify data descripencies such as missing values, duplicate rows, outliers and cardinality.
- The profiling also provided the distribution of the dataset attributes and the correlation coefficients between the numerical features.
- After profiling, in Databrew a cleaning project was created. The cleaning recipe which was developed during the descriptive analyis in project 2, was used to clean this dataset as it had the same schema.
- The recipe processes the data to replace the missing values with "Not available" and "0000-00-00" in the Cultivator_Name and Date_Planted columns respectively. The Outliers were removed from the HEIGHT_RANGE_ID and DIAMETER columns.
- The outputs are saved in the trasformed databucket as a user friendly CSV file and a system readable PARQUET file.

#### Exploratory Analysis (AWS Glue)
- A pipeline is developed to combine the new cleaned dataset with the previously cleaned data for the 'xx' species stored in S3 the trasformed data bucket. AWS GLue visual ETL feature is utilized for this process.
- The pipeline xtracts the two cleaned datasets in PARQUET formats from S3, and uses the 'Union' node to combine the two, to created a new master dataset. It uses Tree_id as the primary refrence key. This union is possible because the datssets have the same metadata.
- Once combined the same pipeline is further developed to find aggregation and distribution statistics for exploratory analysis.
- The columns not required for the analysis were dropped, and the aggregate node was added to compute the count of trees grouped by multiple dimensions, such as: Street Sides (Even, Odd, Median, Park), Neighbourhood Name, Blocks, and Curbs across Tree Height range and Diameter. These aggregations revealed patterns in the spatial distribution of tree species and their physical attributes.
- The output of the pipeline is stored back in the transform data S3 bucket.
- Screenshot of a pipline for Reference.
-  ![Screenshot 2024-12-14 214852](https://github.com/user-attachments/assets/7dd0d502-266d-463e-8a20-5c5bd7fe9f15)


#### Visualizations and Insights (AWS Glue Databrew, MS-Excel)
- The summarized amd aggregated results were utilized to generate meaningful visualizations, helping to uncover patterns and trends in the spatial and physical distribution of the Malus genus trees. AWS Glue Databrew and MS-Excel were used for this purpose.
- A bar chart was created to illustrate the number of trees of various species planted on each street side (Even, Odd, Median, Park).
- A stacked barchart illustrated the distribution of the tree heights across the species.
- Stacked Bar plots was also created for the count of trees grouped by species across diffrent neighborhoods.
- The box plots showed the frequency distribution for the tree diameters and height ranges. Almost all species had only a few trees in the higher diameter and height ranges.
- A pie chart demonstrated the aggreage of the trees across root barriers and with and without curbs.
- A scatter plot was created using geo_point_2d data to map the spatial distribution of trees across the city.
- A heatmap was produced to visualize the relationship between neighborhoods and tree height ranges.
- Screenshot example of a distribution graph for reference.
- ![Screenshot 2024-12-14 214908](https://github.com/user-attachments/assets/17459371-0e51-4f95-a30e-7dbd3de536ff)


#### Recommendations
- Tree planting can be focused in underutilized spaces, such as medians and odd-numbered street sides, to achieve a more equitable distribution. Planting shorter species on medians can be considered to accommodate space constraints.
- Target planting in neighborhoods with low tree densities to balance tree cover across the city. Plans to engage the local communities in tree-planting can be developed, this will promote awareness and participation.
- Trees planted on curbs exhibit smaller diameters, possibly due to restricted growing conditions. The use of root barriers can help improve soil quality to support healthier growth hence increasing curbside planting. Alterntively, smaller species with lower space requirements can be favoured for curbside planting.
- Taller trees are predominantly found on even street sides indicating the even side having more planting space. Schedule regular maintenance to ensure tree health and prevent potetial interference with overhead utilities sue to tree growth.
- develop plans to allocate maintenance resources proportionally to areas with a higher concentration of larger and taller trees. Also use the maintenance insights collected to prevent overgrowth and ensure tree health.


### Timeline
 Total estimated time for project completion:**7 weeks**
 - **Week 1:** Data extraction, ingestion, profiling and Cleaning. 
 - **Week 2:**  Data pipeline design and implementaion.
 - **Week 3:** Conducting descriptive analysis, developing visualizations and key insights.
 - **Week 4,5**: Carry out exploratory analysis to identify treands and distribution patterns. 
 - **Week 6:** Prepare a deatiled report containing the findings and providing actionable reccomendations.
 - **Week 7:** Presentaion and visualization of the key insights to present to the stakeholders.



### Deliverables
- A cleaned and combined master dataset containing data for multiple Malus species.
- Development of processes on the Data Analytical Platform to automate the progiling, cleaning, combining and aggregation processes for future ingested data.
- Establishing a pipeiline design to provide aggregation results for tree distributions across street sides, neighborhoods, height ranges, blocks, and curb placements.
- A exploratory report conatining visualizations in the form of Charts and Graphs, showcasing the trends and patterns of the planted trees.
- A detailed documentation of the methodology, tools used, insights obtained from the exploratory analysis and providing actionable recommendations including suggestions for tree planting and urban forestry management.



  



  














##    
##   

## Project 4: Data Wrangling

### Title
Implementation of Data Wrangling Measures for the UCW Student Information Dataset Using Amazon Web Cloud Services.

### Objective
The objective of this project is to design and implement a robust Data Wrangling framework using AWS services to process and prepare the University Canada West (UCW) student information dataset. This framework ensures that the data is clean, structured, and ready for further analysis. The project aims to address common issues such as missing values, duplicates, and formatting inconsistencies. The wrangling process will enhance the dataset's reliability, enabling accurate decision-making in areas such as academic standing, course enrollment, visa regulation compliance, scholarship disbursals, and emergency response planning.

### Background
The Registrar’s Office at University Canada West is responsible for maintaining the student information database, and relies on critical information recorded in the dataset for institutional operations such as academic standing evaluations, visa compliance checks, scholarship processing, and emergency management. However, challenges such as missing fields, inconsistent data formats, and duplicate records can undermine operational efficiency and decision-making. This project demonstrates how AWS cloud services can be leveraged to address these issues by implementing a scalable data wrangling framework to improve data quality and readiness for further analysis. 

### Dataset 
The dataset used in this project is a dummy dataset, generated to reflect the structure and attributes of the actual UCW student information database. It contains 50 records across 21 attributes, including:
-  Identification and Demographics: Student_ID, Name, Date_of_Birth, Age, Gender, Country_of_Citizenship.
-  Academic Information: Program, Cumulative_CGPA, Program_Start_Term, Year_of_Enrollment, Enrollment_Status, IELTS_Score. 
-  Contact Information: EmailPhone_Number, Current_Address, Emergency_Contact_Name, Emergency_Contact_Phone.\
-  Administrative and Compliance Information: SIN_Number, Passport_Number, Visa_Status.
-  Financial Information: Scholarship_Amount.
-  Health Information: Health_Insurance_Status, BC_Health_Card_Number.

  

### Tools and Techniques
- AWS S3: For storing raw data inputs.
- AWS Glue DataBrew: For data profiling and cleaning with reusable recipes.
- AWS Glue Pipelines: For data transformation and summarization.
- ![Screenshot 2024-12-14 231137](https://github.com/user-attachments/assets/fa7d6719-5ea9-45f4-b170-ad55ab5fdcbe)


### Methodology
#### Data Ingestion (AWS S3)
- The dummy dataset was generated and saved as an Excel file, then this file is uploaded to AWS S3 inside the raw data bucket.
- Organized the folders and subfolders to create a structure for ingestion according to year and ingestion frequency for efficient access.
- S3 standard storage class has been used since the dataset is accessed frequently. S3 standard ensures scalability, facilitate streamlined and long-term storage.
- Bucket versioning was enabled to prevent against loss of data.


#### Data Profiling (AWS Glue DataBrew)
- The ingested raw dataset is connected to AWS Glue DataBrew.
- A profiling job is run for the dataset to get information of the dataset's quality.
- The dataset contains 16 string columns, 3 numerical columns, and 2 date columns.
- It was found that there was 8% missing values in Emergency_Contact_Name and 5% missing in Health_Insurance_Status.
- Outliers are identified in Scholarship_Amount and few duplicate rows are detected in Student_ID.
- The profiling results are saved in the 'data-profiling-results' folder within the S3 bucket dedicated for storing transformed data.

#### Data Cleaning (AWS Glue DataBrew)
- The insights from the data profiling are utilized and a cleaning project was created using in Glue DataBrew.
- A reusable cleaning recipe was developed the following transformation were applied:
- - Replaced missing entries with "Not Available" in the Emergency_Contact_Name feature.
  - Health_Insurance_Status column is filled with "Unknown".
  - Removed duplicate rows based on Student_ID, and only unique values are kept.
  - Removed high outlier values in Scholarship_Amount, based on maximum and minimum scholarship awarded information.
  - Standardize Date_of_Birth format to YYYY-MM-DD.
- The cleaned data is stored in the transformed data bucket, in two formats. A CSV file to be read by users and a Snappy Parquet file for use by other AWS services.

#### Data Pipeline Design (AWS Glue Pipelines)
- The cleaned Parquet file stored in S3 transformed bucket is extracted and used as input to design a pipeline in AWS Glue.
- The cloumns not required to generate aggregate statistics are dropped from the analysis.
- The data was aggregated using the Aggregate node to compute the average Cumulative_CGPA grouped by Program and Total Scholarship_Amount awarded by IELTS_score.
- The result is saved back in the transformed data bucket, in a CSV and PARQUET formats.



### Deliverables
- Dataset that has been cleaned and processed into CSV and Parquet formats, ready for use by other AWS services for additional descriptive, exploratory, and diagnostic analysis.
- A detailed documentaion providing a walkthrough of the AWS console outlining the procedure to be utilized for data wrangling.
- A standardized cleaning formula, accessible for reuse by new updated student information datasets that adhere to the same data attribute structure.


### Timeline
 Total estimated time for project completion:**3 weeks**
 - **Week 1:** Data generation and ingestion.
 - **Week 2:**  Data profiling and Cleaning.
 - **Week 3:** Data pipeline design and implementaion.

### Impact 
This project improves the integrity and usability of the UCW student information dataset by addressing data quality issues. Using AWS services it establishes a scalable and reusable framework for data preparation, enabling the results to be utlizied for creating efficient workflows and reliable decision-making processes. The cleaned and structured dataset supports critical processes such as enrollment management, academic standing evaluation, visa and regualtor compliance, and scholarship allocation, ensuring readiness for advanced analytics and fostering data-driven planning at the university.





## Project 5: Data Quality Control 

### Title
Implementation of Data Quality Control Measures for the UCW Student information Using AWS. 

### Objective
The objective of this project is to design and implement a robust Data Quality Control (DQC) framework for the student dataset managed by the Registrar’s Office at University Canada West. This framework would ensure the integrity, compliance, consistency, and security of student records, enabling reliable decision-making processes for course enrollment, visa compliance, emergency medical senarios and scholarship eligibility.

### Background
The Registrar’s Office at University Canada West is responsible for maintaining the student information database, which contains sensitive and critical information such as academic performance, visa status, enrollment eligibility, personal identification and medical details. This dataset plays a key role in many decision-making processes, such as determining students’ academic standing, course enrollments eligibility, scholarship reimbursements, scheduled break approvals, and compliance with institutional and regulatory policies. However, challenges such as missing values in fields, duplicate entries, and inconsistencies in formatting can lead to inefficiencies and errors. These data quality issues risk undermining operational accuracy and decision-making. This project aims to leverage AWS services to implement a robust Data Quality Control (DQC) framework empowering the Registrar’s Office ability to streamline operations, maintain compliance, and support institutional goals effectively.

### Dataset
The dataset used in this project is a dummy data that has been generated using AI tools to mirrior the actual student information collected and manintained by the Registrar's Office of University Canada West.
The dummy dataset contains 50 student records across 21 attributes. Below are the key attributes:
- Student_ID: A unique identifier assigned to each student.
- Name: Full name of the student.
- Age: Age of the student.
- Gender: Gender of the student.
- Date_of_Birth: Birthdate of the student in YYYY-MM-DD format.
- Country_of_Citizenship: The country of citizenship of the student.
- Program: The academic program in which the student is enrolled.
- Email: Student's personal email address.
- Phone_Number: Contact phone number of the student.
- Current_Address: The student's current residential address.
- Emergency_Contact_Name: Name of a person to contact in case of an emergency.
- Emergency_Contact_Phone:Phone number of the emergency contact.
- SIN_Number: The student’s Social Insurance Number.
- Passport_Number: The student’s passport number.
- Cumulative_CGPA: Cumulative Grade Point Average of the student.
- Visa_Status: The current visa status of the student (e.g., Student Visa, Permanent Resident).
- Enrollment_Status: Current enrollment status (e.g., Active, Suspended, Graduated).
- IELTS_Score: The score of the International English Language Testing System (IELTS) exam.
- Scholarship_Amount: Amount of scholarship awarded to the student.
- Health_Insurance_Status: Indicates if the student has health insurance coverage.
- BC_Health_Card_Number: The student’s British Columbia health card number.
- Program_Start_Term: The term in which the student started the program.
- Year_of_Enrollment: The year the student enrolled in the university.
- Screenshot of part of the dataset for reference.



### Scope
The project encompasses the following key areas: 
- **Data avalibility** -securely store datasets in AWS S3 with versioning and replication to maintain a history of changes and backup to enable data rollback and prevent against data loss.
- **Data Protection**-protect data against external breaches and data theft attempts, by encrypting and restricting access.
- **Data Sensitivity**-ensure the integrity of student personal and sensitive information.
- **Data Validity**-Define and implement automated checks to ensure consistency, accuracy, and completeness of critical attributes.
- **Data Monitoring**-Establish real-time monitoring of data quality metrics and generate insights using dashboards to track trends, identify anomalies, and warn against thresholds.

### Tools and Techniques
- AWS S3 is used for establishing replication rules and bucket versioning.
- AWS Glue Databrew for profiling and cleaning the data to address missing values, outliers, and other inconsistencies.
- Utilize AWS Glue to created pipleine for establishing quality control metrics and dealing with sensitive information.
- AWS CloudTrail is used to track access and modifications on the strored data by users and other services.
- Set up alarms and dashboard for real-time monitoring of Costs, Glue jobs, S3 usage, and performance metrics using AWS CloudWatch.

![Screenshot 2024-12-13 144119](https://github.com/user-attachments/assets/656f4ca3-94c9-467e-b3c3-c26c8c4dd52e)

### Methodology
#### Storage Versioning and Replication (AWS S3)
- Organized datasets into a dedicated S3 buckets for storing the raw, trasformed and curated data respectively structured by data ingestion year and ingestion frequency for efficient access.
- Enabled S3 bucket versioning to track changes and maintain historical records to datasets, providing rollback capabilities in case of errors or data quality issues.
- Configured and setup cross-region replication rules for the S3 buckets. This creates copies of the objects in the bucket for disaster recovery and protection against data loss.
- Screenshot of bucket versioning enabled for reference.
- ![s19](https://github.com/user-attachments/assets/4678605c-44b7-47f2-b7e4-61010deb309a)
- Screenshot of the replication rule setup for reference.
- ![s20](https://github.com/user-attachments/assets/d935d302-0a9f-427e-89e5-1b5676f5fefe)




#### Data Encryption (AWS KMS)
- Created a custom key that can be used for encrypting data using Amazon Key Management Services.
- Applied encryption to the S3 buckets using this custom generated key.
- Created role-based policies to restrict access to encryption keys, ensuring that only authorized users can decrypt sensitive data.
- Defined bucket policies and IAM roles to restrict access ensuring compliance with data privacy requirements.
- Screenshot of the Custom Key generated for Reference.
- ![s18](https://github.com/user-attachments/assets/5212c137-7e63-459b-9ea6-d781cf004697)



#### Profiling and Cleaning (AWS Glue Databrew)
- The datasets are connected to the AWS Glue Databrew and a profiling job is run on them.
- The generated profiling report aids us to identify patterns, missing values, outliers and any other anomalies presnt in the data features.
- The profiling insights can be used to identify and plan cleaning processes required to the features.
- Created a cleaning project and developed reuseable cleaning recipe for the data in AWS Glue Databrew.
- Cleaned the raw dataset by addressing format inconsistencies in columns like date_of_birth and phone_number, and including foramt standardizing where possible.
- Replaced the missing values with appropriate placeholders for fields like Visa_Status, IELTS_Score, Emergency_Contact_Phone.
- Identifying and removing duplicates records and outliers.



#### Data Sensitivity and Validation (AWS Glue)
- A pipeline was developed to address sensitive information and validate the data.
- - The pipeline starts by extracting the cleaned and formatted student data (obtained from the above cleaning step), stored in the S3 bucket.
  - Then adjusted the data schema to ensure consistent attribute data types, dropping cloumns (with unstructured textual information if any), attribute compatibility and uniformity.
  - Next, detecting the sensitive data attributes utilizing AWS Glue’s PII detection transform to flag and censor sensitive information, such as SIN_Number and Passport_Number. Since, UCW is a Canadian University, the protection rules applied are limited to those applicable only in Canada.
  - Evaluate the data quality by implementing automated checks for consistency, completeness, and uniqueness for key attributes like Student_ID, Cumulative_cgpa, Visa_status etc.
  - At the end, the data is categorized by conditional routing and saved as system readable PARQUET files. The records that meet all the validation criteria, are saved as 'Passed' while those that do not meet one or more conditions are saved as 'failed'.
- The results of the pipleine are stored in the curated S3 bucket.
- For all further analysis and use by other AWS services, the data that has passed the validation and quality checks through this pipeline should be used.
- Screenshot of the pipeline for reference.
- ![Screenshot 2024-12-12 200119](https://github.com/user-attachments/assets/dd156404-5a14-4518-b2c1-90b004c8a284)




#### Monitoring and Control (AWS CloudTrail, AWS CloudWatch)
- AWS CloudTrail can be used to keep a track on the changes made on the datasets stored in S3.
- - CloudTrail is configured to record activities on the datasets by other AWS services.
- - It tracks access, updates, or deletions, ensuring a comprehensive audit trail of who accessed the data, what changes were made, and when they occurred. Hnece, providing historical logs for post-incident investigations.
- - This helps in identifying any unauthorized or suspicious activities related to sensitive student data and ensures compliance with regulatory standards.
  - - Screenshot of AWS CloudTrail for Reference.
  - - ![s16](https://github.com/user-attachments/assets/3be1d4d4-d71a-42d9-a8ef-ead339f4b208)

- AWS CloudWatch is used to set up real-time monitoring of key metrics related to the platform use and system performance.
- - Custom alarms are set-up for various components of the platform. The alarms can be configured to execute certain steps and codes, or send alerts when defined thresholds are breached.
  - The frequency of the monitoring can be customized based on the data generation rate of the components, thier monitoring importance and the associated costs.
  - The creation of custom dashboards enables us to track performance and monitor key metrics in real-time via visualizations like graphs, logs, gauges etc.
  - They can be used to observe trands, spikes, irregularities for metrics like platform costs, Glue jobs success rates, error counts, and S3 bucket capacities.
  - Screenshot of an alarm for reference.
  - ![s23](https://github.com/user-attachments/assets/3a561cf9-4e1a-4b08-8296-9c5b76fbce3c)
  - Screenshot of the dashboard for reference.
  - ![s17](https://github.com/user-attachments/assets/3a63b4ee-0670-4fa7-9fd6-5e62eb9b907c)

 

### Timeline
 Total estimated time for project completion:**4 weeks**
 - **Week 1:** Setting up S3 buckets, versioning, replication rules and encryption.
 - **Week 2:**  Data profiling and Cleaning for removing data inconsistencies. 
 - **Week 3:** Data pipeline design and implementaion for quality control.
 - **Week 4:** Setting up alarmas and configuring dashboard for monitoring.

### Deliverables
-  Organized, cleaned, and schema-validated datasets securely stored in S3 with versioning and replication for backup.
-  Establish a data quality control framework for handling sensitivity information and implementing validation and control rules.
-  Creation of dashboard for monitoring real-time insights, trends and summarizing key data quality metrics.

### Impact 
This Data Quality Control (DQC) project ensures the University Canada West’s student dataset is accurate, secure, and compliant with regulatory standards. By automating data validation, encryption, and monitoring processes using AWS tools, it enhances decision-making for course enrollment and scholarship management while safeguarding sensitive information and improving operational efficiency.















  



