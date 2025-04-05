# DNA Translation, Logistic Growth, and String Comparison

## Overview

This project provides simple Python functions for DNA translation, logistic population growth simulation, and string comparison.

## Code Description

The Python code includes the following four functions:

1.  **`translate_dna(dna)`:** Translates a DNA sequence to a protein sequence using a codon-amino acid mapping.
2.  **`logistic_grow(t, cap, rate, lag, exp)`:** Simulates logistic population growth at a given time point, considering lag and exponential phases.
3.  **`time_to_80(cap, rate, lag, exp)`:** Calculates the time it takes for a population to reach 80% of its carrying capacity.
4.  **`hamming(s1, s2)`:** Calculates the Hamming distance between two input strings (Slack username and Twitter handle).
Slack username: Chinenyenwa
Twitter handle: nenyenwa


## Expected Output

When the provided Python code is executed, it will produce the following output:

1.  The protein sequence corresponding to the input DNA sequence "ATGCGTAC".
2.  The population size at time `t=50` using the logistic growth function with given parameters.
3.  The time it takes to reach 80% of the carrying capacity using the logistic growth function with given parameters.
4.  The Hamming distance between the strings "Chinenyenwa" and "nenyenwa".

## Considerations

* The `translate_dna` function assumes the input DNA sequence is a multiple of 3, as it processes codons (3-base sequences).
* The `logistic_grow` function uses a simplified logistic growth model, approximating Euler's number.
* The `hamming` function pads shorter strings with spaces to ensure equal length before calculating the Hamming distance.
* The code is designed for simplicity and clarity, and may not be optimised for performance in large-scale applications.
* The example names used in the hamming function are "Chinenyenwa" and "nenyenwa", the code will work with any two strings.

## Python Code

```
def translate_dna(dna):
    table = { 
        'TTT': 'F', 'TTC': 'F', 'TTA': 'L', 'TTG': 'L', 'TCT': 'S', 'TCC': 'S', 'TCA': 'S', 'TCG': 'S',
        'TAT': 'Y', 'TAC': 'Y', 'TAA': '*', 'TAG': '*', 'TGT': 'C', 'TGC': 'C', 'TGA': '*', 'TGG': 'W',
        'CTT': 'L', 'CTC': 'L', 'CTA': 'L', 'CTG': 'L', 'CCT': 'P', 'CCC': 'P', 'CCA': 'P', 'CCG': 'P',
        'CAT': 'H', 'CAC': 'H', 'CAA': 'Q', 'CAG': 'Q', 'CGT': 'R', 'CGC': 'R', 'CGA': 'R', 'CGG': 'R',
        'ATT': 'I', 'ATC': 'I', 'ATA': 'I', 'ATG': 'M', 'ACT': 'T', 'ACC': 'T', 'ACA': 'T', 'ACG': 'T',
        'AAT': 'N', 'AAC': 'N', 'AAA': 'K', 'AAG': 'K', 'AGT': 'S', 'AGC': 'S', 'AGA': 'R', 'AGG': 'R',
        'GTT': 'V', 'GTC': 'V', 'GTA': 'V', 'GTG': 'V', 'GCT': 'A', 'GCC': 'A', 'GCA': 'A', 'GCG': 'A',
        'GAT': 'D', 'GAC': 'D', 'GAA': 'E', 'GAG': 'E', 'GGT': 'G', 'GGC': 'G', 'GGA': 'G', 'GGG': 'G'
    }
    protein = ''
    for i in range(0, len(dna), 3):  
        codon = dna[i:i + 3].upper() # Extracts each codon.
        protein += table.get(codon, 'X')  # Add amino acid or 'X' if unknown
    return protein

def logistic_grow(t, cap, rate, lag, exp):
    if t < lag:  # Lag phase
        return 0.01
    t_adj = t - lag 
    if t_adj < exp:
        return cap / (1 + ((cap - 0.01) / 0.01) * 2.71828 ** (-rate * t_adj))
    return cap 

def time_to_80(cap, rate, lag, exp):
    target = 0.8 * cap
    t = lag # start at lag time
    while logistic_grow(t, cap, rate, lag, exp) < target:
        t += 0.01
    return t

def hamming(s1, s2):
    """Calculates Hamming distance."""
    if len(s1) != len(s2):
        ml = max(len(s1), len(s2)) 
        s1 = s1.ljust(ml) # to pad shorter string
        s2 = s2.ljust(ml) 
    distance = 0
    for i in range(len(s1)): 
        if s1[i] != s2[i]:
            distance += 1
    return distance


print(translate_dna("ATGCGTAC"))
print(logistic_grow(50, 500, 0.3, 10, 30))
print(time_to_80(500, 0.3, 10, 30))
print(hamming("Chinenyenwa", "nenyenwa"))
