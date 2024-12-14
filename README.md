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
In this project I assume the role of a member of the data team woking for the City of Vancouver. The aim was to design and implement Data Analytical Platform (DAP) hosted on AWS for the street-trees-dataset. The primary goal of this project is to conduct a descriptive analysis on the historical data focusing on the genus "Malus" and species "XX" to summarize key characteristics of the trees planted, identify distribution patterns, and provide insights that can assist in future urban planning and maintenance strategies.



### Dataset
The dataset provides information details on over 180,000 public trees. For this analysis, (given the resouces constraints of utilizing the AWS services as a student) a subset of 526 records was selected, focusing just on trees with genus-Malus and species-XX. The Dataset contains 21 features, including: 
- **Tree Details:** Genus, species, common name, height, and diameter.
- **Location Data:** Geographic coordinates, address, neightbourhood, street-side details etc.
- **Maintenance Information:** Root barrier status, planting area type, and curb presence.
- **Time Data:** Planting date, person who planted, historical updates.


### Tools and Techniques
AWS S3 for data ingestion, storage and organization.
AWS DataBrew for data profiling and cleaning.
AWS Glue for designing pipelines and transforming data.
AWS Athena for querying datasets.
Visualization and graphing tools like MS-Excel.

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
- ![s7](https://github.com/user-attachments/assets/e3f893c1-3663-44ab-93f3-a6e43b9adac2)
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
- Screenshts of example SQL queries that can be used in AWS Athena for reference.
- ![s28](https://github.com/user-attachments/assets/bb67ea1a-1d27-4df6-8180-eb91bffd573f)
- ![s27](https://github.com/user-attachments/assets/4f54eace-42ce-4eb6-b701-a6d0160c93af)



#### Statistics and Visualizations
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
- 
- 


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




  














##    
##   

## Project 3: Data Quality Control 

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
- ![Screenshot 2024-12-13 134112](https://github.com/user-attachments/assets/d3159558-cbc8-4fe8-8d0b-e20d73310119)



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
- Screenshot of the Encryption of S3 bucket for reference.
- ![s21](https://github.com/user-attachments/assets/3aedb8ca-60c2-43e9-876c-8f2f21f4ff19)


#### Profiling and Cleaning (AWS Glue Databrew)
- The datasets are connected to the AWS Glue Databrew and a profiling job is run on them.
- The generated profiling report aids us to identify patterns, missing values, outliers and any other anomalies presnt in the data features.
- The profiling insights can be used to identify and plan cleaning processes required to the features.
- Created a cleaning project and developed reuseable cleaning recipe for the data in AWS Glue Databrew.
- Cleaned the raw dataset by addressing format inconsistencies in columns like date_of_birth and phone_number, and including foramt standardizing where possible.
- Replaced the missing values with appropriate placeholders for fields like Visa_Status, IELTS_Score, Emergency_Contact_Phone.
- Identifying and removing duplicates records and outliers.
- Screenshot of the profiling output for reference.
- ![s24](https://github.com/user-attachments/assets/33560e1a-4704-4642-a158-adc99b7f8ad4)
- Screenshot of the cleaning jobs for reference.
- ![s22](https://github.com/user-attachments/assets/428cbb35-dc8d-438a-8e12-bfed101fd0f2)


#### Data Sensitivity and Validation (AWS Glue)
- A pipeline was developed to address sensitive information and validate the data.
- - The pipeline starts by extracting the cleaned and formatted student data (obtained from the above cleaning step), stored in the S3 bucket.
  - Then adjusted the data schema to ensure consistent attribute data types, dropping cloumns (with unstructured textual information if any), attribute compatibility and uniformity.
  - Next, detecting the sensitive data attributes utilizing AWS Glue’s PII detection transform to flag and censor sensitive information, such as SIN_Number and Passport_Number. Since, UCW is a Canadian University, the protection rules applied are limited to those applicable only in Canada.
  - Evaluate the data quality by implementing automated checks for consistency, completeness, and uniqueness for key attributes like Student_ID, Cumulative_cgpa, Visa_status etc.
  - At the end, the data is categorized by conditional routing and saved as system readable PARQUET files. The records that meet all the validation criteria, are saved as 'Passed' while those that do not meet one or more conditions are saved as 'failed'.
- The results of the pipleine are stored in the curated S3 bucket.
- For all further analysis and use by other AWS services, the data that has passed the validation and quality checks through this pipeline should be used.
- Screenshot of the pipeline and the job run for reference.
- ![Screenshot 2024-12-12 200119](https://github.com/user-attachments/assets/dd156404-5a14-4518-b2c1-90b004c8a284)
- ![s15](https://github.com/user-attachments/assets/581b08b0-5b6f-4d12-b9bf-575a2b68aa3e)



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















  



