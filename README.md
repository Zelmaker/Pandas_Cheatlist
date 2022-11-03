# Pandas_Cheatlist_Шпаргалка по pandas(пандас) @Zelmaker
## Creating, Reading and Writing
### DateFrame Creating
import pandas as pd

fruits = pd.DataFrame([[30, 21]], columns=['Apples', 'Bananas'])

![Ax3pp2A](https://user-images.githubusercontent.com/113107446/199091676-6fe68090-c728-4845-84aa-3ed84f89d081.png)

fruit_sales = pd.DataFrame([[35, 21],[41,34]], columns=['Apples', 'Bananas'], index =['2017 Sales','2018 Sales'])

![CHPn7ZF](https://user-images.githubusercontent.com/113107446/199091402-24cf62cc-d672-4251-839b-1779de2487ac.png)

### Series

Create a variable ingredients with a Series that looks like:

Flour     4 cups
Milk       1 cup
Eggs     2 large
Spam       1 can
Name: Dinner, dtype: object

##### ingredients = pd.Series(['4 cups','1 cup','2 large','1 can'], index=['Flour', 'Milk', 'Eggs','Spam'], name='Dinner')

### Reading CSV
Let's now set aside our toy datasets and see what a real dataset looks like when we read it into a DataFrame. We'll use the pd.read_csv() function to read the data into a DataFrame. This goes thusly:
##### wine_reviews = pd.read_csv("../input/wine-reviews/winemag-data-130k-v2.csv")

To make pandas use that column for the index (instead of creating a new one from scratch), we can specify an index_col.
##### wine_reviews = pd.read_csv("../input/wine-reviews/winemag-data-130k-v2.csv", index_col=0)

We can use the shape attribute to check how large the resulting DataFrame is:
##### wine_reviews.shape

head() command, which grabs the first five rows:
##### wine_reviews.head()

### Write CSV
save this DataFrame to disk as a csv file with the name cows_and_goats.csv.
##### animals.to_csv("cows_and_goats.csv")

## Indexing, Selecting & Assigning
### Selecting
Hence to access the country property of reviews we can use:
##### reviews.country

we can access its values using the indexing ([]) operator. We can do the same with columns in a DataFrame:
##### reviews['country']

to drill down to a single specific value, we need only use the indexing operator [] once more:
##### reviews['country'][0]

### Indexing
#### Iloc
Pandas indexing works in one of two paradigms. The first is index-based selection: selecting data based on its numerical position in the data. iloc follows this paradigm.
To select the first row of data in a DataFrame, we may use the following:
##### reviews.iloc[0]

Both loc and iloc are row-first, column-second. This is the opposite of what we do in native Python, which is column-first, row-second.
This means that it's marginally easier to retrieve rows, and marginally harder to get retrieve columns. To get a column with iloc, we can do the following:
##### reviews.iloc[:, 0]

On its own, the : operator, which also comes from native Python, means "everything". When combined with other selectors, however, it can be used to indicate a range of values. For example, to select the country column from just the first, second, and third row, we would do:
##### reviews.iloc[:3, 0]   #:3 index of column
first_descriptions = reviews.description.iloc[:10]
Note that many other options will return a valid result, such as desc.head(10) and reviews.loc[:9, "description"].

Select the records with index labels 1, 2, 3, 5, and 8, assigning the result to the variable sample_reviews.
##### sample_reviews = reviews.iloc[[1,2,3,5,8],:]

Finally, it's worth knowing that negative numbers can be used in selection. This will start counting forwards from the end of the values. So for example here are the last five elements of the dataset.
##### reviews.iloc[-5:]

#### loc
For example, to get the first entry in reviews, we would now do the following:
##### reviews.loc[0, 'country']

##### reviews.loc[:, ['taster_name', 'taster_twitter_handle', 'points']]
>iloc uses the Python stdlib indexing scheme, where the first element of the range is included and the last one excluded. 
>loc, meanwhile, indexes inclusively.
>This is particularly confusing when the DataFrame index is a simple numerical list, e.g. 0,...,1000. 
>In this case df.iloc[0:1000] will return 1000 entries, while df.loc[0:1000] return 1001 of them! 
>To get 1000 elements using loc, you will need to go one lower and ask for df.iloc[0:999].
cols = ['country', 'variety']
df = reviews.loc[:99, cols]
or
cols_idx = [0, 11]
df = reviews.iloc[:100, cols_idx]

#### Choosing between loc and iloc¶
When choosing or transitioning between loc and iloc, there is one "gotcha" worth keeping in mind, which is that the two methods use slightly different indexing schemes.

iloc uses the Python stdlib indexing scheme, where the first element of the range is included and the last one excluded. So 0:10 will select entries 0,...,9. loc, meanwhile, indexes inclusively. So 0:10 will select entries 0,...,10.

Why the change? Remember that loc can index any stdlib type: strings, for example. If we have a DataFrame with index values Apples, ..., Potatoes, ..., and we want to select "all the alphabetical fruit choices between Apples and Potatoes", then it's a lot more convenient to index df.loc['Apples':'Potatoes'] than it is to index something like df.loc['Apples', 'Potatoet'] (t coming after s in the alphabet).

This is particularly confusing when the DataFrame index is a simple numerical list, e.g. 0,...,1000. In this case df.iloc[0:1000] will return 1000 entries, while df.loc[0:1000] return 1001 of them! To get 1000 elements using loc, you will need to go one lower and ask for df.loc[0:999].

Otherwise, the semantics of using loc are the same as those for iloc.

#### Manipulating the index¶
Label-based selection derives its power from the labels in the index. Critically, the index we use is not immutable. We can manipulate the index in any way we see fit.
The set_index() method can be used to do the job. Here is what happens when we set_index to the title field:
##### reviews.set_index("title")
This is useful if you can come up with an index for the dataset which is better than the current one.

### Conditional selection
For example, suppose that we're interested specifically in better-than-average wines produced in Italy.
We can start by checking if each wine is Italian or not:
reviews.country == 'Italy'
This operation produced a Series of True/False booleans based on the country of each record. This result can then be used inside of loc to select the relevant data:
##### reviews.loc[reviews.country == 'Italy']

We also wanted to know which ones are better than average. Wines are reviewed on a 80-to-100 point scale, so this could mean wines that accrued at least 90 points.
We can use the ampersand (&) to bring the two questions together:
##### reviews.loc[(reviews.country == 'Italy') & (reviews.points >= 90)]

Suppose we'll buy any wine that's made in Italy or which is rated above average. For this we use a pipe (|):
##### reviews.loc[(reviews.country == 'Italy') | (reviews.points >= 90)]

Pandas comes with a few built-in conditional selectors, two of which we will highlight here.

The first is isin. isin is lets you select data whose value "is in" a list of values. For example, here's how we can use it to select wines only from Italy or France:
##### reviews.loc[reviews.country.isin(['Italy', 'France'])]

The second is isnull (and its companion notnull). These methods let you highlight values which are (or are not) empty (NaN). For example, to filter out wines lacking a price tag in the dataset, here's what we would do:
##### reviews.loc[reviews.price.notnull()]

### Assigning data
Going the other way, assigning data to a DataFrame is easy. You can assign either a constant value:
##### reviews['critic'] = 'everyone'
Or with an iterable of values:
##### reviews['index_backwards'] = range(len(reviews), 0, -1)

## Summary Functions and Maps
### Summary functions
Pandas provides many simple "summary functions" (not an official name) which restructure the data in some useful way. For example, consider the describe() method:
##### reviews.points.describe()
For example, to see the mean of the points allotted (e.g. how well an averagely rated wine does), we can use the mean() function:
##### reviews.points.mean()
To see a list of unique values we can use the unique() function:
##### reviews.taster_name.unique()
To see a list of unique values and how often they occur in the dataset, we can use the value_counts() method:
##### reviews.taster_name.value_counts()
##### median_points = reviews.points.median()
### Maps
A map is a term, borrowed from mathematics, for a function that takes one set of values and "maps" them to another set of values. In data science we often have a need for creating new representations from existing data, or for transforming data from the format it is in now to the format that we want it to be in later. Maps are what handle this work, making them extremely important for getting your work done!
There are two mapping methods that you will use often.
map() is the first, and slightly simpler one. For example, suppose that we wanted to remean the scores the wines received to 0. We can do this as follows:
##### review_points_mean = reviews.points.mean()
##### reviews.points.map(lambda p: p - review_points_mean)
The function you pass to map() should expect a single value from the Series (a point value, in the above example), and return a transformed version of that value. map() returns a new Series where all the values have been transformed by your function.

apply() is the equivalent method if we want to transform a whole DataFrame by calling a custom method on each row.
##### def remean_points(row):
    row.points = row.points - review_points_mean
    return row
    
reviews.apply(remean_points, axis='columns')

>If we had called reviews.apply() with axis='index', then instead of passing a function to transform each row, we would need to give a function to transform each column.
>Note that map() and apply() return new, transformed Series and DataFrames, respectively. They don't modify the original data they're called on. If we look at the first row of reviews, we can see that it still has its original points value.

##### review_points_mean = reviews.points.mean()
##### reviews.points - review_points_mean
##### reviews.country + " - " + reviews.region_1

Which wine is the "best bargain"? Create a variable bargain_wine with the title of the wine with the highest points-to-price ratio in the dataset.
##### bargain_idx = (reviews.points / reviews.price).idxmax()
##### bargain_wine = reviews.loc[bargain_idx, 'title']

There are only so many words you can use when describing a bottle of wine. Is a wine more likely to be "tropical" or "fruity"? Create a Series descriptor_counts counting how many times each of these two words appears in the description column in the dataset. (For simplicity, let's ignore the capitalized versions of these words.)
##### n_trop = reviews.description.map(lambda desc: "tropical" in desc).sum()
##### n_fruity = reviews.description.map(lambda desc: "fruity" in desc).sum()
descriptor_counts = pd.Series([n_trop, n_fruity], index=['tropical', 'fruity'])

We'd like to host these wine reviews on our website, but a rating system ranging from 80 to 100 points is too hard to understand - we'd like to translate them into simple star ratings. A score of 95 or higher counts as 3 stars, a score of at least 85 but less than 95 is 2 stars. Any other score is 1 star.
Also, the Canadian Vintners Association bought a lot of ads on the site, so any wines from Canada should automatically get 3 stars, regardless of points.
Create a series star_ratings with the number of stars corresponding to each review in the dataset.
def stars(row):
    if row.country == 'Canada':
        return 3
    elif row.points >= 95:
        return 3
    elif row.points >= 85:
        return 2
    else:
        return 1

star_ratings = reviews.apply(stars, axis='columns')

## Grouping and Sorting
Introduction
Maps allow us to transform data in a DataFrame or Series one value at a time for an entire column. However, often we want to group our data, and then do something specific to the group the data is in.

As you'll learn, we do this with the groupby() operation. We'll also cover some additional topics, such as more complex ways to index your DataFrames, along with how to sort your data.

To start the exercise for this topic, please click here.


### Groupwise analysis
One function we've been using heavily thus far is the value_counts() function. We can replicate what value_counts() does by doing the following:
##### reviews.groupby('points').points.count()
or 
##### reviews_written = reviews.groupby('taster_twitter_handle').size()
groupby() created a group of reviews which allotted the same point values to the given wines. Then, for each of these groups, we grabbed the points() column and counted how many times it appeared. value_counts() is just a shortcut to this groupby() operation.
We can use any of the summary functions we've used before with this data. For example, to get the cheapest wine in each point value category, we can do the following:
##### reviews.groupby('points').price.min()
You can think of each group we generate as being a slice of our DataFrame containing only data with values that match. This DataFrame is accessible to us directly using the apply() method, and we can then manipulate the data in any way we see fit. For example, here's one way of selecting the name of the first wine reviewed from each winery in the dataset:
##### reviews.groupby('winery').apply(lambda df: df.title.iloc[0])
Another groupby() method worth mentioning is agg(), which lets you run a bunch of different functions on your DataFrame simultaneously. For example, we can generate a simple statistical summary of the dataset as follows:
##### reviews.groupby(['country']).price.agg([len, min, max])

### Sorting
Looking again at countries_reviewed we can see that grouping returns data in index order, not in value order. That is to say, when outputting the result of a groupby, the order of the rows is dependent on the values in the index, not in the data.
To get data in the order want it in we can sort it ourselves. The sort_values() method is handy for this.
##### countries_reviewed = countries_reviewed.reset_index()
##### countries_reviewed.sort_values(by='len')
sort_values() defaults to an ascending sort, where the lowest values go first. However, most of the time we want a descending sort, where the higher numbers go first. That goes thusly:
##### countries_reviewed.sort_values(by='len', ascending=False)
To sort by index values, use the companion method sort_index(). This method has the same arguments and default order:
##### countries_reviewed.sort_index()
## Data Types and Missing Values



What are the most common wine-producing regions? Create a Series counting the number of times each value occurs in the region_1 field. This field is often missing data, so replace missing values with Unknown. Sort in descending order. Your output should look something like this:
##### reviews_per_region = reviews.region_1.fillna('Unknown').value_counts().sort_values(ascending=False)


## Renaming and Combining
### Renaming
The first function we'll introduce here is rename(), which lets you change index names and/or column names. For example, to change the points column in our dataset to score, we would do:
##### reviews.rename(columns={'points': 'score'})
Here is an example using it to rename some elements of the index.
##### reviews.rename(index={0: 'firstEntry', 1: 'secondEntry'})
Both the row index and the column index can have their own name attribute. The complimentary rename_axis() method may be used to change these names. For example:
##### reviews.rename_axis("wines", axis='rows').rename_axis("fields", axis='columns')
### Combining
When performing operations on a dataset, we will sometimes need to combine different DataFrames and/or Series in non-trivial ways. Pandas has three core methods for doing this. In order of increasing complexity, these are concat(), join(), and merge(). Most of what merge() can do can also be done more simply with join(), so we will omit it and focus on the first two functions here.

The simplest combining method is concat(). Given a list of elements, this function will smush those elements together along an axis.

This is useful when we have data in different DataFrame or Series objects but having the same fields (columns). One example: the YouTube Videos dataset, which splits the data up based on country of origin (e.g. Canada and the UK, in this example). If we want to study multiple countries simultaneously, we can use concat() to smush them together:
##### canadian_youtube = pd.read_csv("../input/youtube-new/CAvideos.csv")
##### british_youtube = pd.read_csv("../input/youtube-new/GBvideos.csv")
##### pd.concat([canadian_youtube, british_youtube])
The middlemost combiner in terms of complexity is join(). join() lets you combine different DataFrame objects which have an index in common. For example, to pull down videos that happened to be trending on the same day in both Canada and the UK, we could do the following:

##### left = canadian_youtube.set_index(['title', 'trending_date'])
##### right = british_youtube.set_index(['title', 'trending_date'])

##### left.join(right, lsuffix='_CAN', rsuffix='_UK')
The lsuffix and rsuffix parameters are necessary here because the data has the same column names in both British and Canadian datasets. If this wasn't true (because, say, we'd renamed them beforehand) we wouldn't need them.
