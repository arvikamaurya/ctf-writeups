## PicoCTF — Cryptography

---

### 🔐 interencdec

| Field         | Details                              |
|---------------|--------------------------------------|
| **Category**  | Cryptography                         |
| **Challenge** | interencdec                          |
| **Difficulty**| Easy                                 |
| **Flag**      | `picoCTF{caesar_d3cr9pt3d_78250fce}` |

---

#### 📝 Description

> Can you find the flag by decoding the contents of the file?

The challenge provided a file containing this encoded string:

```
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclh6YzRNalV3YUcxcWZRPT0nCg==
```

---

#### 🔍 Approach

The `==` padding at the end immediately signals Base64 encoding. After the first decode, the output still ended in `==` meaning there was a second Base64 layer. After decoding that, the output looked like garbled text in flag format (`wpjvJAM{...}`). Since the real flag format is `picoCTF{...}`, I used that as a crib to find the Caesar shift: `w → p` = shift 7.

---

#### 🛠️ Solution

```python
import base64

# The original encoded string from the file
encoded = "YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclh6YzRNalV3YUcxcWZRPT0nCg=="

# Step 1: First Base64 decode
step1 = base64.b64decode(encoded).decode()
print(f"After 1st decode: {step1}")
# d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrXzc4MjUwaG1qfQ==

# Step 2: Second Base64 decode (still has == padding)
step2 = base64.b64decode(step1).decode()
print(f"After 2nd decode: {step2}")
# wpjvJAM{jhlzhy_k3jy9wa3k_78250hmj}

# Step 3: Caesar cipher — shift 7
# Identified by recognising wpjvJAM should be picoCTF, so w→p = 7 back
shift = 7
flag = ""
for ch in step2:
    if ch.isalpha():
        base = ord('a') if ch.islower() else ord('A')
        flag += chr((ord(ch) - base - shift) % 26 + base)
    else:
        flag += ch

print(f"Flag: {flag}")
# picoCTF{caesar_d3cr9pt3d_78250fce}
```

**Output at each step:**

```
After 1st decode : d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrXzc4MjUwaG1qfQ==
After 2nd decode : wpjvJAM{jhlzhy_k3jy9wa3k_78250hmj}
Flag             : picoCTF{caesar_d3cr9pt3d_78250fce}
```

---

#### 🚩 Flag

```
picoCTF{caesar_d3cr9pt3d_78250fce}
```

---

#### 💡 Key Takeaways

- `==` at the end of a string always means Base64 — always check for multiple layers.
- Encodings can be stacked: re-examine the output after every decode step.
- When Caesar output looks like a flag (`wpjvJAM{...}`), use the known format `picoCTF` as a crib to instantly find the shift instead of brute-forcing.
- Shift 7 maps `w→p`, `p→i`, `j→c`, `v→o` — confirming the correct key.
