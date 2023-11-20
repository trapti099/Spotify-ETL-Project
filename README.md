## Spotify-ETL-Project
<img width="608" alt="image" src="https://github.com/trapti099/Spotify-ETL-Project/assets/63699608/842cd649-e961-4b2b-ab5e-07b07ee40b74">

#Spotify ETL using Python and AWS
Objective
Develop an ETL pipeline using Python and AWS services to obtain the artist, album, and song information from the “Discover Weekly” playlist on Spotify. This playlist is updated every Monday and consists of 30 personalized songs tailored for each user. Alternatively, you can use an IFTT service to create an archive of this playlist. In the course, Darshil used the “Top Songs — Global” playlist which is updated weekly.

The pipeline involves extracting data from the Spotify API, transforming the data to prepare it for analysis, loading the transformed data into Amazon S3, and querying the transformed data using Amazon Athena, utilizing AWS Glue for cataloging and metadata management.

Key Learnings
What you can learn from this project:

-How to extract data from API
-Build an automated trigger to run data pipelines (EventBridge trigger)
-Write extract and transformation jobs (Lambda) and store raw data (S3)
-Build the correct bucket structure for data storage
-Automate transformation job (S3 trigger) for new data
-Build analytics layer for SQL queries and business intelligence (Athena)

Credits: Darshil Parmar’s LinkedIn post
