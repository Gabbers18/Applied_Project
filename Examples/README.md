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

## What is CRQA?
Cross Recurrence Quantification Analysis (CRQA) captures the dynamics between two people. It is used to quantify patterns in language and movement. It captures non-linear patterns and synchorny over time. 

## RMD File
You can find the corresponding .rmd file for our motion energy analysis example [here](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_CRQA.Rmd).

## Libraries
- plyr
- crqa
- dplyr
- tseriesChaos 
- ggplot2


## The data
I chose to utilize the .csv formatted data for simplicity purposes for this analysis.
This is because you can run CRQA with any time series data.

- [CSV data](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_Datasets/dyad_16_MEA.csv)

## Setting Parameters for the Analysis

### Our parameters
- theiler window
- delay
- embedding dimenson
- radius
- rescale type

## Theiler Window
- Theiler window excludes near-diagonal points from being counted as recurrences
- Set to 0
- 0 means no exclusion of points
- we will use this later when determining embedding dimension

### Example in R:

```r
cross_theiler_window = 0
```

## Radius

Radius is a threshold value in CRQA that determines how close two points in phase space must be to count as a recurrence (i.e., considered “similar”). The radius defines the sensitivity of recurrence detection.

### Significance of radius values:

* Smaller radius: only very similar points are considered recurrent (may result in low recurrence rates).
* Larger radius: more points are considered recurrent, including less similar ones (may result in overly high recurrence).
    
Overall, the goal is to choose a radius that produces a **recurrence rate (RR)** within an interpretable and consistent range—often around **2–5%**, but this can vary by study.

In this case I chose a radius of **0.1**. This small value **increases** the sensitivity to recurrent points in this data. This stricter criteria means our analysis will be more sensitive to smaller differences in the data.

### Example in R:

```r
crqa_analysis = crqa(ts1 = rescaled_p1, 
                    ts2 = rescaled_p2,
                    delay = cross_chosen_delay, 
                    embed = cross_chosen_embedding, 
                    r = .1, # we are looking at this value!
                    normalize = 0, 
                    rescale = 0,
                    mindiagline = 2,
                    minvertline = 2, 
                    tw = cross_theiler_window, 
                    whiteline = FALSE,
                    recpt=FALSE)
```

## Delay
Delay is a parameter set within CRQA which refer to the time lag between data points used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing their similarity or synchronization.

Delay is a parameter used in CRQA that refers to the **time lag between data points** used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing similarity or synchronization.

### How Delay is Determined:

* Calculated using **Average Mutual Information (AMI)**.
* AMI measures the amount of shared information between the original time series and its delayed version.
* The **first local minimum** in the AMI curve indicates the optimal delay—this is the point where each successive data point provides the most new information.

### Example in R:

```r
# Calculate AMI for participant time series up to lag 800
ami_values <- mutual(ts_data, lag.max = 800)

# Identify first local minimum of AMI
find_first_minimum <- function(ami_values) {
  for (i in 2:(length(ami_values) - 1)) {
    if (ami_values[i] < ami_values[i - 1] && ami_values[i] < ami_values[i + 1]) {
      return(i)
    }
  }
  return(NULL)
}

# Get optimal delay
optimal_delay <- find_first_minimum(ami_values)
```

## Embedding Dimension

The embedding dimension determines the **number of consecutive data points** used to reconstruct the system’s state space. It captures how many dimensions are needed to unfold the underlying dynamics of the system without overlaps or false trajectories.

### How Embedding Dimension is Determined:

* Typically selected using the **False Nearest Neighbors (FNN)** algorithm.
* FNN identifies the minimum number of dimensions where false neighbors (points that appear close due to projection in low dimensions) are minimized.
* The optimal embedding dimension is where the **percentage of false neighbors drops to near zero or levels off**, meaning the system’s dynamics are well represented.

### Example in R:

```r
# Calculate FNN with previously selected delay
fnn_values <- false.nearest(ts_data, m = 20, d = optimal_delay, t = 0)

# Extract the fraction of false neighbors
fraction_values <- as.numeric(fnn_values["fraction", ])
fraction_values <- fraction_values[!is.na(fraction_values)]

# Find the first local minimum (drop in false neighbors)
embedding_dimension <- which(diff(sign(diff(fraction_values))) > 0) + 1
embedding_dimension <- embedding_dimension[1]
```

## Rescale Type
- Rescale type is set a method of **normalizing** the time series before computing distances
- Initlaly set rescale type to 'mean'

```r
cross_rescale_type = 'mean'
```

### Implementation
- Utilize this code to rescale the time series data for each participant based on the chosen 'cross_rescale_type', so the CRQA comparison is done on a comparable scale.
  
```r
if (cross_rescale_type == 'mean'){
  rescaled_p1 = ts_participant1s / mean(ts_participant1s)
  rescaled_p2 = ts_participant2s / mean(ts_participant2s)
} else if (cross_rescale_type == 'max'){
  rescaled_p1 = ts_participant1s / max(ts_participant1s)
  rescaled_p2 = ts_participant2s / max(ts_participant2s)
}
```

## Useful function for large datasets

I utilized this function to extract the middle 60% of the time series; consider using this function or something similar to sample your large datasets.

I recieved this warning, which led to troubleshooting a method to sampling my datasets:
Warning: sparse->dense coercion: allocating vector of size 1.2 GiBError: vector memory exhausted (limit reached?)

This warning will usually appear if you are working with large timeseries datasets with **more than 12000 rows** within the CRQA function.

### Example in R: 

```r
get_middle_60_percent <- function(time_series) {
  total_length <- length(time_series)
  start_index <- floor(0.2 * total_length) + 1
  end_index <- ceiling(0.8 * total_length)
  return(time_series[start_index:end_index])
}
```

## Results - Outcome Variables
- rate of recurrence
- determinism
- total number of lines on the plot
- maximum line length on plot
- average line length on plot
- entropy
- normalized entropy
- laminarity
- trapping time

## Example in R:

```r
crqa_analysis$RR # rate of recurrence
crqa_analysis$DET # % determinism
crqa_analysis$NRLINE # total number of lines on the plot
crqa_analysis$maxL # maximum line length on plot
crqa_analysis$L # average line length on plot
crqa_analysis$ENTR # entropy
crqa_analysis$rENTR # normalized entropy
crqa_analysis$LAM # laminarity
crqa_analysis$TT # trapping time
```

## Example Visualization
For this example, I chose to include two methods of visualizing the analysis. The first method utilizes the function 'plotRP()' which is from the 'crqa' library. The second method I used 'ggplot.'

### Plotting Method 1
```r
# CRQA Plot for Single Dyad - Dyad 16
par = list(unit = 2, 
           labelx = "x-axis movement", 
           labely = "y-axis movement", 
           cols = "red", 
           pcex = 1)
plotRP(crqa_analysis$RP, par)
```

### Plotting Method 2
```r
crqa_df = data.frame(points = crqa_analysis$RP@i,
                           loc = seq_along(crqa_analysis$RP@i))
ggplot(crqa_df,aes(x=points,
                        y=loc)) +
  geom_point(color="black",size=.01) +
  theme_classic() +
  theme(legend.position="none", axis.text.x = element_blank(), axis.text.y = element_blank()) +
  ylab("Participant 2") + xlab("Participant 1") +
  ggtitle("Dyad 16\nCross-recurrence Plot between\nParticipant 1 and Participant 2 Movement in Survivor Task")
```

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

