# Applied Project (Fall 2023-Spring 2025)
# Investigating Dyadic Persuasion through Behavioral Synchrony: A CRQA Approach

#### By: Gabrielle Young; Advisor: Dr. Nicholas Duran

## Overview
This study examines how individuals coordinate movements on a shared, goal-oriented task by analyzing real-time multimodal data. Using **Cross-Recurrence Quantification Analysis (CRQA)**, we identify **initiator** and **responder** roles through movement patterns, while **Motion Energy Analysis (MEA)** extracts each participant's overall motion energies. These techniques help uncover interaction patterns that may predict persuasion or shifts in opinion as individuals negotiate and align toward a shared decision.

_View the link to the presentation [here.](https://docs.google.com/presentation/d/1EzJJVp6K7kZwB51ulxABSgz0aShCgJfpRR7RleH1l2I/edit?usp=sharing)_

## Research Questions
1) How does movement synchronization affect persuasion?
2) How does movement specialization (initiator/responder roles) affect persuasion?

## Significance of Research Questions

<ins>**Research Question 1**<ins>

Past research suggests synchronizion of movements, language, gesturing, etc. fosters trust, and rapport. With this   in mind, we ponder whether dyads who synchronize are likely to change there minds similarly when making joint-  decisions.

<ins>**Research Question 2**<ins>

Past research also suggests that individuals who take on initator (leader) roles tend to have more of a persuasive influence on their peers. We believe that dyads where intiator/responder roles emerge may show more asymmetrical changes in responses when making joint-decisions.

Both of these questions link the embodied coordination of interaction to deeper cognitive and/or social outcomes. Specifically, we are looking into how people change their minds or influence one another while working toward a shared decision.

## Repository Structure

```
Applied_Project/
├── data/                          
│   ├── TxtFiles_cleaned/          # Files derived from MEA
│   └── cleaned_qualtrics.csv/     # Survey & Task data - for calculating change scores
├── notebooks/                     # R Markdown notebooks
│   ├── CRQA_Full.Rmd              # Comprehensive CRQA analysis
│   ├── ChangeScores_Full.Rmd      # Persuasion Score Calculation using cleaned_qualtrics.csv
│   └── Descriptive_Stats.Rmd      # Descriptive statistics of Final_CRQA_Results.csv  
├── results/                       # Output results from analyses
│   ├── Change_Scores.csv          # Persuasion Change Scores        
│   ├── CRQA_Descriptives_Results.csv                 
│   └── clean_results.csv          # Final CRQA Results
├── Examples/                      # Example usage scripts with data
│   ├── Example_CRQA.Rmd         
│   ├── Example_MEA.Rmd
│   ├── README.md     
│   ├── Example_Datasets/
│      ├── MEA_video16.txt        # Dyad 16 MEA output
│      ├── dyad_16_MEA.csv        # Dyad 16 MEA output converted to csv
│      └── README.md              # Dataset overview
├── LICENSE                       # MIT License file           
├── README.md                     # Project overview and instructions    
└── .gitignore                   
```

## Our Data
- TxtFiles_cleaned
- cleaned_qualtrics.csv

### Data Descriptions

**TxtFiles_cleaned**
- Source:
  - Originally 60 video files from dyads completing a goal-oriented "Survival" Task
- Format:
  - 52 .txt files identified by Dyad #
- Preprocessing:
  - Video data converted using MEA
  - Files were excluded if they showed:
    - Task interference (e.g., experimenter interruptions, technical malfunctions)
    - Data corruption (e.g., missing or unreadable video frames)
    - Incomplete sessions (e.g., participants not finishing the task).
  - Final dataset includes 52 dyads derived from 52 corresponding video files.

**cleaned_qualtrics.csv**
- Source:
  -Survey responses from 120 main study participants and 20 pilot participants.
- Format:
  - .csv file with 140 rows, each representing one participant.
  - Participants are identified by dyad_number and participant_number.
- Preprocessing:
  - All data were de-identified to protect participant confidentiality.
  - Rows with missing data (NAs) were removed.
  - Column names were standardized and renamed for clarity.
  - Variables were reordered based on relevance to the research questions.

## Libraries
- plyr
- crqa
- dplyr
- rMEA
- zoo
- tseriesChaos 
- ggplot2
- purrr
- readr
- tidyr
- scales

