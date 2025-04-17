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
8.  Placeholder comments indicating where to add your observations.

## Considerations

* This code assumes the dataset has columns named 'Strain', 'Mutation' (with values '-' for knockout and '+' for knock-in), 'Time', and 'OD600'. You may need to adjust these names based on your actual dataset.
* The method for determining the time to reach carrying capacity is a simplistic approximation based on the time of maximum OD600. More sophisticated methods might involve fitting growth models.
* The statistical test assumes the data meets the assumptions of an independent samples t-test (e.g., approximate normality, independence). It's important to check these assumptions for a rigorous analysis.
* The interpretation of the statistical test is basic. A more detailed analysis would consider the effect size and the biological context.

## Python Code

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

# 1. Load the Dataset
# Assuming your data is in a CSV file named 'microbiology_data.csv'
# Replace 'link_to_dataset.csv' with the actual path or URL
df_micro = pd.read_csv('link_to_dataset.csv')
print("Microbiology Data Head:\n", df_micro.head())

# 2. Identify Unique Strains
unique_strains = df_micro['Strain'].unique() # Assuming 'Strain' is the column name
print("\nUnique Strains:", unique_strains)

# 3. Iterate and Plot Growth Curves
plt.figure(figsize=(10, 6)) # Adjust figure size as needed

for strain in unique_strains:
    knockout_data = df_micro[(df_micro['Strain'] == strain) & (df_micro['Mutation'] == '-')] # Assuming 'Mutation' column
    knockin_data = df_micro[(df_micro['Strain'] == strain) & (df_micro['Mutation'] == '+')]

    plt.plot(knockout_data['Time'], knockout_data['OD600'], label=f'{strain} (-)', marker='o')
    plt.plot(knockin_data['Time'], knockin_data['OD600'], label=f'{strain} (+)', marker='x')

plt.xlabel('Time')
plt.ylabel('OD600')
plt.title('Growth Curves for Different Strains')
plt.legend()
plt.grid(True)
plt.show()

# 4. Determine Time to Reach Carrying Capacity (Simple Approach - Time of Max OD600)
carrying_capacity_times = []
for strain in unique_strains:
    knockout_data = df_micro[(df_micro['Strain'] == strain) & (df_micro['Mutation'] == '-')]
    knockin_data = df_micro[(df_micro['Strain'] == strain) & (df_micro['Mutation'] == '+')]
    time_cc_ko = knockout_data.loc[knockout_data['OD600'].idxmax(), 'Time'] if not knockout_data.empty else None
    time_cc_ki = knockin_data.loc[knockin_data['OD600'].idxmax(), 'Time'] if not knockin_data.empty else None
    carrying_capacity_times.append({'Strain': strain, 'Mutation': '-', 'Time_to_CC': time_cc_ko})
    carrying_capacity_times.append({'Strain': strain, 'Mutation': '+', 'Time_to_CC': time_cc_ki})

cc_times_df = pd.DataFrame(carrying_capacity_times)
print("\nTime to Reach Carrying Capacity (Approximate):\n", cc_times_df)

# 5. Generate Scatter Plot of Time to Reach CC
sns.scatterplot(data=cc_times_df, x='Time_to_CC', y='Strain', hue='Mutation')
plt.title('Time to Reach Carrying Capacity for Knockout vs Knockin Strains')
plt.xlabel('Time to Carrying Capacity')
plt.ylabel('Strain')
plt.show()

# 6. Generate Box Plot of Time to Reach CC
sns.boxplot(data=cc_times_df, x='Time_to_CC', y='Mutation')
plt.title('Distribution of Time to Reach Carrying Capacity')
plt.xlabel('Time to Carrying Capacity')
plt.ylabel('Mutation Type')
plt.show()

# 7. Statistical Difference (Simple Approach - Comparing Means)
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

# Observations as comments will go here in your final code
print("\n# Observations for Microbiology Task:")
print("# You will add your observations about the growth curves, time to reach carrying capacity,")
print("# scatter plot, box plot, and the statistical test results here as comments.")
