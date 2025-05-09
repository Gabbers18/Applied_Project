# Applied Project (Fall 2023-Spring 2025)
# Persuasion THrough Synchronization: Examining the Impact of Movement on Persuasion in Social Interactions

#### By: Gabrielle Young; Advisor: Dr. Nicholas Duran

## Overview
This study examines how individuals coordinate movements and language during shared tasks by analyzing real-time multimodal data. Employing Cross-Recurrence Quantification Analysis (CRQA), the research identifies initiator and responder roles through movement patterns. Additionally, Motion Energy Analysis (MEA) techniques are applied to extract motion energies of each participant, aiming to uncover interaction dynamics that may inform training and other applications.

## Research Questions
1) How does movement synchronization affect persuasion?
2) How does movement specialization (initiator/responder roles) affect persuasion?


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
│   ├── ALLDataChangeScores.csv                  
│   ├── CRQA_Descriptives_Results.csv                 
│   └── Final_CRQA_Results.csv                  
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

### Visualizing the frame-by-frame differences in interactions - Example Dyad 40
![gif](https://github.com/user-attachments/assets/882d8317-5555-4003-a48d-c325caadc337)

### Example R code
For .txt files - direct output of MEA

```r
mea_normal <- readMEA("your_folder_path_to_MEA_files", 
sampRate = 25, s1Col = 2, s2Col = 1,
                     s1Name = "Participant2", s2Name = "Participant1", skip=1,
                     idOrder = c("id","session"), idSep="_")
```

### Code explaination
This section utilizes **only** the 'rMEA' library. 

Item descriptions:
- s1Col = what each column should be identified as; in this case, the first column was participant 2 
- s2Col = what each column should be identified as; in this case, the first column was participant 1
- s1Name = corresponding name of column 1
- s2Name = corresponding name of column 2

### Important notes
- The path for this code **must** be a folder, not individual files
- This code utilizes a folder containing **only** .txt files; see [Example_MEA.Rmd](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd) for how to run other file types
- This package is useful for naming columns and parsing columns appropriately
- Useful for visualing the time series data; see [Example_MEA.Rmd](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd)  diagnostic plots

### Structure of mea_normal Output list
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

## Step 1: Determine Inital Parameter(s)

I chose to intially set the following variables:
1) theiler window
2) rescale type
3) radius

**1) Theiler Window**
- Theiler window excludes near-diagonal points from being counted as recurrences
- Set to 0
- 0 means no exclusion of points
- we will use this later when determining embedding dimension

**Example in R**
```r
cross_theiler_window = 0
```
**2) Rescale type**

- Rescale type is set a method of **normalizing** the time series before computing distances
- Initlaly set rescale type to 'mean'

**Example in R**
```r
cross_rescale_type = 'mean'
```
**3) Radius**

In this case I chose a radius of **0.1**. This small value **increases** the sensitivity to recurrent points in this data. This stricter criteria means our analysis will be more sensitive to smaller differences in the data.

**Example in R**

```r
radius = .1
```

## Step 2: Create a Random Sample

First, we will set a seed for reproducibility.

**Example in R:**
```r
set.seed(123)
```

Second, we will take a random sample of 5 dyads.

Sampling Justification:
- Our data contains files between 12,000-21,000 rows, making full-scale computations resource-intensive
- Sampling a smaller subset allows for efficient parameter estimation, without processing the entire dataset.

**Example in R:**
```r
dyads_to_sample <- sample(1:length(mea_normal), 5)
```

## Step 3: Create Functions for Parameter Calculation

We will be calculating
1) Delay
2) Embedding Dimension

### 1) Delay

Delay is a parameter set within CRQA which refer to the time lag between data points used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing their similarity or synchronization.

Delay is a parameter used in CRQA that refers to the **time lag between data points** used to reconstruct the phase space of a time series. It determines how far apart in time the data points are when assessing similarity or synchronization.

### How Delay is Determined:

* Calculated using **Average Mutual Information (AMI)**.
* AMI measures the amount of shared information between the original time series and its delayed version.
* The **first local minimum** in the AMI curve indicates the optimal delay—this is the point where each successive data point provides the most new information.

**Function to calcualte AMI:**

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

### 2) Embedding Dimension

Determines the **number of consecutive data points** used to reconstruct the system’s state space. It captures how many dimensions are needed to unfold the underlying dynamics of the system without overlaps or false trajectories.

### How Embedding Dimension is Determined:

* Typically selected using the **False Nearest Neighbors (FNN)** algorithm.
* FNN identifies the minimum number of dimensions where false neighbors (points that appear close due to projection in low dimensions) are minimized.
* The optimal embedding dimension is where the **percentage of false neighbors drops to near zero or levels off**, meaning the system’s dynamics are well represented.

