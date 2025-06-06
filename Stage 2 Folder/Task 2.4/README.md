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

sift_df = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/sift.tsv', sep='\t')
foldx_df = pd.read_csv('https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/foldX.tsv', sep='\t')

print(f"SIFT Data Columns: {list(sift_df.columns)}")
print(f"FoldX Data Columns: {list(foldx_df.columns)}")

# Getting the exact column names
sift_combined_col = list(sift_df.columns)[0]
foldx_combined_col = list(foldx_df.columns)[0]

try:
    sift_split = sift_df[sift_combined_col].astype(str).str.split(' ', n=2, expand=True)
    # Assign the split parts, handling potential missing columns with .get()
    sift_df['Protein'] = sift_split.get(0)
    sift_df['Amino_Acid'] = sift_split.get(1)
    sift_df['sift_Score_temp'] = sift_split.get(2)
    sift_df = sift_df.drop(columns=[sift_combined_col])
    print(f"SIFT Data Head after split attempt: {sift_df.head()}")
except ValueError as e:
    print(f"Error splitting SIFT DataFrame: {e}")

try:
    foldx_split = foldx_df[foldx_combined_col].astype(str).str.split(' ', n=2, expand=True)
    # Assign the split parts, handling potential missing columns with .get()
    foldx_df['Protein'] = foldx_split.get(0)
    foldx_df['Amino_Acid'] = foldx_split.get(1)
    foldx_df['foldX_Score_temp'] = foldx_split.get(2)
    foldx_df = foldx_df.drop(columns=[foldx_combined_col])
    print(f"FoldX Data Head after split attempt: {foldx_df.head()}")
except ValueError as e:
    print(f"Error splitting FoldX DataFrame: {e}")

if 'Protein' in sift_df.columns and 'Amino_Acid' in sift_df.columns and 'sift_Score_temp' in sift_df.columns and \
   'Protein' in foldx_df.columns and 'Amino_Acid' in foldx_df.columns and 'foldX_Score_temp' in foldx_df.columns:

    sift_df['SIFT_Score'] = sift_df['sift_Score_temp']
    foldx_df['FoldX_Score'] = foldx_df['foldX_Score_temp']
    sift_df = sift_df.drop(columns=['sift_Score_temp'])
    foldx_df = foldx_df.drop(columns=['foldX_Score_temp'])

    sift_df['specific_Protein_aa'] = f"{sift_df['Protein']}_{sift_df['Amino_Acid']}"
    foldx_df['specific_Protein_aa'] = f"{foldx_df['Protein']}_{foldx_df['Amino_Acid']}"

    # Merge Datasets
    merged_df = pd.merge(sift_df, foldx_df, on='specific_Protein_aa', how='inner')
    print(f"Merged Data Head:{merged_df.head()}")

    # Identify Deleterious Mutations
    deleterious_mutations = merged_df[(merged_df['SIFT_Score'].astype(float) < 0.05) & (merged_df['FoldX_Score'].astype(float) > 2)]
    print(f"Deleterious Mutations (SIFT < 0.05 and FoldX > 2):{deleterious_mutations}")

    if not deleterious_mutations.empty:
        deleterious_mutations['Original_AA'] = deleterious_mutations['Amino_Acid'].str[0]
        most_impacted_aa = deleterious_mutations['Original_AA'].value_counts().idxmax()
        print(f"Amino Acid with Most Frequent Deleterious Mutations (Original): {most_impacted_aa}")
    else:
        print("No deleterious mutations found based on the criteria.")

    # Amino Acid Frequency Table
    sift_df['Original_AA'] = sift_df['Amino_Acid'].str[0]
    aa_frequency = sift_df['Original_AA'].value_counts()
    print(f"Amino Acid Frequency Table (from SIFT data):{aa_frequency}")

    # Bar Plot and Pie Chart
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
