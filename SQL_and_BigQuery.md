## usefull
To get each country only once you can run `SELECT DISTINCT country ...`. The DISTINCT keyword ensures each column shows up once, which you'll want in some cases.

![image](https://user-images.githubusercontent.com/113107446/203158596-285bd465-b6e3-46d8-84fb-0a5cc155b32b.png)

![image](https://user-images.githubusercontent.com/113107446/203158737-e00637de-050b-49be-8f80-a24846b3c074.png)

![image](https://user-images.githubusercontent.com/113107446/203158540-c05e49b1-16b6-49b6-a567-76535aa8cba6.png)

![image](https://user-images.githubusercontent.com/113107446/203162805-747790f0-74de-4c36-9b3f-59e466aed2a7.png)

![image](https://user-images.githubusercontent.com/113107446/203162828-d4f189a4-c6be-4b63-ba99-005d8826fb21.png)

![image](https://user-images.githubusercontent.com/113107446/203165752-382ede6f-22a6-4545-a47e-bc62eebb0076.png)

### Query to determine the number of files per license, sorted by number of files
query = """
        SELECT L.license, COUNT(1) AS number_of_files
        FROM `bigquery-public-data.github_repos.sample_files` AS sf
        INNER JOIN `bigquery-public-data.github_repos.licenses` AS L 
            ON sf.repo_name = L.repo_name
        GROUP BY L.license
        ORDER BY number_of_files DESC
        """

![image](https://user-images.githubusercontent.com/113107446/203166194-738e91fd-d5d7-4faa-ade7-529662e1b73e.png)

We'll begin with the JOIN (highlighted in blue above). This specifies the sources of data and how to join them. We use ON to specify that we combine the tables by matching the values in the repo_name columns in the tables.

Next, we'll talk about SELECT and GROUP BY (highlighted in yellow). The GROUP BY breaks the data into a different group for each license, before we COUNT the number of rows in the sample_files table that corresponds to each license. (Remember that you can count the number of rows with COUNT(1).)

Finally, the ORDER BY (highlighted in purple) sorts the results so that licenses with more files appear first.

It was a big query, but it gave us a nice table summarizing how many files have been committed under each license:

# Set up the query (cancel the query if it would use too much of 
# your quota, with the limit set to 10 GB)
safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=10**10)
query_job = client.query(query, job_config=safe_config)

# API request - run the query, and convert the results to a pandas DataFrame
file_count_by_license = query_job.to_dataframe()


### Query to find out the number of accidents for each day of the week
query = """
        SELECT COUNT(consecutive_number) AS num_accidents, 
               EXTRACT(DAYOFWEEK FROM timestamp_of_crash) AS day_of_week
        FROM `bigquery-public-data.nhtsa_traffic_fatalities.accident_2015`
        GROUP BY day_of_week
        ORDER BY num_accidents DESC


### Improved version of earlier query, now with aliasing & improved readability
query_improved = """
                 SELECT parent, COUNT(1) AS NumPosts
                 FROM `bigquery-public-data.hacker_news.comments`
                 GROUP BY parent
                 HAVING COUNT(1) > 10
                 """

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
