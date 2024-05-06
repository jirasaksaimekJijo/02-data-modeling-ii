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
### Step 1: set up docker and run server Cassandra

1. Run the environment so that the tools can be used to perform tasks.
   But because we have a file .yml

   The .yml file will configure the environment for Cassandra, making it ready for use.

run in command line
```bash
docker compose up
```
   When run, the tool we have configured in the .yml file will execute.
   To enable and proceed with the next steps.
   
### Step 2: process get data from json to dataframe

- It defines three lists create_table_queries, drop_table_queries, each containing queries to create and drop tables respectively. These queries are assumed to be defined elsewhere in the code as string variables table_create_events, table_create_actor, table_create_repo, table_drop_events, table_drop_actor, table_drop_repo.
- It defines two functions:
drop_tables(session): Iterates over the drop_table_queries list and attempts to execute each query to drop the corresponding tables.
create_tables(session): Iterates over the create_table_queries list and attempts to execute each query to create the corresponding tables.
- In the main() function:
It connects to a Cassandra cluster and creates a keyspace named "github_events" if it doesn't exist already.
Sets the keyspace to "github_events".
Drops existing tables and creates new ones using the defined functions drop_tables(session) and create_tables(session).
Calls a function process(session, filepath="../data"), which is assumed to handle some data processing (not defined in the provided code).
Executes a SELECT query to retrieve data from the Cassandra database, filtering by event type and a specific time range.
Prints the retrieved rows to the standard output.

```bash
def get_files(filepath: str) -> List[str]:
    all_files = []
    for root, dirs, files in os.walk(filepath):
        files = glob.glob(os.path.join(root, "*.json"))
        for f in files:
            all_files.append(os.path.abspath(f))
    num_files = len(all_files)
    print(f"{num_files} files found in {filepath}")
    return all_files

def process(session, filepath):
    all_files = get_files(filepath)

    for datafile in all_files:
        with open(datafile, "r") as f:
            data = json.loads(f.read())
            for each in data:
                print(each["id"], each["type"], each["actor"]["login"])

                insert_statement = f"""INSERT INTO events (
                event_id,type,public,created_at,actor_id,actor_login,actor_display_login,actor_url,repo_id,repo_name,repo_url)
                VALUES ('{each["id"]}', '{each["type"]}', {each["public"]}, '{each["created_at"]}',
                '{each["actor"]["id"]}', '{each["actor"]["login"]}', '{each["actor"]["display_login"]}', '{each["actor"]["url"]}',
                '{each["repo"]["id"]}', '{each["repo"]["name"]}', '{each["repo"]["url"]}')
                """
                session.execute(insert_statement)

                insert_statement = f"""INSERT INTO actor (actor_id,actor_login) VALUES ('{each["actor"]["id"]}','{each["actor"]["login"]}')"""
                session.execute(insert_statement)

                insert_statement = f"""INSERT INTO repo (repo_id,repo_name) VALUES ('{each["repo"]["id"]}','{each["repo"]["name"]}')"""
                session.execute(insert_statement)
```
### Step 3: Creating tables and loading data into Cassandra according to the created tables

It will create an events table with columns id, type, pulib and PK id and type

#### DROP TABLE statements:
- table_drop_events: Drops the table named "events" if it exists.
- table_drop_actor: Drops the table named "actor" if it exists.
- table_drop_repo: Drops the table named "repo" if it exists.

#### CREATE TABLE statements:
- table_create_events: Creates a table named "events" with columns for event details such as event ID, type, whether it's public, created timestamp, actor details (ID, login, display login, URL), and repository details (ID, name, URL). It also defines the primary key constraint on the event ID and type.
- table_create_actor: Creates a table named "actor" with columns for actor details such as actor ID and actor login. It defines a composite primary key constraint on actor ID and actor login.
- table_create_repo: Creates a table named "repo" with columns for repository details such as repository ID and repository name. It also defines a composite primary key constraint on repo ID and repo name.

```bash
table_drop_events = "DROP TABLE events"
table_drop_actor = "DROP TABLE actor"
table_drop_repo = "DROP TABLE repo"

table_create_events = """
    CREATE TABLE IF NOT EXISTS events
    (   event_id varchar,type varchar,public boolean,created_at timestamp,actor_id varchar,actor_login varchar,
        actor_display_login varchar,actor_url varchar,repo_id varchar,repo_name varchar,repo_url varchar,
        PRIMARY KEY (event_id,type))
"""

table_create_actor = """
    CREATE TABLE IF NOT EXISTS actor
    (   actor_id varchar,actor_login varchar,
        PRIMARY KEY (actor_id,actor_login))
"""
table_create_repo = """
    CREATE TABLE IF NOT EXISTS repo
    (   repo_id varchar,repo_name varchar,
        PRIMARY KEY (repo_id,repo_name))
"""
```
- It defines a list of table creation queries (create_table_queries) and a list of table drop queries (drop_table_queries). These queries are used later to create and drop tables in the Cassandra keyspace.
- It defines two functions:
drop_tables(session): Iterates over the drop_table_queries list and attempts to execute each query to drop existing tables.
create_tables(session): Iterates over the create_table_queries list and attempts to execute each query to create tables in the keyspace.
- It defines a main() function where the script execution starts:
- It connects to the Cassandra cluster running on localhost.
- It attempts to create a keyspace named "github_events" if it doesn't already exist.
- It sets the keyspace to "github_events".
- It calls the drop_tables(session) function to drop any existing tables.
- It calls the create_tables(session) function to create new tables.
- It likely has a call to a function process(session, filepath="../data"), which is not defined in the provided code snippet. This function probably processes data from files located in the "../data" directory and inserts them into the Cassandra tables.
- It executes a select query to retrieve GitHub events data within a specific time range and of a certain type ("IssuesEvent"). This query is executed against the "events" table in the "github_events" keyspace.
- It iterates over the query results and prints each row to the standard output.
```bash
create_table_queries = [
    table_create_events,
    table_create_actor,
    table_create_repo
]
drop_table_queries = [
    table_drop_events,
    table_drop_actor,
    table_drop_repo
]

def drop_tables(session):
    for query in drop_table_queries:
        try:
            rows = session.execute(query)
        except Exception as e:
            print(e)

def create_tables(session):
    for query in create_table_queries:
        try:
            session.execute(query)
        except Exception as e:
            print(e)

def main():
    cluster = Cluster(['127.0.0.1'])
    session = cluster.connect()

    try:
        session.execute(
            """CREATE KEYSPACE IF NOT EXISTS github_events
            WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 1 }
            """
        )
    except Exception as e:
        print(e)

    try:
        session.set_keyspace("github_events")
    except Exception as e:
        print(e)

    drop_tables(session)
    create_tables(session)
    process(session, filepath="../data")
    #insert_sample_data(session)

    query = """SELECT event_id, actor_id, created_at FROM events
    WHERE created_at >= '2022-08-17T15:51:04Z' AND created_at <= '2022-08-17T15:51:05Z' AND type = 'IssuesEvent'
    ALLOW FILTERING;
    """

    try:
        rows = session.execute(query)
    except Exception as e:
        print(e)

    for row in rows:
        print(row)
```
data save to Cassandra is img 
![image](https://github.com/jirasaksaimekJijo/Week2-Data-Modeling-with-Cassandra-NoSQL/assets/116647943/25d8ccd8-31cb-44ff-bfa7-0c730f56391c)
