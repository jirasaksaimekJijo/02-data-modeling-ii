# Week2: Data-Modeling-with-Cassandra-NoSQL

## Description
Cassandra is a NoSQL database system designed to handle large volumes of data with high density, particularly in scenarios where data is written frequently and read in various patterns. It employs various features such as distributed architecture and fault tolerance to ensure efficiency and high stability. Additionally, Cassandra excels in real-time operations and offers flexible scalability, making it popular for systems requiring high speed and reliability. It is especially favored in industries that demand high-speed data management and analysis, such as internet service user tracking systems and financial transaction management platforms.

We can use Cassandra to store data from a JSON file by using tools like DataStax DevCenter or interacting directly with DataStax Apache Cassandra. These tools allow us to connect to the Cassandra database and manage data easily.

## Prosesc
1. Create Keyspace:
   In Cassandra, we first create a Keyspace to serve as the storage area for our data. A Keyspace is similar to a database in
   traditional RDBMS. We can use the CREATE KEYSPACE command to create a Keyspace, specifying the name and other parameters as needed.

2. Create Table:
   Once the Keyspace is created, the next step is to create a Table to store our data. We use the CREATE TABLE command to define the
   name of the table and the schema of the data to be stored.

3. Import JSON Data:
   After creating the Keyspace and Table, we can use DataStax DevCenter or CQL (Cassandra Query Language) commands to import data from
   the JSON file into the created table. DataStax DevCenter provides convenient tools for this operation, or we can directly use the
   COPY FROM command to import data from CSV or JSON files via CQL.

4. Querying: Once the data is successfully imported, we can use CQL to query, modify, or delete data as needed.
  
## Library Python

If you want to consolidate all the libraries and versions into one file for installation in one go, you can use either a requirements.txt file or an environment.yml file. These are convenient and efficient ways to manage dependencies for your project.

- equirements.txt: This file specifies the names of the libraries and the versions to be installed. You can create one like this:
```bash
cassandra-driver==3.25.0
click==8.1.3
geomet==0.2.1.post1
numpy==1.23.2
python-dateutil==2.8.2
pytz==2022.2.1
six==1.16.0
```
## 
The command you would use to install libraries, and once executed, the specified libraries will be ready to use. 

```bash
pip install -r requirements.txt
```
- cassandra-driver
- click
- geomet
- numpy
- python-dateutil
- pytz
- six

## Working steps of this project
### Step 1: 

1. Run the environment so that the tools can be used to perform tasks.
   But because we have a file .yml

   The .yml file will configure the environment for Cassandra, making it ready for use.

run in command line
```bash
docker compose up
```
   When run, the tool we have configured in the .yml file will execute.
   To enable and proceed with the next steps.
### Step 2: 

### Step 3: Creating tables and loading data into Cassandra according to the created tables

It will create an events table with columns id, type, pulib and PK id and type

```bash
     CREATE TABLE IF NOT EXISTS events
    (
        id text,
        type text,
        public boolean,
        PRIMARY KEY (
            id,
            type
        )
    )
```

### Step 3: Get data from Json file all to table in Cassandra
