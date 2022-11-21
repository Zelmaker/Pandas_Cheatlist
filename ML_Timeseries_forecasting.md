# Timeseries-forecasting
    
from statsmodels.tsa.deterministic import CalendarFourier, DeterministicProcess

fourier = CalendarFourier(freq="A", order=10)  # 10 sin/cos pairs for "A"nnual seasonality

dp = DeterministicProcess(
    index=tunnel.index,
    constant=True,               # dummy feature for bias (y-intercept)
    order=1,                     # trend (order 1 means linear)
    seasonal=True,               # weekly seasonality (indicators)
    additional_terms=[fourier],  # annual seasonality (fourier)
    drop=True,                   # drop terms to avoid collinearity
)

X = dp.in_sample()  # create features for dates in tunnel.index
y = tunnel["NumVehicles"]

model = LinearRegression(fit_intercept=False)
_ = model.fit(X, y)

y_pred = pd.Series(model.predict(X), index=y.index)
X_fore = dp.out_of_sample(steps=90)
y_fore = pd.Series(model.predict(X_fore), index=X_fore.index)

ax = y.plot(color='0.25', style='.', title="Tunnel Traffic - Seasonal Forecast")
ax = y_pred.plot(ax=ax, label="Seasonal")
ax = y_fore.plot(ax=ax, label="Seasonal Forecast", color='C3')
_ = ax.legend()

![image](https://user-images.githubusercontent.com/113107446/201765684-4b14301f-9e06-4d1e-9398-d94387a45aa9.png)



# Compute Fourier features to the 4th order (8 new features) for a
# series y with daily observations and annual seasonality:
#
# fourier_features(y, freq=365.25, order=4)

#Example
tunnel = pd.read_csv(data_dir / "tunnel.csv", parse_dates=["Day"])

# Create a time series in Pandas by setting the index to a date
# column. We parsed "Day" as a date type by using `parse_dates` when
# loading the data.
tunnel = tunnel.set_index("Day")

# By default, Pandas creates a `DatetimeIndex` with dtype `Timestamp`
# (equivalent to `np.datetime64`, representing a time series as a
# sequence of measurements taken at single moments. A `PeriodIndex`,
# on the other hand, represents a time series as a sequence of
# quantities accumulated over periods of time. Periods are often
# easier to work with, so that's what we'll use in this course.
tunnel = tunnel.to_period()

tunnel.head()
NumVehicles
Day	
2003-11-01	103536
2003-11-02	92051
2003-11-03	100795
2003-11-04	102352
2003-11-05	106569
Time-step feature
Provided the time series doesn't have any missing dates, we can create a time dummy by counting out the length of the series.

df = tunnel.copy()

df['Time'] = np.arange(len(tunnel.index))

df.head()
NumVehicles	Time
Day		
2003-11-01	103536	0
2003-11-02	92051	1
2003-11-03	100795	2
2003-11-04	102352	3
2003-11-05	106569	4
The procedure for fitting a linear regression model follows the standard steps for scikit-learn.

from sklearn.linear_model import LinearRegression

# Training data
X = df.loc[:, ['Time']]  # features
y = df.loc[:, 'NumVehicles']  # target

# Train the model
model = LinearRegression()
model.fit(X, y)

# Store the fitted values as a time series with the same time index as
# the training data
y_pred = pd.Series(model.predict(X), index=X.index)
The model actually created is (approximately): Vehicles = 22.5 * Time + 98176. Plotting the fitted values over time shows us how fitting linear regression to the time dummy creates the trend line defined by this equation.

ax = y.plot(**plot_params)
ax = y_pred.plot(ax=ax, linewidth=3)
ax.set_title('Time Plot of Tunnel Traffic');

![image](https://user-images.githubusercontent.com/113107446/201201549-ab31216c-f1a6-4b1f-b90f-5d6841dddb98.png)


y, X = y.align(X, join='inner')  # drop corresponding values in target

model = LinearRegression()
model.fit(X, y)

y_pred = pd.Series(model.predict(X), index=X.index)
The lag plot shows us how well we were able to fit the relationship between the number of vehicles one day and the number the previous day.

fig, ax = plt.subplots()
ax.plot(X['Lag_1'], y, '.', color='0.25')
ax.plot(X['Lag_1'], y_pred)
ax.set_aspect('equal')
ax.set_ylabel('NumVehicles')
ax.set_xlabel('Lag_1')
ax.set_title('Lag Plot of Tunnel Traffic');

![image](https://user-images.githubusercontent.com/113107446/201201834-571263e0-7ab6-4006-b2b0-b9bab7f59f8f.png)

What does this prediction from a lag feature mean about how well we can predict the series across time? The following time plot shows us how our forecasts now respond to the behavior of the series in the recent past.

ax = y.plot(**plot_params)
ax = y_pred.plot()

![image](https://user-images.githubusercontent.com/113107446/201202424-65ecedd8-7932-4809-add6-b8741732e44e.png)


The best time series models will usually include some combination of time-step features and lag features. Over the next few lessons, we'll learn how to engineer features modeling the most common patterns in time series using the features from this lesson as a starting point.


y_ma = y.rolling(7, center=True).mean()
# Plot
ax = y_ma.plot()
ax.set_title("Seven-Day Moving Average");

plot_pacf(y_deseason, lags=8);
plot_lags(y_deseason, lags=8, nrows=2);
