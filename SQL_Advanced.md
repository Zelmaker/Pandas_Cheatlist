![image](https://user-images.githubusercontent.com/113107446/203639518-dc3f1d00-f478-4a96-8f01-1a8853a8a687.png)

![image](https://user-images.githubusercontent.com/113107446/203639559-7ec76899-36ca-4b9e-9c58-906eaf62faf1.png)

![image](https://user-images.githubusercontent.com/113107446/203639718-38227dce-6ed2-45a4-8da0-7fd84325f3ed.png)

![image](https://i.imgur.com/G6buS7P.png)

Replacing INNER JOIN in the query above with LEFT JOIN returns all rows where the two tables have matching entries, along with all of the rows in the left table (whether there is a match or not).

If we instead use a RIGHT JOIN, we get the matching rows, along with all rows in the right table (whether there is a match or not).

Finally, a FULL JOIN returns all rows from both tables. Note that in general, any row that does not have a match in both tables will have NULL entries for the missing values. You can see this in the image below.

![image](https://user-images.githubusercontent.com/113107446/203639764-155715a1-be01-44b6-b8cf-64964696c309.png)

# UNIONs
As you've seen, JOINs horizontally combine results from different tables. If you instead would like to vertically concatenate columns, you can do so with a UNION. The example query below combines the Age columns from both tables.

![image](https://user-images.githubusercontent.com/113107446/203640052-efe712f2-a7a0-423c-b7be-96b8b48b795b.png)

Note that with a UNION, the data types of both columns must be the same, but the column names can be different. (So, for instance, we cannot take the UNION of the Age column from the owners table and the Pet_Name column from the pets table.)

We use UNION ALL to include duplicate values - you'll notice that 9 appears in both the owners table and the pets table, and shows up twice in the concatenated results. If you'd like to drop duplicate values, you need only change UNION ALL in the query to UNION DISTINCT.

# Example
The query below pulls information from the stories and comments tables to create a table showing all stories posted on January 1, 2012, along with the corresponding number of comments. We use a LEFT JOIN so that the results include stories that didn't receive any comments.

# Query to select all stories posted on January 1, 2012, with number of comments
join_query = """
             WITH c AS
             (
             SELECT parent, COUNT(*) as num_comments
             FROM `bigquery-public-data.hacker_news.comments` 
             GROUP BY parent
             )
             SELECT s.id as story_id, s.by, s.title, c.num_comments
             FROM `bigquery-public-data.hacker_news.stories` AS s
             LEFT JOIN c
             ON s.id = c.parent
             WHERE EXTRACT(DATE FROM s.time_ts) = '2012-01-01'
             ORDER BY c.num_comments DESC
             """

#### Run the query, and return a pandas DataFrame
join_result = client.query(join_query).result().to_dataframe()
join_result.head()

Next, we write a query to select all usernames corresponding to users who wrote stories or comments on January 1, 2014. We use UNION DISTINCT (instead of UNION ALL) to ensure that each user appears in the table at most once.

#### Query to select all users who posted stories or comments on January 1, 2014
union_query = """
              SELECT c.by
              FROM `bigquery-public-data.hacker_news.comments` AS c
              WHERE EXTRACT(DATE FROM c.time_ts) = '2014-01-01'
              UNION DISTINCT
              SELECT s.by
              FROM `bigquery-public-data.hacker_news.stories` AS s
              WHERE EXTRACT(DATE FROM s.time_ts) = '2014-01-01'
              """

#### Run the query, and return a pandas DataFrame
union_result = client.query(union_query).result().to_dataframe()
union_result.head()
