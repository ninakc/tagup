# TagUp Data Engineer Challenge

## Data Structure

The time-series data is stored using [xarray](http://xarray.pydata.org/en/stable/), as a Dataset object. Data corresponding to each of the four features, feat_0, feat_1, feat_2, and feat_3 are stored as DataArrays with two dimensions: "time" and "machine". The "time" dimension is labelled by the coordinate "time", an array of datetime objects spanning the range from 2019-01-01 to 2021-09-27 at approximately 8-hour 10-minute intervals, and indicates the time the data was recorded. The "machine" dimension is labelled by the coordinate "machine", which indicates which of the 20 machines the data is from. This format enables convenient access to data by any combination of time interval, machine or feature of interest. The [xarray](http://xarray.pydata.org/en/stable/) library also offers convenient plotting functions to produce matplotlib plots.

The static data is stored as a Pandas Dataframe and is saved as the file machine_info.csv. If needed, it could be converted to an xarray and merged with the time-series dataset. However, this is not necessary for the present analyses.

## Data Cleaning

To define outliers, we need to visualize the pattern of the data to determine what should be considered normal vs abnormal behavior of the machines. As shown in figures 1a  - 1d, values with absolute value greater than 150 appear to be outliers as they differ significantly from the rest of the data and do not appear to follow a clear pattern. In figures 2a-2d, we can see that a relatively small proportion of the data for each feature is greater than 150 in absolute value. In particular, as shown in the table saved as pct_outlier_table.csv, the percentage of outliers for each feature is less than 3%. Consequently, all values with absolute value greater than 150 are removed in the remaining analyses.

## Data Analysis - Time Series Visualization and Summary Statistics

  The cleaned data for each feature is plotted with outliers removed in figures 3a-3d. The mean and standard deviation for each feature are displayed in summary_stats.csv. 

## Data Analysis - Identification of Machine Failure

As shown in figures 3a-3d and explained in the Challenge README.md file, the machines enter a "faulty" mode before failing. Visually, it appears that this faulty mode might be identifiable via the variability of the data over time, so we consider the standard deviation for each feature and machine across two-week intervals. A longer time interval would reduce the variance of the estimated standard deviation (by including more datapoints), improving the accuracy of the estimates, while a shorter time interval would increase the frequency of updates to the "mode" variable, potentially enabling quicker detection of machine failures. Two-weeks was chosen to balance these concerns, but a different interval length could be chosen to fit a company's needs.

In figure 4, the standard deviation of each feature within each two-week interval is plotted for each machine. Visually, it appears that standard deviations over 30, 10, 20, and 20, for feat_0, feat_1, feat_2, and feat_3, respectively, represent outliers. So, the "mode" of each machine is defined as "faulty" if any of the features' standard deviations exceeds its corresponding threshold, "failed" if any of the features' standard deviations is less than 1, and "normal" otherwise. This definition could be altered to obtain a more or less sensitive detection algorithm (e.g. one could require all features' standard deviations to exceed their threshold or one could change the thresholds themselves). With this definition of a machine's mode, figures 3a-3d are re-plotted with color corresponding to machine "mode" as figures 5a-5d. This defintion of machine "mode" appears to correspond well to the visual appearance of a machine entering a different state.