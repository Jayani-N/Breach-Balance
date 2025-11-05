# Zipped Up (250 pts)


## Desc:

    My friend sent me this cool picture, but all I see is a corrupted file. He said he 'zipped up' his flag inside, but I can't figure out how
     to open it. Can you take a look?
     
     NOTE (for this chall only):  flag format- flag{}


## Solution:

     $ file challenge.png
     challenge.png: Zip archive data, at least v2.0 to extract
     This confirms it's a ZIP file, not a PNG.
     4. Find the Password (The "Hint"): The player, knowing it's a file challenge, runs strings on it to look for human-readable text.
     $ strings challenge.png
     PK
     ... (lots of garbled text) ...
     flag.txt
     ... (more garbled text) ...
     I forgot my password, but I'm sure I'll remember it. I just love zip files so much!
     The player sees the hint: "I just love zip files so much!". This strongly suggests the password is ilovezipfiles .
     5. Extract the Flag:
    First, they rename the file to have the correct extension:
     mv challenge.png challenge.zip
     Next, they try to unzip it:
     unzip challenge.zip
     The terminal prompts them for a password:
     Archive:  challenge.zip
     [challenge.zip] flag.txt password:
     They enter the password they found: ilovezipfiles
     It successfully extracts:
     inflating: flag.txt
     6. Get the Flag: The player reads the new flag.txt file.
     $ cat flag.txt
     flag{f1l3_typ3s_c4n_b3_d3c31v1ng}

## Flag

    flag{f1l3_typ3s_c4n_b3_d3c31v1ng}
