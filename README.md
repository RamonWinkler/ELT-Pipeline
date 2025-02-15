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

## How these tools work together
Tool	Purpose in ELT Pipeline
PostgreSQL	Stores raw and transformed data
dbt	Applies SQL-based transformations inside Postgres
CRON	Schedules simple ELT jobs but lacks monitoring
Airflow	Manages worklflows, dependencies, and retries.
Airbyte	Handles automated data extraction/loading

# Custom ELT Project

This repository contains a custom Extract, Load, Transform (ELT) project that utilizes Docker, Airbyte, Airflow, dbt, and PostgreSQL to demonstrate a simple ELT process.

## Repository Structure

1. **docker-compose.yaml**: This file contains the configuration for Docker Compose, which is used to orchestrate multiple Docker containers. It defines multiple services:
   - `source_postgres`: The source PostgreSQL database running on port 5433.
   - `destination_postgres`: The destination PostgreSQL database running on port 5434.
   - `postgres`: The postgres database used to store metadata from Airflow.
   - `webserver`: The Web UI for Airflow.
   - `scheduler`: Airflow's scheduler to orchestrate your tasks.

2. **airflow**: This folder contains the Airflow project including the dags to orchestrate both Airbyte and dbt to complete the ELT workflow

3. **postgres_transformations**: This folder contains the dbt project including all of the custom models we will be writing into the destination database

4. **source_db_init/init.sql**: This SQL script initializes the source database with sample data. It creates tables for users, films, film categories, actors, and film actors, and inserts sample data into these tables.

## How It Works

1. **Docker Compose**: Using the `docker-compose.yaml` file, a couple Docker containers are spun up:
   - A source PostgreSQL database with sample data.
   - A destination PostgreSQL database.
   - A Postgres database to store Airflow metadata
   - The webserver to access Airflow throught the UI
   - Airflow's Scheduler

2. **ELT Process**: Within Airbyte, you will input the information for both the source and destination databases to create a connection. From there, you'll take the connection ID from the Airbyte UI, plug it into the `elt_dag.py` file so that when you run Airflow, it can run the sync for you. After the sync is complete, Airflow will run dbt to run the transformations on top of the destination database. 

3. **Database Initialization**: The `init.sql` script initializes the source database with sample data. It creates several tables and populates them with sample data.

## Getting Started

1. Ensure you have Docker and Docker Compose installed on your machine.
2. Clone this repository.
3. Navigate to the repository directory and run `./start.sh`.
4. Once all containers are up and running, you can access the Airbyte UI at `http://localhost:8000` and the Airflow UI at `http://localhost:8080`
5. Within Airbyte, you'll input the information for both the source and destination databases. (Information can be found in the `docker-compose.yaml` file in the root directory)
6. Take the Connection ID once you have created the connection and paste the string into the `CONN_ID` variable found in the `elt_dag.py` file. That will be located in the `airflow directory`
   1. *NOTE*: You may need to restart the set of Docker containers that have Airflow and dbt in order for the new Connection ID to propogate in the services
7. Once that is all setup, you can head into Airflow, run the DAG and watch as the ELT process is orchestrated for you!

