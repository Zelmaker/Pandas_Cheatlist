from google.cloud import bigquery
### Create a "Client" object
client = bigquery.Client()
### Construct a reference to the "hacker_news" dataset
dataset_ref = client.dataset("hacker_news", project="bigquery-public-data")

### API request - fetch the dataset
dataset = client.get_dataset(dataset_ref)

### List all the tables in the "hacker_news" dataset
tables = list(client.list_tables(dataset))

### Print names of all tables in the dataset (there are four!)
for table in tables:  
    print(table.table_id)
    
### Construct a reference to the "full" table
table_ref = dataset_ref.table("full")

### API request - fetch the table
table = client.get_table(table_ref)

![image](https://user-images.githubusercontent.com/113107446/202863886-f820df5d-a258-4b91-a7d1-763b230a22bc.png)

### Print information on all the columns in the "full" table in the "hacker_news" dataset

table.schema

Each SchemaField tells us about a specific column (which we also refer to as a field). In order, the information is:

The name of the column
The field type (or datatype) in the column
The mode of the column ('NULLABLE' means that a column allows NULL values, and is the default)
A description of the data in that column

### Preview the first five lines of the "full" table
client.list_rows(table, max_results=5).to_dataframe()

### Preview the first five entries in the "by" column of the "full" table
client.list_rows(table, selected_fields=table.schema[:1], max_results=5).to_dataframe()


# Example:

from google.cloud import bigquery

### Create a "Client" object
client = bigquery.Client()

### Construct a reference to the "openaq" dataset
dataset_ref = client.dataset("openaq", project="bigquery-public-data")

### API request - fetch the dataset
dataset = client.get_dataset(dataset_ref)

### List all the tables in the "openaq" dataset
tables = list(client.list_tables(dataset))

### Print names of all tables in the dataset (there's only one!)
for table in tables:  
    print(table.table_id)
    
### Construct a reference to the "global_air_quality" table
table_ref = dataset_ref.table("global_air_quality")

### API request - fetch the table
table = client.get_table(table_ref)

### Preview the first five lines of the "global_air_quality" table
client.list_rows(table, max_results=5).to_dataframe()

Say we want to select all the values from the city column that are in rows where the country column is 'US' (for "United States").

### Query to select all the items from the "city" column where the "country" column is 'US'
query = """
        SELECT city
        FROM `bigquery-public-data.openaq.global_air_quality`
        WHERE country = 'US'
        """

# Submitting the query to the dataset
We're ready to use this query to get information from the OpenAQ dataset. As in the previous tutorial, the first step is to create a Client object.

### Create a "Client" object
client = bigquery.Client()
Using Kaggle's public dataset BigQuery integration.
We begin by setting up the query with the query() method. We run the method with the default parameters, but this method also allows us to specify more complicated settings that you can read about in the documentation. We'll revisit this later.

### Set up the query
query_job = client.query(query)
Next, we run the query and convert the results to a pandas DataFrame.

### API request - run the query, and return a pandas DataFrame
us_cities = query_job.to_dataframe()

Now we've got a pandas DataFrame called us_cities, which we can use like any other DataFrame.

### What five cities have the most measurements?
us_cities.city.value_counts().head()

# To begin,you can estimate the size of any query before running it.
Here is an example using the (very large!) Hacker News dataset. To see how much data a query will scan, we create a QueryJobConfig object and set the dry_run parameter to True.

### Query to get the score column from every row where the type column has value "job"
query = """
        SELECT score, title
        FROM `bigquery-public-data.hacker_news.full`
        WHERE type = "job" 
        """

### Create a QueryJobConfig object to estimate size of query without running it
dry_run_config = bigquery.QueryJobConfig(dry_run=True)

### API request - dry run query to estimate costs
dry_run_query_job = client.query(query, job_config=dry_run_config)

print("This query will process {} bytes.".format(dry_run_query_job.total_bytes_processed))
This query will process 494391991 bytes.

# You can also specify a parameter when running the query to limit how much data you are willing to scan. 
Here's an example with a low limit.

### Only run the query if it's less than 1 MB
ONE_MB = 1000*1000
safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=ONE_MB)

### Set up the query (will only run if it's less than 1 MB)
safe_query_job = client.query(query, job_config=safe_config)

### API request - try to run the query, and return a pandas DataFrame
safe_query_job.to_dataframe()

# run if less than 1 gb
### Only run the query if it's less than 1 GB
ONE_GB = 1000*1000*1000
safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=ONE_GB)

### Set up the query (will only run if it's less than 1 GB)
safe_query_job = client.query(query, job_config=safe_config)

### API request - try to run the query, and return a pandas DataFrame
job_post_scores = safe_query_job.to_dataframe()

### Print average score for job posts
job_post_scores.score.mean()
