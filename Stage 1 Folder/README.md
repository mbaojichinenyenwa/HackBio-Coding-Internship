# DNA Translation, Logistic Growth, and String Comparison

This task provides simple Python functions for DNA translation, logistic population growth simulation, and string comparison.

## Functions

### `translate_dna(dna)`

Translates a DNA sequence to a protein sequence.

**Parameters:**

* `dna` (`str`): The DNA sequence.

**Returns:**

* `str`: The protein sequence.

### `logistic_grow(t, cap, rate, lag, exp)`

Simulates logistic population growth.

**Parameters:**

* `t` (`float`): Time.
* `cap` (`float`): Carrying capacity.
* `rate` (`float`): Growth rate.
* `lag` (`float`): Lag phase duration.
* `exp` (`float`): Exponential phase duration.

**Returns:**

* `float`: Population at time `t`.

### `time_to_80(cap, rate, lag, exp)`

Calculates the time to reach 80% of carrying capacity.

**Parameters:**

* `cap` (`float`): Carrying capacity.
* `rate` (`float`): Growth rate.
* `lag` (`float`): Lag phase duration.
* `exp` (`float`): Exponential phase duration.

**Returns:**

* `float`: Time to reach 80% capacity.

### `hamming(s1, s2)`

Calculates the Hamming distance between two strings.

**Parameters:**

* `s1` (`str`): First string.
* `s2` (`str`): Second string.

**Returns:**

* `int`: Hamming distance.

## Usage

1.  Save the Python code below as `main.py`.
2.  Run `python main.py`.

## Python Code (`main.py`)

```python
def translate_dna(dna):
    table = {'TTT': 'F', 'TTC': 'F', 'TTA': 'L', 'TTG': 'L', 'TCT': 'S', 'TCC': 'S', 'TCA': 'S', 'TCG': 'S',
             'TAT': 'Y', 'TAC': 'Y', 'TAA': '*', 'TAG': '*', 'TGT': 'C', 'TGC': 'C', 'TGA': '*', 'TGG': 'W',
             'CTT': 'L', 'CTC': 'L', 'CTA': 'L', 'CTG': 'L', 'CCT': 'P', 'CCC': 'P', 'CCA': 'P', 'CCG': 'P',
             'CAT': 'H', 'CAC': 'H', 'CAA': 'Q', 'CAG': 'Q', 'CGT': 'R', 'CGC': 'R', 'CGA': 'R', 'CGG': 'R',
             'ATT': 'I', 'ATC': 'I', 'ATA': 'I', 'ATG': 'M', 'ACT': 'T', 'ACC': 'T', 'ACA': 'T', 'ACG': 'T',
             'AAT': 'N', 'AAC': 'N', 'AAA': 'K', 'AAG': 'K', 'AGT': 'S', 'AGC': 'S', 'AGA': 'R', 'AGG': 'R',
             'GTT': 'V', 'GTC': 'V', 'GTA': 'V', 'GTG': 'V', 'GCT': 'A', 'GCC': 'A', 'GCA': 'A', 'GCG': 'A',
             'GAT': 'D', 'GAC': 'D', 'GAA': 'E', 'GAG': 'E', 'GGT': 'G', 'GGC': 'G', 'GGA': 'G', 'GGG': 'G'}
    protein = ''
    for i in range(0, len(dna), 3):
        codon = dna[i:i + 3].upper()
        protein += table.get(codon, 'X')
    return protein

def logistic_grow(t, cap, rate, lag, exp):
    if t < lag:
        return 0.01
    t_adj = t - lag
    if t_adj < exp:
        return cap / (1 + ((cap - 0.01) / 0.01) * 2.71828 ** (-rate * t_adj))
    return cap

def time_to_80(cap, rate, lag, exp):
    target = 0.8 * cap
    t = lag
    while logistic_grow(t, cap, rate, lag, exp) < target:
        t += 0.01
    return t

def hamming(s1, s2):
    if len(s1) != len(s2):
        ml = max(len(s1), len(s2))
        s1 = s1.ljust(ml)
        s2 = s2.ljust(ml)
    distance = 0
    for i in range(len(s1)):
        if s1[i] != s2[i]:
            distance += 1
    return distance

print(translate_dna("ATGCGTAC"))
print(logistic_grow(50, 500, 0.3, 10, 30))
print(time_to_80(500, 0.3, 10, 30))
print(hamming("test", "text"))