# Part 1
## Motion Energy Analysis (MEA)
Motion Energy Analysis was developed by Ramseyer in 2019. MEA is a non-invasive method of developed by  extracting frame-by-frame differences in movement from video data. This method is especially useful when trying to analyze nonverbal behavior. 

The data extracted from this analysis is outputed as txt files. For the purposes of this study, we have chosen to capture entire body movements. Looking within the files, we can see each participant's movement is represented by a singluar column in the txt file. Each txt file represents a dyad.

_More information on the MEA software can be found at [this link.](https://psync.ch/mea/)_

_More information on the rMEA package can be found at [this link.](https://github.com/kleinbub/rMEA)._

## Visualizing the frame-by-frame differences in interactions - Example Dyad 40
![gif](https://github.com/user-attachments/assets/882d8317-5555-4003-a48d-c325caadc337)

## Example R code
For .txt files - direct output of MEA

```r
mea_normal <- readMEA("your_folder_path_to_MEA_files", 
sampRate = 25, s1Col = 2, s2Col = 1,
                     s1Name = "Participant2", s2Name = "Participant1", skip=1,
                     idOrder = c("id","session"), idSep="_")
```

## Code explaination
This section utilizes **only** the 'rMEA' library. 

Item descriptions:
- s1Col = what each column should be identified as; in this case, the first column was participant 2 
- s2Col = what each column should be identified as; in this case, the first column was participant 1
- s1Name = corresponding name of column 1
- s2Name = corresponding name of column 2

## Important notes
- The path for this code **must** be a folder, not individual files
- This code utilizes a folder containing **only** .txt files; see [Example_MEA.Rmd](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd) for how to run other file types
- This package is useful for naming columns and parsing columns appropriately
- Useful for visualing the time series data; see [Example_MEA.Rmd](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd)  diagnostic plots

## Structure of mea_normal Output list
```
mea_normal/
├── all_MEA_1000/                          # each individual file (ex: dyad 1000 here)
│   ├── MEA/                               # 2 column MEA output 
│        ├── Participant2                  # column 1 extracted
│        └── Participant1                  # column 2 extracted
├── all_MEA_101/                          
│   ├── MEA/                       
│        ├── Participant2
│        └── Participant1
├── all_MEA_102/                         
│   ├── MEA/                      
│        ├── Participant2
│        └── Participant1
# ... and so on for each file
```

# Part 2
## Cross Recurrence Quantification Analysis (CRQA)
This method is used to capture the dynamics between two people. It is used to quantify patterns in language and movement. It captures non-linear patterns and synchorny over time. 

We will be using this technique in conjuction with the continuous data derived from the MEA. This is time series data between two participants which will be reconstructed into a single, two-dimensional plot.

We will specificaly be examining initiator/responder patterns within this dataset. CRQA is a method that has been used in the literature to examine coupling properties of leader-follower relationships, which is similar to what we are examining.

We will be using Monerno and Coco CRQA R package to conduct this analysis. 

_More information on this package can be found at [this link.](https://github.com/morenococo/crqa)_

## Setting Paramaters

### Our parameters
- theiler window
- delay
- embedding dimenson
- radius
- rescale type

# Step 1: Determine Inital Parameter(s)

I chose to intially set the following variables:
1) theiler window
2) rescale type
3) radius

## 1) Theiler Window
- Theiler window excludes near-diagonal points from being counted as recurrences
- Set to 0
- 0 means no exclusion of points
- we will use this later when determining embedding dimension

**Example in R**
```r
cross_theiler_window = 0
```
## 2) Rescale type

- Rescale type is set a method of **normalizing** the time series before computing distances
- Initlaly set rescale type to 'mean'

**Example in R**
```r
cross_rescale_type = 'mean'
```
## 3) Radius

In this case I chose a radius of **0.1**. This small value **increases** the sensitivity to recurrent points in this data. This stricter criteria means our analysis will be more sensitive to smaller differences in the data.

**Example in R**

```r
radius = .1
```

# Step 2: Create a Random Sample

## First, set a seed for reproducibility.

**Example in R:**
```r
set.seed(123)
```

## Second, take a random sample of 5 dyads.

Sampling Justification:
- Our data contains files between 12,000-21,000 rows, making full-scale computations resource-intensive
- Sampling a smaller subset allows for efficient parameter estimation, without processing the entire dataset.

**Example in R:**
```r
dyads_to_sample <- sample(1:length(mea_normal), 5)
```

# Step 3: Create Functions for Parameter Calculation

We will be calculating
1) Delay
2) Embedding Dimension