**Function to caculate FNN**

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

## Step 4: Calculate Delay and Embedding Dimension Parameters

### First, initialize storage for parameters.

- Set up empty vectors to store the computed delay and embedding dimension values for each sampled dyad.
```r
delays <- c()
embeddings <- c()
```

### Second, iterate over our selected sampled dyads.

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


### Third, extract individual participant time series.

- Retrieve the time series data for Participant 1 and Participant 2.

```r
ts_participant1 <- dyad_data$Participant1
ts_participant2 <- dyad_data$Participant2
```

### Fourth, select middle 60% of time series

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

### Fifth: Determine Delay Using Average Mutual Information (AMI)

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

### Sixth: Determine Embedding Dimension Using FNN

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

### Optional: Visualize FNN Results

- Plot the FNN results for each participant to visually assess the embedding dimension selection.

```r
plot(cross_fnn_p1, type = "b", main = paste("Dyad", dyads_to_sample[i], "- FNN P1"))
plot(cross_fnn_p2, type = "b", main = paste("Dyad", dyads_to_sample[i], "- FNN P2"))
```

### Seventh: Average the Parameters

- Average out the calculated delays and embedding dimensions across the five sampled dyads.
- Save these variables as `average_delay` and `average_embedding` to be used later in our CRQA calculations.

```r
average_delay <- round(mean(delays, na.rm = TRUE))
average_embedding <- round(mean(embeddings, na.rm = TRUE))

cat("Average Delay: ", average_delay, "\n")
cat("Average Embedding: ", average_embedding, "\n")
```

## Step 5: Function for All Dyads

### First: Define the Function and Initialize Storage

- Here, we utilize the parameters we have named and calculated previously as inputs.

**Example in R:**
```r
run_crqa_for_dyads <- function(df_list, cross_rescale_type, average_delay, average_embedding, cross_theiler_window) {
  crqa_results <- list()
  # Function continues...
}
```

### Second: Loop Through Each Dyad and Prepare Time Series

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


### Third: Convert Time Series Data into Numeric Vectors

**Example in R:**
```r
ts_participant1s <- unlist(ts_participant1)
ts_participant1s <- as.numeric(ts_participant1s)
ts_participant2s <- unlist(ts_participant2)
ts_participant2s <- as.numeric(ts_participant2s)
```

### Fourth: Trim Time Series Data

- Focus on the central portion to mitigate edge effects using our 60% function

```r
ts_participant1s <- get_middle_60_percent(ts_participant1s)
ts_participant2s <- get_middle_60_percent(ts_participant2s)
```

### Fifth: Rescale Time Series

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

### Sixth: Perform CRQA Analysis

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

### Seventh: Extract and Store CRQA Metrics

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

## Step 6: Using the Function

### First: Define the Data Source and Prepare Batches

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

### Second, Process Each Batch and Apply the CRQA Function

- For each batch of files, the code reads the data, organizes it, and applies the `run_crqa_for_dyads` function.

### Create an empty dataframe to store all results
```r
all_results <- data.frame(Dyad = character(), stringsAsFactors = FALSE)
```

**Loop through each batch:**
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

### Third, View Results

```r
print(all_results)
```

### Fourth, Clean and Export the Final Results

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

* **Flatten List Columns**:

```r
  clean_results <- clean_results %>%
    mutate(across(where(is.list), ~sapply(., function(x) paste(unlist(x), collapse = ", "))))  # Flatten list columns
```

This step converts any list-type columns into character strings for easier handling.

### Fifth: Export the Cleaned Results to CSV

  ```r
  write.csv(clean_results, file = "/Users/gabrielleyoung/Desktop/Final_CRQA_Results_Full_test.csv", row.names = FALSE)
  ```

The final cleaned dataframe is saved as a CSV file for future use.

* **Preview the Results**:

  ```r
  clean_results %>% head(10)
  ```

Displays the first 10 rows of the cleaned results to verify the output.


## Final_CRQA_Results.csv  
this is what you use for the descriptive results analyses
## Section 3
### Examining Persuasion
We will be examining underlying patterns of persuasion within the interactions by calculating/examining a few measures of persuasion. We will be using Spearman's Rank Correlation Coefficient as a measure of the strength and direction of the association between two ranked lists. 

**The dataset:**
[cleaned_with_participant_numbers (1) (1).csv.zip](https://github.com/user-attachments/files/17985395/cleaned_with_participant_numbers.1.1.csv.zip)

