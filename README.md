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

- Python 3.x
- glob
- json
- os
- psycopg2 
  
`If Library doesn't give`
```bash
$ pip install ..... (Library Name)
```

## Working steps of this project
### Step 1: Running PostgreSQL on Docker
Running PostgreSQL on Docker allows you to deploy and manage a PostgreSQL database system within a Docker container. Docker is a platform that enables developers to package applications and their dependencies into lightweight containers, which can then be easily deployed across different environments.

1. Run the environment so that the tools can be used to perform tasks.
But because we have a file .yml

run in command line
```bash
docker compose up
```
When run, the tool we have configured in the .yml file will execute.
To enable and proceed with the next steps.

### Step 2: Create a table for storing data on Postgres
The tables that you can create to store the data are divided into 4 tables as you mentioned:

1. Events Table:
Used to store data related to events that occur, such as ID, type, actor ID, and a foreign key constraint on actor_id.
```bash
    CREATE TABLE IF NOT EXISTS events (
        id text PRIMARY KEY,
        type text,
        actor_id int,
        CONSTRAINT fk_actor FOREIGN KEY(actor_id) REFERENCES actors(id)
    )
```

2. Repo Table:
Used to store data related to repositories associated with ID, name, URL, actor ID, and a foreign key constraint on actor_id.

```bash
    CREATE TABLE IF NOT EXISTS repo (
        id text PRIMARY KEY,
        name text,
        url text,
        actor_id int,
        CONSTRAINT fk_actor FOREIGN KEY(actor_id) REFERENCES actors(id)
    )
```

3. Payload Table:
Used to store data related to payloads or data associated with events, such as JSON data sent with the event. Includes fields like push ID, size, distinct size, actor ID, and a foreign key constraint on actor_id.
```bash
    CREATE TABLE IF NOT EXISTS payload (
        push_id text PRIMARY KEY,
        size text,
        distinct_size text,
        actor_id int,
        CONSTRAINT fk_actor FOREIGN KEY(actor_id) REFERENCES actors(id)
    )
```
4. Actors Table:
Used to store data related to actors or individuals who perform the events, such as ID, login, display login, gravatar ID, URL, avatar URL, and other relevant data.

```bash
    CREATE TABLE IF NOT EXISTS actors (
        id int PRIMARY KEY,
        login text,
        display_login text,
        gravatar_id text,
        url text,
        avatar_url text
```

### Step 3: snippet for ETL to import JSON to database
Python code snippet for ETL (Extract, Transform, Load) to import data from a JSON file into a PostgreSQL database

The necessary libraries for this task are as follows:
```bash
import glob
import json
import os
from typing import List

import psycopg2
```

1.Connecting to our database, which is Postgres, requires creating a database, a username, and a password for the connection. Here's an example of how to write the connection:

- db_name : You DB name
- user_name : You User name
- password_name : You Password name
```bash
def main():
    conn = psycopg2.connect(
        "host=127.0.0.1 dbname=<db_name> user=<user_name> password=<password_name>"
    )
    cur = conn.cursor()
    process(cur, conn, filepath="../data")
    conn.close()
```

2.Due to all our JSON files being present here, there must be a script to fetch them. An example of such a script would be as follows:
```bash
def get_files(filepath: str) -> List[str]:
    """
    Description: This function is responsible for listing the files in a directory
    """

    all_files = []
    for root, dirs, files in os.walk(filepath):
        files = glob.glob(os.path.join(root, "*.json"))
        for f in files:
            all_files.append(os.path.abspath(f))

    num_files = len(all_files)
    print(f"{num_files} files found in {filepath}")
    return all_files
```
3.To extract only the necessary data from all of these JSON files, the script would look something like this:
```bash
def process(cur, conn, filepath):
    # Get list of files from filepath
    all_files = get_files(filepath)

    for datafile in all_files:
        with open(datafile, "r") as f:
            data = json.loads(f.read())
            for each in data:
```