## 1) Delay

Delay is a parameter set within CRQA which refer to the time lag between data points used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing their similarity or synchronization.

Delay is a parameter used in CRQA that refers to the **time lag between data points** used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing similarity or synchronization.

## How Delay is Determined:

* Calculated using **Average Mutual Information (AMI)**.
* AMI measures the amount of shared information between the original time series and its delayed version.
* The **first local minimum** in the AMI curve indicates the optimal delay—this is the point where each successive data point provides the most new information.

## Function to calcualte AMI:

```{r find-first-minimum}
find_first_minimum <- function(ami_values) {
  for (i in 2:(length(ami_values) - 1)) {
    if (ami_values[i] < ami_values[i - 1] && ami_values[i] < ami_values[i + 1]) {
      return(i)
    }
  }
  return(NULL)
}
```

## 2) Embedding Dimension

Determines the **number of consecutive data points** used to reconstruct the system’s state space. It captures how many dimensions are needed to unfold the underlying dynamics of the system without overlaps or false trajectories.

## How Embedding Dimension is Determined:

* Typically selected using the **False Nearest Neighbors (FNN)** algorithm.
* FNN identifies the minimum number of dimensions where false neighbors (points that appear close due to projection in low dimensions) are minimized.
* The optimal embedding dimension is where the **percentage of false neighbors drops to near zero or levels off**, meaning the system’s dynamics are well represented.

## Function to caculate FNN

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

# Step 4: Calculate Delay and Embedding Dimension Parameters

## First, initialize storage for parameters.

- Set up empty vectors to store the computed delay and embedding dimension values for each sampled dyad.
```r
delays <- c()
embeddings <- c()
```

## Second, iterate over our selected sampled dyads.

- Loop through each dyad selected in the `dyads_to_sample` vector to perform the analysis.
```r
for (i in 1:length(dyads_to_sample)) {
  # Analysis steps for each dyad
}
```

Within the loop:

### Select dyad data

- Extract the data for the current dyad from the `mea_normal` list.

```r
dyad_data <- mea_normal[[dyads_to_sample[i]]][[1]]
```


## Third, extract individual participant time series.

- Retrieve the time series data for Participant 1 and Participant 2.

```r
ts_participant1 <- dyad_data$Participant1
ts_participant2 <- dyad_data$Participant2
```

## Fourth, select middle 60% of time series

## _Troubleshooting Memory Issue:_
  
<ins>**Purpose:**<ins>

To mitigate memory-related errors during analysis, particularly when working with large time series datasets.

<ins>**Background:**<ins>

Processing extensive time series data (e.g., datasets exceeding 12,000 rows) can lead to memory exhaustion errors in R, especially when using the 'crqa()' function. You may recieve warnings such as:

```
Warning: sparse->dense coercion: allocating vector of size 1.2 GiB
Error: vector memory exhausted (limit reached?)
```

These errors occur when R attempts to convert large sparse matrices into dense ones, consuming significant memory resources. This is especially pertinent when using packages like `crqa`, which may not efficiently handle very large datasets.

<ins>**Solution:**<ins>

To address this, we focus on the central 60% of the time series.

**Example in R:**
```r
get_middle_60_percent <- function(time_series) {
  total_length <- length(time_series)
  start_index <- floor(0.2 * total_length) + 1
  end_index <- ceiling(0.8 * total_length)
  return(time_series[start_index:end_index])
}
```

**Benefits:**

- Reduces the risk of memory exhaustion errors.
- Enhances computational efficiency.
- Maintains the integrity of the analysis by focusing on the most stable segment of the data; therefore, reducing edge effects

## Fifth: Determine Delay Using Average Mutual Information (AMI)

- Compute the AMI for each participant's time series to identify the optimal delay using the functions we created in **Step 3.**

