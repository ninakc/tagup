# TagUp Data Engineer Challenge

## Data Structure

The time-series data is stored as an [xarray](http://xarray.pydata.org/en/stable/) Dataset. Data corresponding to each of the four features, feat_0, feat_1, feat_2, and feat_3 are stored as DataArrays with two dimensions: "time" and "machine". The "time" dimension indicates the time the data was recorded and ranges from 2019-01-01 to 2021-09-27, with data available at approximately 8-hour 10-minute intervals. The "machine" dimension indicates which of the 20 machines the data is from. This format enables convenient access to slices of data by any combination of time interval, machine or feature of interest. The [xarray](http://xarray.pydata.org/en/stable/) library also offers convenient plotting functions to produce matplotlib plots.

The static data is stored as a Pandas Dataframe and is saved as the file machine_info.csv. If needed, it could be converted to an xarray and merged with the time-series dataset. However, this is not necessary for the present analyses.

## Data Cleaning

To identify outliers, we visualize the data in figures 1a-1d and 2a-2d. As shown in figures 1a-1d, values with absolute value greater than 150 appear to be outliers as they differ significantly from the rest of the data and do not appear to follow a clear pattern. In figures 2a-2d, we can see that a relatively small proportion of the data for each feature is greater than 150 in absolute value. In particular, as shown in the table pct_outlier_table.csv, the percentage of outliers for each feature is less than 3%. Consequently, all values with absolute value greater than 150 are removed in the remaining analyses.

## Data Analysis - Time Series Visualization and Summary Statistics

The cleaned data with outliers removed is plotted for each feature in figures 3a-3d. The mean and standard deviation for each feature are displayed in summary_stats.csv. 

## Data Analysis - Identification of Machine Failure

As shown in figures 3a-3d and explained in the Challenge README.md file, the machines enter a "faulty" mode before failing. Visually, it appears that this faulty mode might be identifiable via the variability of the data over time, so we compute the standard deviation for each feature and machine across two-week intervals. A longer time interval would reduce the variance of the estimated standard deviation, improving the accuracy of the estimates, while a shorter time interval would increase the frequency of updates to the "mode" variable, potentially enabling quicker detection of machine failures. Two-weeks was chosen to balance these concerns, but a different interval length could be chosen to fit the company's needs.

In figure 4, the standard deviation of each feature within each two-week interval is plotted for each machine. Visually, it appears that standard deviations over 30, 10, 20, and 20, for feat_0, feat_1, feat_2, and feat_3, respectively, represent outliers. So, the "mode" of each machine is defined as "faulty" if any of the features' standard deviations exceeds its corresponding threshold, "failed" if any of the features' standard deviations is less than 1, and "normal" otherwise. This definition could be altered to obtain a more or less sensitive detection algorithm (e.g. one could require all features' standard deviations to exceed their threshold or one could change the thresholds themselves). With this definition of a machine's mode, figures 3a-3d are re-plotted as figures 5a-5d with color corresponding to machine "mode". This defintion of machine "mode" appears to correspond well to the visual appearance of a machine entering a different state.