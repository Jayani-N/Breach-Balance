# nibble_swap(300 pts)


## Desc:

     A hex string was created from the ASCII bytes of the secret, but each byte's nibbles were swapped (e.g., 0xAB -> 0xBA).
     File: cipher_hex.txt
    
    format: bab{<secret_found>}

## Solution:

    Each pair of hex characters is one byte. The challenge swapped the high nibble and low nibble inside every byte. To reverse it, for every byte XY (hex), swap the characters to YX, then convert the resulting hex stream to ASCII.
    
    PYTHON CODE:

    s = open('cipher_hex.txt').read().strip()                # read hex string
    # pair into bytes, swap each byte's nibbles, join, then decode
    decoded = bytes.fromhex(''.join(b[1]+b[0] for b in (s[i:i+2] for i in range(0,len(s),2)))).decode()
    print(decoded)

    


## Flag:

    bab{nibble_ok}