```r
cross_ami_p1 <- mutual(ts_participant1s, lag.max = 800)
cross_ami_p2 <- mutual(ts_participant2s, lag.max = 800)

chosen_delay_p1 <- find_first_minimum(cross_ami_p1)
chosen_delay_p2 <- find_first_minimum(cross_ami_p2)
cross_chosen_delay <- round(mean(c(chosen_delay_p1, chosen_delay_p2)))
delays <- c(delays, cross_chosen_delay)
```

* `mutual()` computes the mutual information for a range of lags.
* `find_first_minimum()` identifies the first local minimum in the mutual information, suggesting an optimal delay.

## Example Output AMI Plots - Dyad 16
<img width="459" alt="AMI_plot_2" src="https://github.com/user-attachments/assets/5e8efa03-6c62-49a9-b292-35ca794eba0e" />
<img width="459" alt="AMI_plot_1" src="https://github.com/user-attachments/assets/aaa87257-b323-4671-a60b-2eb130ea3892" />

## Sixth: Determine Embedding Dimension Using FNN

- Apply the FNN method to estimate the appropriate embedding dimension.

```r
cross_max_embedding <- 10
cross_fnn_p1 <- false.nearest(ts_participant1s, m = cross_max_embedding, d = cross_chosen_delay, t = 0)
cross_fnn_p2 <- false.nearest(ts_participant2s, m = cross_max_embedding, d = cross_chosen_delay, t = 0)

elbow_p1 <- find_elbow(cross_fnn_p1)
elbow_p2 <- find_elbow(cross_fnn_p2)
cross_chosen_embedding <- round(mean(c(elbow_p1, elbow_p2)))
embeddings <- c(embeddings, cross_chosen_embedding)
```

* `false.nearest()` calculates the fraction of false nearest neighbors for different embedding dimensions.
* `find_elbow()` identifies the "elbow point" in the FNN results, indicating the optimal embedding dimension

## Optional: Visualize FNN Results

- Plot the FNN results for each participant to visually assess the embedding dimension selection.

```r
plot(cross_fnn_p1, type = "b", main = paste("Dyad", dyads_to_sample[i], "- FNN P1"))
plot(cross_fnn_p2, type = "b", main = paste("Dyad", dyads_to_sample[i], "- FNN P2"))
```

## Example Output FNN Plots - Dyad 16
<img width="469" alt="FNN_plot_1" src="https://github.com/user-attachments/assets/c186fb6c-1f2a-4093-b36a-49a12204e5e8" />
<img width="461" alt="FNN_plot_2" src="https://github.com/user-attachments/assets/4fd8c41f-6ebe-4747-86ef-a84a93455805" />

## Seventh: Average the Parameters

- Average out the calculated delays and embedding dimensions across the five sampled dyads.
- Save these variables as `average_delay` and `average_embedding` to be used later in our CRQA calculations.

```r
average_delay <- round(mean(delays, na.rm = TRUE))
average_embedding <- round(mean(embeddings, na.rm = TRUE))

cat("Average Delay: ", average_delay, "\n")
cat("Average Embedding: ", average_embedding, "\n")
```

# Step 5: Function for All Dyads

## First: Define the Function and Initialize Storage

- Here, we utilize the parameters we have named and calculated previously as inputs.

**Example in R:**
```r
run_crqa_for_dyads <- function(df_list, cross_rescale_type, average_delay, average_embedding, cross_theiler_window) {
  crqa_results <- list()
  # Function continues...
}
```

## Second: Loop Through Each Dyad and Prepare Time Series

- Iterate over each dyad in the provided list
- Extract and preprocess the time series data for both participants

**Example in R:**
```r
for (dyad_name in names(df_list)) {
  if (grepl("Participant1", dyad_name)) {
    ts_participant1 <- df_list[[dyad_name]]
    ts_participant2 <- df_list[[gsub("Participant1", "Participant2", dyad_name)]]
    # Function continues...
  }
}
```

- **Identify Dyad Pairs:** For each entry in `df_list`, check if it corresponds to "Participant1". If so, assume a corresponding "Participant2" exists.
- **Extract Time Series:** Retrieve the time series data for both participants using the dyad name and its modified version.


## Third: Convert Time Series Data into Numeric Vectors

**Example in R:**
```r
ts_participant1s <- unlist(ts_participant1)
ts_participant1s <- as.numeric(ts_participant1s)
ts_participant2s <- unlist(ts_participant2)
ts_participant2s <- as.numeric(ts_participant2s)
```

