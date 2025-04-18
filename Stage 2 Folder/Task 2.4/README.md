# Task Code 2.4 - Biochemistry & Oncology

## Overview

This code analyses SIFT and FoldX datasets to identify mutations that potentially affect both protein function and structure. It merges the datasets, filters for deleterious mutations based on given criteria, and examines the frequency of amino acids.

## Code Description

The Python code includes the following steps:

1.  **Load Datasets:** Reads the SIFT and FoldX datasets from CSV files into pandas DataFrames.
2.  **Create `specific_Protein_aa` Column:** Creates a unique identifier column by concatenating the 'Protein' and 'Amino_acid' columns in both DataFrames.
3.  **Merge Datasets:** Merges the SIFT and FoldX DataFrames based on the `specific_Protein_aa` column.
4.  **Identify Deleterious Mutations:** Filters the merged DataFrame to find mutations where the SIFT score is below 0.05 and the FoldX score is above 2.
5.  **Find Amino Acid with Most Impact (Simple Approach):** Identifies the original amino acid that appears most frequently in the list of deleterious mutations.
6.  **Generate Amino Acid Frequency Table:** Calculates the frequency of each original amino acid in the SIFT dataset.
7.  **Generate Bar Plot and Pie Chart:** Creates a bar plot and a pie chart to visualise the frequency distribution of the amino acids.

## Expected Output

The code will generate the following output:

1.  Print the head of the loaded SIFT and FoldX DataFrames.
2.  Print the head of the merged DataFrame.
3.  Print the DataFrame containing the identified deleterious mutations.
4.  Print the amino acid that is most frequently observed as the original amino acid in deleterious mutations.
5.  Print the frequency table of all original amino acids in the SIFT dataset.
6.  Display a bar plot showing the frequency of each amino acid.
7.  Display a pie chart showing the proportion of each amino acid.
8.  Placeholder comments indicating where to add observations.


## Python Code

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

sift_df = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/sift.tsv')
foldx_df = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/foldX.tsv')
print(f"Biochemistry & Oncology:")
print(f"SIFT Data Head:{sift_df.head()}")
print(f"FoldX Data Head:{foldx_df.head()}")

sift_df['specific_Protein_aa'] = f"{sift_df['Protein']}_{sift_df['Amino_acid']}"
foldx_df['specific_Protein_aa'] = f"{foldx_df['Protein']}_{foldx_df['Amino_acid']}"

merged_df = pd.merge(sift_df, foldx_df, on='specific_Protein_aa', how='inner')
print(f"Merged Data Head:{merged_df.head()}")

deleterious_mutations = merged_df[(merged_df['SIFT_Score'] < 0.05) & (merged_df['FoldX_Score'] > 2)]
print(f"Deleterious Mutations (SIFT < 0.05 and FoldX > 2):{deleterious_mutations}")

if not deleterious_mutations.empty:
    deleterious_mutations['Original_AA'] = deleterious_mutations['Amino_acid'].str[0]
    most_impacted_aa = deleterious_mutations['Original_AA'].value_counts().idxmax()
    print(f"Amino Acid with Most Frequent Deleterious Mutations (Original): {most_impacted_aa}")
else:
    print("No deleterious mutations found based on the criteria.")

sift_df['Original_AA'] = sift_df['Amino_acid'].str[0]
aa_frequency = sift_df['Original_AA'].value_counts()
print(f"Amino Acid Frequency Table (from SIFT data):{aa_frequency}")

plt.figure(figsize=(12, 5))
plt.subplot(1, 2, 1)
sns.barplot(x=aa_frequency.index, y=aa_frequency.values)
plt.xlabel('Amino Acid')
plt.ylabel('Frequency')
plt.title('Frequency of Amino Acids (SIFT Data)')
plt.subplot(1, 2, 2)
plt.pie(aa_frequency, labels=aa_frequency.index, autopct='%1.1f%%', startangle=90)
plt.title('Proportion of Amino Acids (SIFT Data)')
plt.tight_layout()
plt.show()

print("Observations for Biochemistry & Oncology Task:")
print("add a brief description of the highest impact amino acid")
print("and observations about the structural and functional properties")
print("of frequent amino acids here as comments.")
