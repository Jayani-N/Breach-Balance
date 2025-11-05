# locked PDF(300 pts)

## Desc:

    A password-protected PDF  is given. The password is hidden inside 'ascii_art.txt' using whitespace encoding.
     - Use that password to open the PDF and read the flag

## Solution:

    The whitespace at the start of each line encodes bits (space = 0, tab = 1). I processed each line’s leading whitespace, reversed the bit order per line, left-padded each to 8 bits, converted to bytes and joined the characters. The result is the password:
    letmein

    python script:
    
    # decode_ascii_art.py
    import sys
    
    def decode_file(path):
        with open(path, "rb") as f:
            # preserve raw whitespace bytes reliably
            raw = f.read()
        # decode with surrogateescape so we keep any byte values
        s = raw.decode("utf-8", errors="surrogateescape")
        lines = s.splitlines()
    
        chars = []
        for i, line in enumerate(lines):
            # get leading whitespace (spaces and tabs) only
            leading = line[:len(line) - len(line.lstrip(' \t'))]
            if not leading:
                continue
            # map: space -> '0', tab -> '1'
            bits = ''.join('1' if ch == '\t' else '0' if ch == ' ' else '' for ch in leading)
            # reverse bit order for this line (lsb-first -> msb-first)
            bits_rev = bits[::-1]
            # pad left to 8 bits
            bits_padded = bits_rev.zfill(8)
            val = int(bits_padded, 2)
            chars.append(chr(val))
            # debug print (optional)
            # print(f"Line {i}: leading({len(leading)}) bits={bits} rev={bits_rev} byte={bits_padded} -> {val} ({chr(val)})")
        return ''.join(chars)
    
    if __name__ == "__main__":
        if len(sys.argv) < 2:
            print("Usage: python decode_ascii_art.py ascii_art.txt")
            sys.exit(1)
        pw = decode_file(sys.argv[1])
        print("Recovered password:", pw)


    and unlocking the pdf gives the flag.


## Flag

    bab{PDF_UNLOCKED}