## Fourth: Trim Time Series Data

- Focus on the central portion to mitigate edge effects using our 60% function

```r
ts_participant1s <- get_middle_60_percent(ts_participant1s)
ts_participant2s <- get_middle_60_percent(ts_participant2s)
```

## Fifth: Rescale Time Series

- Normalize the time series data based on the specified rescaling method to ensure comparability between participants.

**Example in R:**
```r
if (cross_rescale_type == 'mean') {
  rescaled_p1 <- ts_participant1s / mean(ts_participant1s)
  rescaled_p2 <- ts_participant2s / mean(ts_participant2s)
} else if (cross_rescale_type == 'max') {
  rescaled_p1 <- ts_participant1s / max(ts_participant1s)
  rescaled_p2 <- ts_participant2s / max(ts_participant2s)
}
```

## Sixth: Perform CRQA Analysis

```r
crqa_analysis <- crqa(ts1 = rescaled_p1,
                      ts2 = rescaled_p2,
                      delay = average_delay,
                      embed = average_embedding,
                      r = radius,
                      normalize = 0,
                      rescale = 0,
                      mindiagline = 2,
                      minvertline = 2,
                      tw = cross_theiler_window,
                      whiteline = FALSE,
                      recpt = FALSE)
```

## Seventh: Extract and Store CRQA Metrics

```r
crqa_results[[dyad_name]] <- list(
  RR = crqa_analysis$RR,
  DET = crqa_analysis$DET,
  NRLINE = crqa_analysis$NRLINE,
  maxL = crqa_analysis$maxL,
  L = crqa_analysis$L,
  ENTR = crqa_analysis$ENTR,
  rENTR = crqa_analysis$rENTR,
  LAM = crqa_analysis$LAM,
  TT = crqa_analysis$TT
)
```

# Step 6: Using the Function

## First: Define the Data Source and Prepare Batches

- Here, we process multiple `.txt` files containing dyadic time series data:

```r
MEA_folder_path <- "your_folder_path"
```

- This retrieves all files ending with `.txt` in the given directory:

```r
txt_files <- list.files(path = MEA_folder_path2, pattern = "\\.txt$", full.names = TRUE)
```

- This divides the list of files into smaller groups (batches) of 28 files each:

```r
batch_size <- 28
batches <- split(txt_files, ceiling(seq_along(txt_files) / batch_size))
```

- Here, we initialize an empty dataframe to store the results:

```r
all_results <- data.frame(Dyad = character(), stringsAsFactors = FALSE)
```

## Second, Process Each Batch and Apply the CRQA Function

- For each batch of files, the code reads the data, organizes it, and applies the `run_crqa_for_dyads` function.

## Create an empty dataframe to store all results
```r
all_results <- data.frame(Dyad = character(), stringsAsFactors = FALSE)
```

### Loop through each batch:
```r
for (i in seq_along(batches)) {
  batch_files <- batches[[i]]
  df_list <- list()
  
  # Read and store each file in df_list
  for (file in batch_files) {
    df <- read_delim(file, delim = "\t", col_names = FALSE)  # Adjust delimiter if necessary
    base_name <- tools::file_path_sans_ext(basename(file))
    df_list[[base_name]] <- df
  }
  
  # Run CRQA function on the batch
  batch_results <- run_crqa_for_dyads(df_list, cross_rescale_type, average_delay, average_embedding, cross_theiler_window)
  
  # Convert results list to a dataframe
  batch_results_df <- do.call(rbind, lapply(names(batch_results), function(name) {
    data.frame(Dyad = name, t(batch_results[[name]]))
  }))
  
  # Append batch results to final dataframe
  all_results <- rbind(all_results, batch_results_df)
}
```

## Third, View Results

```r
print(all_results)
```

## Fourth, Clean and Export the Final Results

After processing all batches, the results are cleaned and saved for further analysis:

- Clean the Dyad Names
- Convert Dyad Identifiers to Numeric and Sort
- Flatten List Columns

```r
if ("Dyad" %in% names(all_results)) {
  clean_results <- all_results 
  clean_results$Dyad <- sub("_Participant[12]_MEA", "", clean_results$Dyad)
}

clean_results <- clean_results %>%
  mutate(Dyad = as.numeric(sub("Dyad", "", Dyad))) %>%
  arrange(Dyad)

# Print final, cleaned results
print(head(clean_results))
```

