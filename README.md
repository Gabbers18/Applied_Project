# Applied Project (Fall 2023-Spring 2025)

## Overview
This study utilizes MEA in conjuction with CRQA to conduct continuous data anlaysis on experimental data.

This study seeks to uncover the reasoning behind how people coordinate movements and language through a common task. 
Examining multiple channels simultaneously requires a more “data-driven” approach (Yu et al., 2010). This approach utilizes 
technologies that capture fine-grained multimodal data recorded in real-time, as well as data mining techniques that extract 
meaningful patterns. In the case of this study, we seek to examine the emergence of initiator/responder roles through movement
recurrences utilizing Cross-Recurrence Quantification Analysis (CRQA). Analyses, such as CRQA, are useful in uncovering hidden 
patterns between multiple people that may have been overlooked by human observers. We also utilize Ramseyer's Motion Energy Analysis (MEA)
techniques to extract motion energies of each individual in the task. The goal of these analyses is to identify anomalies or patterns that 
may be important to know for training purposes or within other contexts (Angus et al., 2012).


## Repository Structure

```
Applied_Project/
├── data/                          
│   ├── TxtFiles_cleaned/          # Files derived from MEA
│   └── cleaned_qualtrics.csv/     # Survey & Task data - for calculating change scores
├── notebooks/                     # R Markdown notebooks
│   ├── CRQA_Full.Rmd              # Comprehensive CRQA analysis
│   ├── ChangeScores_Full.Rmd
│   └── Descriptive_Stats.Rmd      # Descriptive statistics of Final_CRQA_Results.csv  
├── results/
│   ├── ALLDataChangeScores.csv                   
│   ├── CRQA_Descriptives_Results.csv                 
│   └── Final_CRQA_Results.csv                  
├── Examples/                      # Example usage scripts
│   ├── Example_CRQA.Rmd         
│   ├── Example_MEA.Rmd
│   ├── README.md     
│   ├── Example_Datasets/
│      ├── MEA_video16.txt
│      ├── dyad_16_MEA.csv
│      └── README.md             
├── LICENSE                        
├── README.md                     
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

## Part 1
### Motion Energy Analysis (MEA)
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
- Useful for visualing the time series data; see [Example_MEA.Rmd diagnostic plots](https://github.com/Gabbers18/Applied_Project/blob/main/Examples/Example_MEA.Rmd)

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

## Section 2
### Cross Recurrence Quantification Analysis (CRQA)
This method is used to capture the dynamics between two people. It is used to quantify patterns in language and movement. It captures non-linear patterns and synchorny over time. 

We will be using this technique in conjuction with the continuous data derived from the MEA. This is time series data between two participants which will be reconstructed into a single, two-dimensional plot.

We will specificaly be examining initiator/responder patterns within this dataset. CRQA is a method that has been used in the literature to examine coupling properties of leader-follower relationships, which is similar to what we are examining.

We will be using Monerno and Coco CRQA R package to conduct this analysis. 

_More information on this package can be found at [this link.](https://github.com/morenococo/crqa)_

## Example CRQA Plot for Dyad 102

<img width="606" alt="Screenshot 2025-03-01 at 9 05 37 PM" src="https://github.com/user-attachments/assets/250322d5-5d7c-40a8-91bf-08cba61b97d7" />

## Final_CRQA_Results.csv  
this is what you use for the descriptive results analyses
## Section 3
### Examining Persuasion
We will be examining underlying patterns of persuasion within the interactions by calculating/examining a few measures of persuasion. We will be using Spearman's Rank Correlation Coefficient as a measure of the strength and direction of the association between two ranked lists. 

**The dataset:**
[cleaned_with_participant_numbers (1) (1).csv.zip](https://github.com/user-attachments/files/17985395/cleaned_with_participant_numbers.1.1.csv.zip)

