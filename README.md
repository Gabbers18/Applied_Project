## Applied Project (Fall 2023-Spring 2025)

This study utilizes MEA in conjuction with CRQA to conduct continuous data anlaysis on experimental data.

This study seeks to uncover the reasoning behind how people coordinate movements and language through a common task. 
Examining multiple channels simultaneously requires a more “data-driven” approach (Yu et al., 2010). This approach utilizes 
technologies that capture fine-grained multimodal data recorded in real-time, as well as data mining techniques that extract 
meaningful patterns. In the case of this study, we seek to examine the emergence of initiator/responder roles through movement
recurrences utilizing Cross-Recurrence Quantification Analysis (CRQA). Analyses, such as CRQA, are useful in uncovering hidden 
patterns between multiple people that may have been overlooked by human observers. We also utilize Ramseyer's Motion Energy Analysis (MEA)
techniques to extract motion energies of each individual in the task. The goal of these analyses is to identify anomalies or patterns that 
may be important to know for training purposes or within other contexts (Angus et al., 2012).

## Section 1
### Motion Energy Analysis (MEA)
Motion Energy Analysis was developed by Ramseyer in 2019. MEA is a non-invasive method of developed by  extracting frame-by-frame differences in movement from video data. This method is especially useful when trying to analyze nonverbal behavior. 

The data extracted from this analysis is outputed as txt files. For the purposes of this study, we have chosen to capture entire body movements. Looking within the files, we can see each participant's movement is represented by a singluar column in the txt file. Each txt file represents a dyad.

_More information on the MEA software can be found at [this link.](https://psync.ch/mea/)_

**The dataset:**

[TxtFiles 2.zip](https://github.com/user-attachments/files/17984477/TxtFiles.2.zip)

### Visualizing the frame-by-frame differences in interactions - Example Dyad 40
![gif](https://github.com/user-attachments/assets/882d8317-5555-4003-a48d-c325caadc337)


## Section 2
### Cross Recurrence Quantification Analysis (CRQA)
This method is used to capture the dynamics between two people. It is used to quantify patterns in language and movement. It captures non-linear patterns and synchorny over time. 

We will be using this technique in conjuction with the continuous data derived from the MEA. This is time series data between two participants which will be reconstructed into a single, two-dimensional plot.

We will specificaly be examining initiator/responder patterns within this dataset. CRQA is a method that has been used in the literature to examine coupling properties of leader-follower relationships, which is similar to what we are examining.

We will be using Monerno and Coco CRQA R package to conduct this analysis. 

_More information on this package can be found at [this link.](https://github.com/morenococo/crqa)_

## Example CRQA Plot for Dyad 102

<img width="606" alt="Screenshot 2025-03-01 at 9 05 37 PM" src="https://github.com/user-attachments/assets/250322d5-5d7c-40a8-91bf-08cba61b97d7" />


## Section 3 - Optional inclusion in the GitHub
### Examining Persuasion
We will be examining underlying patterns of persuasion within the interactions by calculating/examining a few measures of persuasion. We will be using Spearman's Rank Correlation Coefficient as a measure of the strength and direction of the association between two ranked lists. 

**The dataset:**
[cleaned_with_participant_numbers (1) (1).csv.zip](https://github.com/user-attachments/files/17985395/cleaned_with_participant_numbers.1.1.csv.zip)


## Section 4
### Conducting the Analysis