This ensures that the `Dyad` column is numeric and the data is sorted accordingly.

- **Flatten List Columns**:

```r
clean_results <- clean_results %>%
  mutate(across(where(is.list), ~sapply(., function(x) paste(unlist(x), collapse = ", "))))  # Flatten list columns
```

This step converts any list-type columns into character strings for easier handling.

## Fifth: Save Results as a csv

```r
write.csv(clean_results, file = "your_desired_file_location.csv", row.names = FALSE)
```

- **Preview the Results**:

```r
clean_results %>% head(10)
```

## Example Output
```
   Dyad        RR      DET  NRLINE maxL        L    ENTR    rENTR      LAM       TT
1     16 0.2272047 77.37567   49230   34 4.078529 1.871789 0.5503323 87.74042 6.257235
2     17 8.6873540 84.53008 2415466   39 4.076759 1.931973 0.5311141 90.45594 6.047816
3     18 0.0290182 47.66987    7579    6 2.280908 0.671311 0.4171090 78.67582 4.670322
4     19 0.9133168 90.66628  153174   45 6.506907 2.441424 0.6531941 93.93665 8.980728
5     21 0.6226238 81.39480  157352   61 4.068954 1.847881 0.4513253 88.15268 5.152100
6     23 0.3523955 68.32132   80156   53 3.794750 1.674242 0.4279735 79.11179 5.262472
7     24 0.9184221 86.02303  175875   45 4.754610 2.137165 0.5682135 92.42515 8.317758
8     25 0.0959766 72.02758   24594   24 3.580263 1.670167 0.5326646 79.68409 4.013555
9     26 1.7909189 86.73970  358559  208 5.001096 2.119385 0.3977918 93.79627 8.589039
10    27 0.8902013 78.03127  157958   31 4.114695 1.867929 0.5491976 87.39552 6.562338
```

## Output File
- [clean_results.csv](https://github.com/Gabbers18/Applied_Project/blob/main/Results/clean_results.csv)

## Results Interpretation
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

# Part 3 - Optional
## Descriptive Statistics

I conducted a short analysis to gather the descriptive statstics of my results.

## The Data

Source:
 - For this analysis, we will be using our results output file from the CRQA.
 - [clean_results.csv](https://github.com/Gabbers18/Applied_Project/blob/main/Results/clean_results.csv)
    
Format:
- .csv file
- 43 rows
-  Variables:
    - `Dyad`
    - `RR`
    - `DET`
    - `NRLINE`
    - `maxL`
    - `L`
    - `ENTR`
    - `rENTR`
    - `LAM`
    - `TT`

## Libraries
- dplyr
- ggplot2
- plyr
- tidyr

## Step 1: Open Results csv file
```r
results <- read.csv("your_path_to_CRQA_Results")
```
  
## Step 2: Generate Descriptives
- Mean
- Sd
- Min
- Max

**Example R code:**
```r
descriptives_results <- results %>%
  summarise(
    mean_RR     = mean(RR, na.rm = TRUE),
    sd_RR       = sd(RR, na.rm = TRUE),
    min_RR      = min(RR, na.rm = TRUE),
    max_RR      = max(RR, na.rm = TRUE),
    
    mean_DET    = mean(DET, na.rm = TRUE),
    sd_DET      = sd(DET, na.rm = TRUE),
    min_DET     = min(DET, na.rm = TRUE),
    max_DET     = max(DET, na.rm = TRUE),
# repeat for each metric
) %>% 
  pivot_longer(
    cols = everything(), 
               names_to = c("metrics", "stat"),
               names_sep = "_",
               values_to = "value") %>%
  pivot_wider(names_from = metrics, values_from = value)
```
- Calculated Mean, sd, min, and max for each of our 9 metrics
- Pivoted the table to make the content easier to digest
  
## Example Output
```
  stat        mean         sd       min        max
  <chr>      <dbl>      <dbl>     <dbl>      <dbl>
1 RR          1.65      3.08     0.0230      18.1 
2 DET        80.1      12.6     37.3         95.8 
3 maxL       77.8      68.0      6          292   
4 NRLINE 308791.   501059.    3415      2415466   
5 L           4.73      1.40     2.28         8.48
6 ENTR        1.98      0.429    0.671        2.76
```

## Output file
- [CRQA_Descriptives_Results.csv](https://github.com/Gabbers18/Applied_Project/blob/main/Results/CRQA_Descriptives_Results.csv)

## Interpretting Results

1) <ins>RR: Recurrence Rate<ins>

Low values indicate little to no synchrony; high values indicate strong synchrony. For example, if a dyad has an RR of 18 (the max RR in our data), it likely reflects a highly coordinated interaction.

2) <ins>DET: Determinism<ins>

Higher DET suggests more patterned coordination. For instance, a maximum DET value of 95.81 in our dataset indicates a dyad with highly synchronized, structured behavior—such as mimicking body movements or moving with similar rhythms (e.g., dancing).

3) <ins>NRLINE: Number of Diagonal Lines<ins>

