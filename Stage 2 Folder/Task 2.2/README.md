# Task Code 2.2 - Microbiology  


### Overview

This Python script carries out a basic analysis of microbial growth curve data obtained from a TSV file. It aims to organise the data by strain and mutation type (assuming a pattern in the well names), visualise the growth curves, estimate the time taken to reach maximum growth, and perform a simple statistical comparison between knockout and knock-in strains.

### Script Description

The script performs the following steps:

1.  **Loading the Data:** Reads the microbial growth data from a TSV file hosted online using the pandas library. The first few rows of the loaded data are printed to the console.

2.  **Organising the Data:** The data is then reshaped from a wide format (where each well is a column) to a long format, creating a new DataFrame (`melted_df`) with columns for 'time', 'well', and 'OD600'.

3.  **Assigning Strain and Mutation:** Based on a presumed pattern in the 'well' names (first letter for strain, number for mutation type), new 'Strain' and 'Mutation' columns are added to the `melted_df`.
    * Wells starting with 'A' are assigned 'StrainA'.
    * Wells starting with 'B' are assigned 'StrainB'.
    * Wells starting with 'C' are assigned 'StrainC'.
    * Wells with numbers '1' to '6' are assigned the mutation type '-'.
    * Wells with numbers '7' to '12' are assigned the mutation type '+'.

4.  **Cleaning the Data:** Rows where the 'Strain' or 'Mutation' could not be determined based on the pattern are removed. The first few rows of the processed data are printed.

5.  **Identifying Unique Strains:** The script identifies and prints the unique microbial strains present in the processed data.

6.  **Plotting Growth Curves:** For each unique strain, the script generates a plot showing the OD600 values over time, distinguishing between the '-' (knockout) and '+' (knock-in) mutation types. These plots are displayed on the screen.

7.  **Estimating Time to Maximum Growth:** A simple estimation of the time taken to reach maximum growth is calculated for each strain and mutation type by finding the time point at which the highest OD600 value was recorded. These times are presented in a DataFrame.

8.  **Visualising Time to Maximum Growth:** The estimated times to maximum growth are visualised using a scatter plot (comparing knockout and knock-in for each strain) and a box plot (showing the distribution of these times for each mutation type across all strains). These plots are displayed.

9.  **Simple Statistical Comparison:** An independent samples t-test is performed to compare the average time to maximum growth between the knockout and knock-in groups. The results of the t-test (t-statistic and p-value) are printed, along with a basic interpretation of the statistical significance.

10. **Observations:** A placeholder is included in the output to remind the user to add their observations and interpretations of the results.


### Python Code:

```python

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

df_micro = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/2025_project_collection/refs/heads/main/Python/Dataset/mcgc.tsv', sep='\t')
print(f"Data:{df_micro.head()}")

melted_df = pd.melt(df_micro, id_vars=['time'], var_name='well', value_name='OD600').copy()

melted_df['Strain'] = None
melted_df['Mutation'] = None

for index, row in melted_df.iterrows():
    well = row['well']
    letter = well[0]
    number_str = well[1:]

    if letter == 'A':
        melted_df.loc[index, 'Strain'] = 'StrainA'
    elif letter == 'B':
        melted_df.loc[index, 'Strain'] = 'StrainB'
    elif letter == 'C':
        melted_df.loc[index, 'Strain'] = 'StrainC'

    if number_str in ['1', '2', '3', '4', '5', '6']:
        melted_df.loc[index, 'Mutation'] = '-'
    elif number_str in ['7', '8', '9', '10', '11', '12']:
        melted_df.loc[index, 'Mutation'] = '+'

melted_df = melted_df.dropna(subset=['Strain', 'Mutation'])
print(f"Processed Data:{melted_df.head()}")

unique_strains = melted_df['Strain'].unique()
print(f"Strains: {unique_strains}")

plt.figure(figsize=(10, 6))
for strain in unique_strains:
    knockout = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '-')]
    knockin = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '+')]
    plt.plot(knockout['time'], knockout['OD600'], label=f'{strain} (-)', marker='o')
    plt.plot(knockin['time'], knockin['OD600'], label=f'{strain} (+)', marker='x')
plt.xlabel('Time')
plt.ylabel('OD600')
plt.title('Growth of Different Strains')
plt.legend()
plt.grid(True)
plt.show()

carrying_capacity_times = []
for strain in unique_strains:
    ko_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '-')]
    ki_data = melted_df[(melted_df['Strain'] == strain) & (melted_df['Mutation'] == '+')]
    time_ko = ko_data.loc[ko_data['OD600'].idxmax(), 'time'] if not ko_data.empty else None
    time_ki = ki_data.loc[ki_data['OD600'].idxmax(), 'time'] if not ki_data.empty else None
    carrying_capacity_times.append({'Strain': strain, 'Mutation': '-', 'Time_to_CC': time_ko})
    carrying_capacity_times.append({'Strain': strain, 'Mutation': '+', 'Time_to_CC': time_ki})
cc_times_df = pd.DataFrame(carrying_capacity_times)
print(f"Time to Max Growth:{cc_times_df}")

sns.scatterplot(data=cc_times_df, x='Time_to_CC', y='Strain', hue='Mutation')
plt.title('Time to Max Growth for Knockout vs Knockin')
plt.xlabel('Time to Max Growth')
plt.ylabel('Strain')
plt.show()

sns.boxplot(data=cc_times_df, x='Time_to_CC', y='Mutation')
plt.title('Distribution of Time to Max Growth')
plt.xlabel('Time to Max Growth')
plt.ylabel('Mutation Type')
plt.show()

ko_times = cc_times_df[cc_times_df['Mutation'] == '-']['Time_to_CC'].dropna()
ki_times = cc_times_df[cc_times_df['Mutation'] == '+']['Time_to_CC'].dropna()
if not ko_times.empty and not ki_times.empty:
    t_stat, p_val = stats.ttest_ind(ko_times, ki_times)
    print(f"T-test Results:T-statistic: {t_stat:.3f}, P-value: {p_val:.3f}")
    if p_val < 0.05:
        print(f"Difference in average time to max growth is likely significant.")
    else:
        print(f"No strong evidence of difference in average time to max growth.")
else:
    print(f"Not enough data for statistical test on max growth times.")

print(f"# Observations:")
print(f"# Add your notes about the plots and numbers here.")
