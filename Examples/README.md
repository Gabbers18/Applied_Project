# Examples

Within this folder, you can find a few examples of how to perform CRQA utilizing timeseries data derived from Ramseyer's MEA. Not only this, I break down this example into multiple parts to illustrate how to perform both of these analyses separately, or jointly depending on your goals. 


# MEA

## RMD File
You can find the corresponding .rmd file for our motion energy analysis example [here](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd).

## Libraries
- rMEA

## Objectives
The goal of this example is to visualize output data from Ramseyer's MEA. 

Within this example, we will be utilizing a series of sample datasets, which correspond to motion energy values derived from MEA.

## The data
- [Example 1 dataset](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_Datasets/MEA_video16.txt)
- [Example 2 dataset](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_Datasets/dyad_16_MEA.csv)

## Data Description

### Example 1
- .txt file
- output from MEA
- Particiant identity is determined by pretenses set in MEA
- Column 1 is participant 2
- Column 2 is participant 1

### Example 2
- .csv file
- column 1 is Participant 2's timeseries data
- column 2 is participant 1's timeseries data

## When might I follow example 1?

- When utilizing .txt data derived directy from Ramseyer's MEA
- When utilizing any type of .txt data

## When might I follow example 2?

- When utilizing .csv data
- When you are wanting to visualize .csv data


# CRQA

## RMD File
You can find the corresponding .rmd file for our motion energy analysis example [here](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_CRQA.Rmd).

## Libraries
- plyr
- crqa
- dplyr
- tseriesChaos 
- ggplot2

## Useful function for large datasets

I utilized this function to extract the middle 60% of the time series; consider using this function or something similar to sample your large datasets.

I recieved this warning, which led to troubleshooting a method to sampling my datasets:
Warning: sparse->dense coercion: allocating vector of size 1.2 GiBError: vector memory exhausted (limit reached?)

This warning will usually appear if you are working with large timeseries datasets with **more than 12000 rows** within the CRQA function.

```{r function-for-all-data}
get_middle_60_percent <- function(time_series) {
  total_length <- length(time_series)
  start_index <- floor(0.2 * total_length) + 1
  end_index <- ceiling(0.8 * total_length)
  return(time_series[start_index:end_index])
}
```