A high maxL value implies a long, uninterrupted period of similarity or synchrony between participants. For example, a maxL of 292 indicates a lengthy stretch of coordinated behavior.

4) <ins>maxL: Maximum Line Length<ins>

This represents the total number of diagonal lines in the CRQA plot. A higher NRLINE, such as our dataset’s maximum of 2,415,466, reflects many repeated behaviors throughout the interaction.

 5) <ins>L: Average Line Length<ins>
 
Higher average line length suggests more predictable and stable behavior. A maximum value of 8.48 indicates consistent coordination between dyad members.

 6) <ins>ENTR: Entropy of Line Lengths<ins>
 
A higher ENTR value (e.g., max = 2.76) reflects more diverse interaction patterns, pointing to complex dyadic dynamics. Lower values imply more regular, predictable behavior.

7) <ins>rENTR: Relative Entropy<ins>

High rENTR values (closer to 1) indicate more complex and variable movement patterns. Lower values suggest more uniformity and predictability.

8) <ins>LAM: Laminarity<ins>

Measures the proportion of recurrence points forming vertical lines—indicative of behavioral stasis. A high LAM value suggests prolonged stable states (e.g., shared stillness or sustained mutual attention).

 9) <ins>TT: Trapping Time<ins>
 
Represents the average duration of stable states (vertical lines). Longer trapping times reflect more sustained coordination. For instance, a TT of 5.14 means dyads stayed in similar states for relatively brief intervals, indicating short-lived stability during interaction.


# Part 4
## Examining Persuasion
We will be examining underlying patterns of persuasion within the interactions by calculating/examining a few measures of persuasion. We will be using **Spearman's Rank Correlation Coefficient** as a measure of the strength and direction of the association between two ranked lists. 

## The Data

