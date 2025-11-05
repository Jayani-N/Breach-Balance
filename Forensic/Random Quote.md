# Random Quote (200 pts)

## Desc:

    A file quotes.txt contains many quotes; one line is subtly different...can you recover the flag?



## Solution

    # show the suspicious line with its line number
    sed -n '18p' quotes.txt
    
    # show hex bytes for line 18 (confirms the U+200B byte sequence)
    sed -n '18p' quotes.txt | xxd
    
    # find lines containing the zero-width-space (U+200B)
    grep -nP '\x{200B}' quotes.txt


## Flag

    bab{INVISIBLE_CHAR}
