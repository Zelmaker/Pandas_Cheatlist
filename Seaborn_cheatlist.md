![image](https://user-images.githubusercontent.com/113107446/203508608-817c9fdb-1064-4c4e-810a-f04de59b8f11.png)


# Line chart showing daily global streams of each song 
sns.lineplot(data=spotify_data)

# Set the width and height of the figure
plt.figure(figsize=(14,6))

# Add title
plt.title("Daily Global Streams of Popular Songs in 2017-2018")

# Line chart showing daily global streams of 'Shape of You'
sns.lineplot(data=spotify_data['Shape of You'], label="Shape of You")

# Line chart showing daily global streams of 'Despacito'
sns.lineplot(data=spotify_data['Despacito'], label="Despacito")

# Add label for horizontal axis
plt.xlabel("Date")
![image](https://user-images.githubusercontent.com/113107446/200006055-a8b08de1-f6d1-4592-8732-152a199cabd7.png)

# Bar chart
Say we'd like to create a bar chart showing the average arrival delay for Spirit Airlines (airline code: NK) flights, by month.

# Set the width and height of the figure
plt.figure(figsize=(10,6))

# Add title
plt.title("Average Arrival Delay for Spirit Airlines Flights, by Month")

# Bar chart showing average arrival delay for Spirit Airlines flights by month
sns.barplot(x=flight_data.index, y=flight_data['NK'])

# Add label for vertical axis
plt.ylabel("Arrival delay (in minutes)")

![image](https://user-images.githubusercontent.com/113107446/200015787-b1d3ccb3-0ef2-4ad8-881c-338120a384e8.png)

# Heatmap
We have one more plot type to learn about: heatmaps!

In the code cell below, we create a heatmap to quickly visualize patterns in flight_data. Each cell is color-coded according to its corresponding value.

# Set the width and height of the figure
plt.figure(figsize=(14,7))

# Add title
plt.title("Average Arrival Delay for Each Airline, by Month")

# Heatmap showing average arrival delay for each airline by month
sns.heatmap(data=flight_data, annot=True)

# Add label for horizontal axis
plt.xlabel("Airline")

![image](https://user-images.githubusercontent.com/113107446/200016181-5cb3ca3f-f8b3-4cc8-8e11-82ef63f8dada.png)

# Scatter plots
To create a simple scatter plot, we use the sns.scatterplot command and specify the values for:

the horizontal x-axis (x=insurance_data['bmi']), and
the vertical y-axis (y=insurance_data['charges']).
sns.scatterplot(x=insurance_data['bmi'], y=insurance_data['charges'])
![image](https://user-images.githubusercontent.com/113107446/200022260-dfb2e9f1-78fd-4a5f-aef8-6372039331fc.png)

The scatterplot above suggests that body mass index (BMI) and insurance charges are positively correlated, where customers with higher BMI typically also tend to pay more in insurance costs. (This pattern makes sense, since high BMI is typically associated with higher risk of chronic disease.)

To double-check the strength of this relationship, you might like to add a regression line, or the line that best fits the data. We do this by changing the command to sns.regplot.

sns.regplot(x=insurance_data['bmi'], y=insurance_data['charges'])

![image](https://user-images.githubusercontent.com/113107446/200023143-44b64d7e-aabb-474d-87be-3b491196f9c8.png)

# Color-coded scatter plots
We can use scatter plots to display the relationships between (not two, but...) three variables! One way of doing this is by color-coding the points.

For instance, to understand how smoking affects the relationship between BMI and insurance costs, we can color-code the points by 'smoker', and plot the other two columns ('bmi', 'charges') on the axes.

sns.scatterplot(x=insurance_data['bmi'], y=insurance_data['charges'], hue=insurance_data['smoker'])

![image](https://user-images.githubusercontent.com/113107446/200023470-2ef8ba67-d6b6-4c73-a389-9d93f21a0ba4.png)

This scatter plot shows that while nonsmokers to tend to pay slightly more with increasing BMI, smokers pay MUCH more.

To further emphasize this fact, we can use the sns.lmplot command to add two regression lines, corresponding to smokers and nonsmokers. (You'll notice that the regression line for smokers has a much steeper slope, relative to the line for nonsmokers!)

sns.lmplot(x="bmi", y="charges", hue="smoker", data=insurance_data)

![image](https://user-images.githubusercontent.com/113107446/200023640-ff5373cb-8637-4ed1-aeb7-d348ee304c73.png)

Finally, there's one more plot that you'll learn about, that might look slightly different from how you're used to seeing scatter plots. Usually, we use scatter plots to highlight the relationship between two continuous variables (like "bmi" and "charges"). However, we can adapt the design of the scatter plot to feature a categorical variable (like "smoker") on one of the main axes. We'll refer to this plot type as a categorical scatter plot, and we build it with the sns.swarmplot command.

sns.swarmplot(x=insurance_data['smoker'],
              y=insurance_data['charges'])
              
# Histograms
Say we would like to create a histogram to see how petal length varies in iris flowers. We can do this with the sns.histplot command.

# Histogram 
sns.histplot(iris_data['Petal Length (cm)'])
![image](https://user-images.githubusercontent.com/113107446/200062135-38fa8aaa-6095-4687-a492-8969bed7d8bc.png)

sns.histplot(data=cancer_data, x='Area (mean)', hue='Diagnosis')

# Density plots
The next type of plot is a kernel density estimate (KDE) plot. In case you're not familiar with KDE plots, you can think of it as a smoothed histogram.

To make a KDE plot, we use the sns.kdeplot command. Setting shade=True colors the area below the curve (and data= chooses the column we would like to plot).

# KDE plot 
sns.kdeplot(data=iris_data['Petal Length (cm)'], shade=True)
![image](https://user-images.githubusercontent.com/113107446/200062781-7f8a301c-754f-4c49-a1b4-6ef67d265a62.png)

# 2D KDE plots
We're not restricted to a single column when creating a KDE plot. We can create a two-dimensional (2D) KDE plot with the sns.jointplot command.

In the plot below, the color-coding shows us how likely we are to see different combinations of sepal width and petal length, where darker parts of the figure are more likely.

# 2D KDE plot
sns.jointplot(x=iris_data['Petal Length (cm)'], y=iris_data['Sepal Width (cm)'], kind="kde")
![image](https://user-images.githubusercontent.com/113107446/200063205-5c56c4bc-2613-44c5-b7f2-1c40c6944938.png)
Note that in addition to the 2D KDE plot in the center,

the curve at the top of the figure is a KDE plot for the data on the x-axis (in this case, iris_data['Petal Length (cm)']), and
the curve on the right of the figure is a KDE plot for the data on the y-axis (in this case, iris_data['Sepal Width (cm)']).

# Color-coded plots
For the next part of the tutorial, we'll create plots to understand differences between the species.

We can create three different histograms (one for each species) of petal length by using the sns.histplot command (as above).

data= provides the name of the variable that we used to read in the data
x= sets the name of column with the data we want to plot
hue= sets the column we'll use to split the data into different histograms
# Histograms for each species
sns.histplot(data=iris_data, x='Petal Length (cm)', hue='Species')

# Add title
plt.title("Histogram of Petal Lengths, by Species")
![image](https://user-images.githubusercontent.com/113107446/200063718-0d986c3a-0d1a-4d2c-82ef-fff794b0a85a.png)

We can also create a KDE plot for each species by using sns.kdeplot (as above). The functionality for data, x, and hue are identical to when we used sns.histplot above. Additionally, we set shade=True to color the area below each curve.

# KDE plots for each species
sns.kdeplot(data=iris_data, x='Petal Length (cm)', hue='Species', shade=True)

# Add title
plt.title("Distribution of Petal Lengths, by Species")
![image](https://user-images.githubusercontent.com/113107446/200063928-90d413cf-0e13-4f7f-ae73-865687a84c31.png)

# Change the style of the figure
sns.set_style("dark")

# Line chart 
plt.figure(figsize=(12,6))
sns.lineplot(data=spotify_data)

Now, try out different themes by amending the first line of code and running the code cell again. Remember the list of available themes:

"darkgrid"
"whitegrid"
"dark"
"white"
"ticks"
