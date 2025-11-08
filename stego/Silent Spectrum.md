# Silent Spectrum (200 pts)

## Desc:

    A picture may be worth a thousand words, but this one hides just a few — and they matter. The flag lies somewhere within the pixels, yet you won’t see it with your eyes. Maybe the colors themselves are whispering along a line...
    
    Can you uncover the secret message hidden in the image?
    
    FLAG FORMAT: bab{}


## Solution

```python
from PIL import Image

# Load image
img = Image.open("challenge.png").convert("RGB")
pixels = img.load()

# ---- CONFIG ----
# Change this if you embedded differently
# e.g., diagonal LSB of red channel, 288 bits for 36 characters
num_bits = 288

bits = []
for i in range(num_bits):
    # extract LSB of red channel from pixel (i,i)
    r, g, b = pixels[i, i]
    bits.append(r & 1)

# Convert bit list → text
flag = ""
for i in range(0, len(bits), 8):
    byte = bits[i:i+8]
    flag += chr(int("".join(map(str, byte)), 2))

print("Recovered flag:", flag)
```
Run this script using python filename.py
## Flag

    bab{cyber_peace_secure_governance}
