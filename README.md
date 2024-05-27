# DES-Algorithm-implementation-

## Description

The DES algorithm is a symmetric-key block cipher used for encrypting and decrypting data. This implementation covers the initial permutation, expansion, key permutation, key rotation, and S-box substitution stages of the DES encryption process.

## Code Overview

### Function: `apply_permutation`

```python
def apply_permutation(input_bits, permutation_table):
    return ''.join(input_bits[i-1] for i in permutation_table)
```

This function applies a permutation to a given input bit string based on a provided permutation table.

### Initial Permutation (IP) Table

```python
IP = [58, 50, 42, 34, 26, 18, 10, 2,
      60, 52, 44, 36, 28, 20, 12, 4,
      62, 54, 46, 38, 30, 22, 14, 6,
      64, 56, 48, 40, 32, 24, 16, 8,
      57, 49, 41, 33, 25, 17, 9, 1,
      59, 51, 43, 35, 27, 19, 11, 3,
      61, 53, 45, 37, 29, 21, 13, 5,
      63, 55, 47, 39, 31, 23, 15, 7]
```

### Example: Initial Permutation of Plaintext

```python
plaintext_bin = "0100100001000001010001110100100001001001010001110100100001000001"
permuted_plaintext = apply_permutation(plaintext_bin, IP)
```

### Expansion Table (E)

```python
E = [32, 1, 2, 3, 4, 5, 4, 5, 6, 7, 8, 9, 8, 9, 10, 11, 12, 13, 12, 13, 14, 15, 16, 17,
     16, 17, 18, 19, 20, 21, 20, 21, 22, 23, 24, 25, 24, 25, 26, 27, 28, 29, 28, 29, 30, 31, 31, 1]
```

### Example: Expansion of the Right Half

```python
right_half = permuted_plaintext[32:]
expanded_right = apply_permutation(right_half, E)
print(expanded_right)
```

### Key Permutation Tables (PC-1 and PC-2) and Shifts

```python
PC1 = [57, 49, 41, 33, 25, 17, 9, 1, 58, 50, 42, 34, 26, 18,
       10, 2, 59, 51, 43, 35, 27, 19, 11, 3, 60, 52, 44, 36,
       63, 55, 47, 39, 31, 23, 15, 7, 62, 54, 46, 38, 30, 22,
       14, 6, 61, 53, 45, 37, 29, 21, 13, 5, 28, 20, 12, 4]
PC2 = [14, 17, 11, 24, 1, 5, 3, 28, 15, 6, 21, 10, 23, 19, 12, 4, 26, 8, 16, 7, 27, 20, 13, 2,
       41, 52, 31, 37, 47, 55, 30, 40, 51, 45, 33, 48, 44, 49, 39, 56, 34, 53, 46, 42, 50, 36, 29, 32]
shifts = [1, 1, 2, 2, 2, 2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 1]
```

### Example: Key Permutation and Rotation

```python
key_bin = "0110001001100101011010000110010101110011011010000111010001101001"
key_pc1 = apply_permutation(key_bin, PC1)
print(key_pc1)

left_key = key_pc1[:28]
right_key = key_pc1[28:]

def rotate_left(key_half, shifts):
    return key_half[shifts:] + key_half[:shifts]

left_key_rot = rotate_left(left_key, shifts[0])
right_key_rot = rotate_left(right_key, shifts[0])
combined_key_rot = left_key_rot + right_key_rot
subkey1 = apply_permutation(combined_key_rot, PC2)
print(subkey1)
```

### XOR Operation

```python
xored = ''.join(str(int(a) ^ int(b)) for a, b in zip(expanded_right, subkey1))
print(xored)
```

### S-Boxes

```python
S_boxes = [
    # Define all 8 S-boxes here as shown in the code
]
```

### Example: Applying S-Boxes

```python
def apply_sbox(input_bits, sbox):
    row = int(input_bits[0] + input_bits[5], 2)  # Convert outer bits to int
    column = int(input_bits[1:5], 2)  # Convert middle 4 bits to int
    sbox_output = sbox[row][column]
    return format(sbox_output, '04b')  # Convert to 4-bit binary

sbox_outputs = []
for i in range(8):
    sbox_input = xored[i*6:(i+1)*6]
    sbox_output = apply_sbox(sbox_input, S_boxes[i])
    sbox_outputs.append(sbox_output)

combined_sbox_output = ''.join(sbox_outputs)
print(combined_sbox_output)
```

### Final Permutation (P)

```python
FINAL = [16, 7, 20, 21, 29, 12, 28, 17,
         1, 15, 23, 26, 5, 18, 31, 10,
         2, 8, 24, 14, 32, 27, 3, 9,
         19, 13, 30, 6, 22, 11, 4, 25]
permuted = apply_permutation(combined_sbox_output, FINAL)
print(permuted)

xored = ''.join(str(int(a) ^ int(b)) for a, b in zip(permuted_plaintext[:32], permuted))
print(xored)
```

## Running the Code

To run this code, simply execute the Python script. The code will output the results of each stage, including the expanded right half, permuted key, subkey, XOR result, S-box outputs, and final permuted result.

This implementation showcases the fundamental operations of the DES encryption algorithm. For a complete DES encryption cycle, additional rounds and final permutations would be required.