Source:
 - For this analysis, we will be using surveyd data
 - [cleaned_qualtrics.csv](https://github.com/Gabbers18/Applied_Project/blob/main/data/cleaned_qualtrics.csv)

## Variables of Interest
- `dyad_number`
- `participant_number`
- individual ranking variables (15 varaibles beginningw with `individual_`)
- group ranking variables (15 varaibles beginningw with `group_`)

## Libraries
- dplyr
- ggplot2
- utils
- tidyr
  
## Step 1: Open Cleaned Qualtrics file

```r
df_cleaned <- read.csv("your_path_to_cleaned_qualtrics.csv")
```

## Step 2: Calculate Individual Change Scores

- using the 'cor' fucntion, we compare survey responses at an individual level to the joint-decison group responses
- see full code chunk in [ChangeScores_Full.Rmd file](https://github.com/Gabbers18/Applied_Project/blob/main/notebooks/ChangeScores_Full.Rmd#L55)

**Example R Code:**
```r
df_change_scores <- df_cleaned %>%
  rowwise() %>% 
  mutate(
    spearman_corr = cor(
      c(individual_jack_knife, individual_flashlight,  etc.),
      c(group_jack_knife, group_flashlight, group_area_map, group_raincoat, etc.),
      method = "spearman", use = "complete.obs"
    )
  )
```
## Step 3: Clean Results
- pivot data longer
- remove incomplete survey data

## Step 4: Calculate Dyad Change Score

- find the difference between participant individual change scores
- `group_by()` the `dyad_number`

**Example R Code:**
```r
df_change_scores <- spearman_data_large %>% 
  group_by(dyad_number) %>%
  mutate(
    dyad_level_change_score = abs(individual_level_change_score[participant_number == 1] - individual_level_change_score[participant_number == 2])
  ) %>%
  ungroup()
```

## Step 5: Check Results
```r
head(df_change_scores)
```

## Example Output
```
  dyad_number participant_number individual_level_change_…¹ dyad_level_change_sc…²
        <int>              <int>                      <dbl>                  <dbl>
1           1                  1                      0.914                  0.154
2           1                  2                      0.761                  0.154
3           2                  2                      0.418                  0.418
4           2                  1                      0.836                  0.418
5           4                  2                      0.539                  0.204
6           4                  1                      0.336                  0.204
```
## Step 6: Save Results as a csv

**Example R Code:**
```r
write.csv(df_change_scores, file = "your_desired_path_location.csv", row.names = FALSE)
```

## Optional Step 7 - Visualize Change Scores

- remove duplicate entries using `filter` or `unique` functions
  
**Example R Code:**
```r
ggplot(df_change_scores2, aes(x = dyad_number, y = dyad_level_change_score)) +
  geom_bar(stat = "identity") +
  labs(title = "Dyad Level Change Score by Dyad Number",
       x = "Dyad Number",
       y = "Dyad Level Change Score") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

## Example Graph - Dyad Level Change Scores
<img width="471" alt="Screenshot 2025-05-09 at 8 45 42 PM" src="https://github.com/user-attachments/assets/3d4a5cbe-ef00-47af-9ec6-5f3c785b4947" />

## Output file
- [Change_Scores.csv](https://github.com/Gabbers18/Applied_Project/blob/main/Results/Change_Scores.csv)

## Interpretting Results
### Individual Change Scores
- A **lower individual change score** suggests greater persuasive influence from their partner.
- A **higher individual change score** suggests little persuasive influence from their partner.
- If individual change scores are **the same** for both participants, this might suggest equal persuasive influence.

### Recurrence Rate (RR) and Dyad Change Score
- **High Dyad-Level Change score (around 1)**
  - Represents the group being 0% in synchrony with their responses being asymmetrical.
  - Low Recurrence rate, meaning the participants are out of sync.
  - One member driving changes without the other engaging or reciprocating.
    
- **A Low Dyad-Level Change score (around 0)**
  - Represents the group being 100% in synchrony with their responses matching perfectly.
  - High Recurrence Rate, meaning the participants are in sync.
  - Changes from both sides equal out.
  - Indicates that the dyad has achieved a high level of alignment, where changes from both sides cancel each other out due to their mirrored timing.


## Troubleshooting

### 1. XQuartz Installation Required for macOS Compatibility

To utilize the `crqa` package on macOS, I needed to install **XQuartz**, as macOS no longer includes X11 by default. This is essential for packages that rely on X11 graphics, such as `crqa` and others like `rgl`.

- **Version Used**: XQuartz 2.8.5 (xorg-server 21.1.6)
- **Note**: After installation, it's advisable to log out and log back in to ensure XQuartz is properly integrated.

### 2. Memory Management Challenges

The `crqa` analyses were memory-intensive, leading to occasional system slowdowns and unresponsiveness. To mitigate these issues:

- **Regularly Saved Progress**: Frequent saving of R data objects (`.RData` files) allowed for easier recovery and continuation of work.
- **Processed Data in Batches**: Dividing large datasets into smaller subsets helped in managing memory usage effectively.
- **Utilized Middle 60% Function**: I devised a function to select the middle of my timeseries data to avoid frequent memory exhastion.


# References

Kleinbub, J. R., & Ramseyer, F. (n.d.). Motion Energy Analysis (MEA). Retrieved May 9, 2025, from https://psync.ch/mea/

Kleinbub, J. R. (n.d.). rMEA: Motion Energy Analysis for R (Version 1.2.2) [R package]. GitHub. https://github.com/kleinbub/rMEA

Coco, M. I., & Dale, R. (n.d.). crqa: Cross Recurrence Quantification Analysis for R (Version 2.0.5) [R package]. GitHub. https://github.com/morenococo/crqa

XQuartz Project. (n.d.). XQuartz: X Window System for macOS (Version 2.8.5; xorg-server 21.1.6) [Computer software]. Retrieved May 9, 2025, from https://www.xquartz.org


