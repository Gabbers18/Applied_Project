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

## The data
I chose to utilize the .csv formatted data for simplicity purposes for this analysis.
This is because you can run CRQA with any time series data.

-[.csv data](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_Datasets/dyad_16_MEA.csv)

## Setting Parameters for the Analysis

### Our parameters
- cross theiler window
- delay
- embedding dimenson
- radius

## Results - Outcome variables from CRQA
- rate of recurrence
- determinism
- total number of lines on the plot
- maximum line length on plot
- average line length on plot
- entropy
- normalized entropy
- laminarity
- trapping time

## What are these metrics?
1) <ins>RR: Recurrence Rate<ins>

The percentage of time points where both participants exhibit the same or similar states. Higher values indicate stronger coordination.

2) <ins>DET: Determinism<ins>

The proportion of recurrent points that form diagonal lines, reflecting structured and predictable behavior in the interaction. Higher values suggest more patterned coordination.

3) <ins>NRLINE: Number of Diagonal Lines<ins>

Counts the number of diagonal line structures in the recurrence plot, indicating sequences of shared states.

4) <ins>maxL: Maximum Line Length<ins>

The longest diagonal line in the recurrence plot, representing the longest uninterrupted period of synchronization.

 5) <ins>L: Average Line Length<ins>

The mean length of diagonal lines, indicating the average duration of coordinated sequences. Higher values suggest greater interaction stability.

 6) <ins>ENTR: Entropy of Line Lengths<ins>

Measures the variability in diagonal line lengths, reflecting the complexity or predictability of coordination patterns.

7) <ins>rENTR: Relative Entropy<ins>

Normalized entropy value, comparing the structure of the recurrence to a baseline or expected distribution.

8) <ins>LAM: Laminarity<ins>

The percentage of recurrent points forming vertical lines, capturing intermittent coordination or pauses in movement. Higher values suggest periods of engagement followed by disengagement.

 9) <ins>TT: Trapping Time<ins>

The average length of vertical lines, representing how long interactions remain in a stable state before shifting. Longer times indicate sustained coordination.

## Results interpretation for this example:

1. <ins>RR: Recurrence Rate</ins>

Indicates the proportion of recurrent points in the recurrence plot. Low RR means little to no synchrony; high RR indicates synchrony. 

**Result: 0.0765 (7.65%)**

This low RR suggests minimal synchrony between the participants, indicating that they rarely occupied similar states simultaneously during the interaction.

2. <ins>DET: Determinism</ins>

Measures the proportion of recurrence points forming diagonal lines. Higher DET suggests more patterned coordination. 

**Result: 77.06%**

A DET of 77.06% indicates a moderate level of structured coordination, suggesting that when synchrony occurred, it followed somewhat predictable patterns.

3. <ins>maxL: Maximum Line Length</ins>

The longest diagonal line in the recurrence plot, representing the longest uninterrupted period of synchrony.  A high maxL suggests that this dyad has a very long, continuous period of synchrony or similarity in movements; low suggests they had a short period of similarity.

**Result: 16,891**

This number reflects a moderate frequency of shared behavioral sequences, implying occasional periods of coordinated activity.

4. <ins>NRLINE: Number of Diagonal Lines</ins>

Counts the number of diagonal line structures in the recurrence plot, indicating sequences of shared states. A high NRLINE means there is a lot of repeated behaviors occurring throughout the interaction.

**Result: 26**

A maxL of 26 suggests that the longest continuous period of synchronization between the participants was brief, indicating limited sustained coordination.

5. <ins>L: Average Line Length</ins>

The average length of diagonal lines in the recurrence plot.

**Result: 3.96**

An average line length of 3.96 indicates that, on average, coordinated sequences were short-lived, reflecting transient synchrony.

6. <ins>ENTR: Entropy of Line Lengths</ins>

Reflects the complexity of interaction by measuring variability in line lengths. A higher entropy suggests more diverse and complex dynamics. A lower value implies more uniform, stable, and predictable behavior.

**Result: 1.82**

This entropy value suggests a moderate level of complexity in the coordination patterns, indicating some variability in the duration of synchronized behaviors.

7. <ins>rENTR: Relative Entropy</ins>

Normalized entropy value. A high value (around 1) indicates more complex movement patterns within the dyad, while lower values reflect more regular, less complex coordination.

**Result: 0.565**

A relative entropy of 0.565 indicates a moderate level of complexity relative to the expected distribution, suggesting that the coordination patterns were neither highly predictable nor entirely random.

8. <ins>LAM: Laminarity</ins>

Measures the proportion of recurrence points forming vertical lines, which reflect periods of behavioral stasis or resistance to change. A high laminarity value indicates that the participants experienced extended periods of similar states, possibly reflecting moments of mutual stillness or sustained attention.

Result: 84.87%
A high laminarity value indicates that the participants experienced extended periods of similar states, possibly reflecting moments of mutual stillness or sustained attention.

9. <ins>TT: Trapping Time</ins>

The average length of vertical lines, representing how long interactions remain in a stable state before shifting. Longer times indicate sustained coordination.

**Result: 5.14**

A trapping time of 5.14 suggests that, on average, the participants remained in similar states for short durations, indicating brief periods of stability in their interaction.






