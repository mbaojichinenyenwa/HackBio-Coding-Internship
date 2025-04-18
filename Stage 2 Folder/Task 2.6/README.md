# Task Code 2.6 - Transcriptomics

## Overview

This code performs a basic analysis of RNA-seq data to generate a volcano plot and identify upregulated and downregulated genes based on specified thresholds for log2 fold change and p-value.

## Code Description

The Python code includes the following steps:

1.  **Load the Dataset:** Reads the RNA-seq data from a tab-separated file into a pandas DataFrame.
2.  **Generate Volcano Plot:** Creates a scatter plot with Log2 Fold Change on the x-axis and the negative logarithm (base 10) of the p-value on the y-axis. Threshold lines for significance and fold change are added.
3.  **Determine Upregulated Genes:** Filters the DataFrame to identify genes with a Log2 Fold Change greater than 1 and a p-value less than 0.01.
4.  **Determine Downregulated Genes:** Filters the DataFrame to identify genes with a Log2 Fold Change less than -1 and a p-value less than 0.01.
5.  **Functions of Top 5 Upregulated and Downregulated Genes:** Includes a placeholder comment indicating where to manually research the functions of the top genes using GeneCards.

## Expected Output

The code will generate the following output:

1.  Print the head of the loaded RNA-seq DataFrame.
2.  Display a volcano plot showing the relationship between Log2 Fold Change and significance (-log10(p-value)) for all genes.
3.  Print the head of the DataFrame containing the upregulated genes.
4.  Print the head of the DataFrame containing the downregulated genes.
5.  Placeholder comments indicating where to add the functions of the top genes.


## Python Code

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Load the dataset with correct delimiter (space-delimited)
url = 'https://gist.githubusercontent.com/stephenturner/806e31fce55a8b7175af/raw/1a507c4c3f9f1baaa3a69187223ff3d3050628d4/results.txt'
df_rna = pd.read_csv(url, sep='\s+')

df_rna.columns = df_rna.columns.str.strip()
print(f"Available columns: {df_rna.columns.tolist()}")

# Volcano Plot
plt.figure(figsize=(10, 6))
log2fc = df_rna['log2FoldChange']
pvalue = df_rna['pvalue']
neg_log10_pvalue = -np.log10(pvalue)

plt.scatter(log2fc, neg_log10_pvalue, alpha=0.5)
plt.xlabel('Log2 Fold Change (Log2FC)')
plt.ylabel('-log10(p-value)')
plt.title('Volcano Plot')
plt.axvline(1, color='red', linestyle='--', label='Log2FC = 1')
plt.axvline(-1, color='blue', linestyle='--', label='Log2FC = -1')
plt.axhline(-np.log10(0.01), color='green', linestyle='--', label='p = 0.01')
plt.legend()
plt.grid(True)
plt.show()

df_rna = df_rna.replace([np.inf, -np.inf], np.nan).dropna(subset=['log2FoldChange', 'pvalue'])

# Determine Upregulated and Downregulated Genes
upregulated_genes = df_rna[(df_rna['log2FoldChange'] > 1) & (df_rna['pvalue'] < 0.01)]
downregulated_genes = df_rna[(df_rna['log2FoldChange'] < -1) & (df_rna['pvalue'] < 0.01)]

# Display Top 5
top_up = upregulated_genes.sort_values(by='pvalue').head(5)
top_down = downregulated_genes.sort_values(by='pvalue').head(5)

top_up_genes = top_up['Gene'].tolist()
top_down_genes = top_down['Gene'].tolist()

print(f"Top 5 Upregulated Genes: {top_up_genes}")
print(f"Top 5 Downregulated Genes: {top_down_genes}")

# Functions of Top 5 Upregulated and Downregulated Genes (Manual research on GeneCards needed)
# Observations as comments will go here in final code
