# Microbiology - Task Code 2.2

## Overview

This code carries out a simplified analysis of microbial growth curve data, comparing knockout (-) and knock-in (+) strains for different strains. It includes plotting growth curves, estimating the time to reach carrying capacity, and performing a basic statistical comparison.

## Code Description

The Python code includes the following steps:

1.  **Loading the Dataset:** This reads the microbial growth data from a CSV file into a pandas DataFrame.
2.  **Identify Unique Strains:** This extracts the list of unique microbial strains present in the dataset.
3.  **Iterate and Plot Growth Curves:** This loops through each unique strain and plots the OD600 values against Time for both the knockout and knock-in conditions on the same graph.
6.  **Determine Time to Reach Carrying Capacity (Simple Approach):** For each strain and condition, it approximates the time to reach carrying capacity by finding the time point at which the maximum OD600 value is observed.
7.  **Generate Scatter Plot of Time to Reach CC:** Creates a scatter plot to visualise the relationship between the time to reach carrying capacity for knockout and knock-in strains. Each point represents a strain.
8.  **Generate Box Plot of Time to Reach CC:** Generates box plots to show the distribution of the time taken to reach carrying capacity for all knockout strains and all knock-in strains.
9.  **Statistical Difference (Simple Approach - Comparing Means):** Performs an independent samples t-test to compare the mean time to reach carrying capacity between the knockout and knock-in strain groups.

## Expected Output

The code will generate the following output:

1.  Print the head of the loaded DataFrame to show the initial data.
2.  Print the list of unique strains identified in the dataset.
3.  Display a plot showing the growth curves (OD600 vs Time) for each strain, with knockout and knock-in conditions overlaid.
4.  Print a DataFrame showing the approximate time to reach carrying capacity for each strain and condition.
5.  Display a scatter plot comparing the time to reach carrying capacity for knockout and knock-in strains.
6.  Display box plots showing the distribution of the time to reach carrying capacity for knockout and knock-in strains.
7.  Print the results of the independent samples t-test, including the t-statistic and p-value, along with a basic interpretation of the statistical significance.
8.  Placeholder comments indicating where to add observations.


## Python Code

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

df_micro = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/2025_project_collection/refs/heads/main/Python/Dataset/mcgc.tsv', sep='\t')
print("Microbiology Data Head:\n", df_micro.head())

# Create a new DataFrame 
melted_df = pd.melt(df_micro, id_vars=['time'], var_name='well', value_name='OD600')

melted_df[['Strain', 'condition']] = melted_df['well'].str.split('_', expand=True)
melted_df['Mutation'] = melted_df['condition'].str.contains('ki').map({True: '+', False: '-'})

print("\nMelted and Processed Data Head:\n", melted_df.head())

unique_strains = melted_df['Strain'].unique()
print("\nUnique Strains:", unique_strains)

# Plotting Growth Curves
plt.figure(figsize=(12, 8))
for strain in unique_strains:
    knockout_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '-')]
    knockin_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '+')]
    plt.plot(knockout_data['time'], knockout_data['OD600'], label=f'{strain} (-)', marker='o', linewidth=1)
    plt.plot(knockin_data['time'], knockin_data['OD600'], label=f'{strain} (+)', marker='x', linewidth=1)

plt.xlabel('Time')
plt.ylabel('OD600')
plt.title('Growth Curves for Different Strains')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

# Determining Time to Reach Carrying Capacity (Time of Max OD600)
carrying_capacity_times = []
for strain in unique_strains:
    knockout_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '-')]
    knockin_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '+')]
    if not knockout_data.empty:
        time_cc_ko = knockout_data.loc[knockout_data['OD600'].idxmax(), 'time']
        carrying_capacity_times.append({'Strain': strain, 'Mutation': '-', 'Time_to_CC': time_cc_ko})
    if not knockin_data.empty:
        time_cc_ki = knockin_data.loc[knockin_data['OD600'].idxmax(), 'time']
        carrying_capacity_times.append({'Strain': strain, 'Mutation': '+', 'Time_to_CC': time_cc_ki})

cc_times_df = pd.DataFrame(carrying_capacity_times)
print("\nTime to Reach Carrying Capacity (Approximate):\n", cc_times_df)

# Scatter Plot of Time to Reach CC
sns.scatterplot(data=cc_times_df, x='Time_to_CC', y='Strain', hue='Mutation')
plt.title('Time to Reach Carrying Capacity for Knockout vs Knockin Strains')
plt.xlabel('Time to Carrying Capacity')
plt.ylabel('Strain')
plt.tight_layout()
plt.show()

# Box Plot of Time to Reach CC
sns.boxplot(data=cc_times_df, x='Time_to_CC', y='Mutation')
plt.title('Distribution of Time to Reach Carrying Capacity')
plt.xlabel('Time to Carrying Capacity')
plt.ylabel('Mutation Type')
plt.tight_layout()
plt.show()

# Statistical Difference (Simple Approach - Comparing Means)
ko_cc_times = cc_times_df[cc_times_df['Mutation'] == '-']['Time_to_CC'].dropna()
ki_cc_times = cc_times_df[cc_times_df['Mutation'] == '+']['Time_to_CC'].dropna()
if not ko_cc_times.empty and not ki_cc_times.empty:
    t_statistic, p_value = stats.ttest_ind(ko_cc_times, ki_cc_times)
    print(f"\nT-test Results (Time to Carrying Capacity):\nT-statistic: {t_statistic:.3f}, P-value: {p_value:.3f}")
    if p_value < 0.05:
        print("There is a statistically significant difference in the mean time to reach carrying capacity.")
    else:
        print("There is no statistically significant difference in the mean time to reach carrying capacity.")
else:
    print("\nNot enough data to perform statistical test on carrying capacity times.")

print(f"Observations for Microbiology Task:")
print("Add  observations about the growth curves, time to carrying capacity,")
print("scatter plot, box plot, and the statistical test results here as comments.")
