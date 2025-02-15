# ELT-Pipeline
## Introduction
First, I built a manual ELT script which runs in a single execution when manually triggered. 
The ELT process consists of:
•	Extract(E): Dumping data from the source_postgres database using pg_dump.
•	Load(L): Importing the dumped data into destination_postgres using psql.
•	Transform(T): Not included yet, but usually done in SQL
In a production environment, other tools can help automate and scale the process.
## PostgreSQL
A tool for SQL-based data transformation. PostgreSQL is both source and destination database. 
## Docker
Docker containerizes the entire ELT process, ensuring it runs consistently on any machine without dependency issues. 
Component	Role of Docker
source_postgres	Runs PostgreSQL inside a container. No need to install it manually
destination_postgres	Same as above, but for the destination database
elt_script	Runs the ELT process inside a Python containerwith all dependencies pre-installed. 

## Automation 
This script has handles only full dump/restore, which isn’t scalable for large datasets. 
In a production environment, other tools can help automate and scale the process. Therefore, the initial approach was updated:
## dbt(Data Build Tool) 
•	A tool for SQL-based data transformation
•	Instead of manually transforming data inside destination_progress, you can define dbt models (SQL files) that apply transformations automatically.
•	Can be integrated into an ELT workflow to run transformations after loading data.
## CRON Job
•	A unix-based job scheduler
•	Instead of running the script manually, you can schedule it using cron.
## Apache Airflow
•	A workflow orchestration tool for managing data pipelines
•	Instead of using a single Python script, Airflow allows to define DAGs (Directed Acyclic Graphs) that define dependencies.
•	Allows scheduling similar to CRON (but more flexible)
•	Allows for monitoring & alerts (If something goes wrong, Airflow notifies you)
## Airbyte
•	ETL/ELT tool designed to extract data from various sources (e.g., APIs, databases) and load it into destinations
•	Instead of manually handling pg_dump and psql, Airbyte provides pre-built connectors. 
•	Supports incremental loading, reducing data transfer time.
•	Can integrate with Airflow for automation
How these tools work together
Tool	Purpose in ELT Pipeline
PostgreSQL	Stores raw and transformed data
dbt	Applies SQL-based transformations inside Postgres
CRON	Schedules simple ELT jobs but lacks monitoring
Airflow	Manages worklflows, dependencies, and retries.
Airbyte	Handles automated data extraction/loading

THE PROJECT

 Readme aus github project
![image](https://github.com/user-attachments/assets/8ba5d210-82c4-4f16-8674-7bb3d798ea49)
