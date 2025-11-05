# vigenere!!! (200 pts)


## Desc:

     A short ciphertext is encrypted with Vigenère. find the key and decrypt this


## Solution:

          The hint shows the fruit melon. An anagram of melon is lemon, which is a natural candidate for the Vigenère key. Using lemon to decrypt the ciphertext yields readable plaintext.
          
          Python solution:
          
          ct = "gmssapvq_cx"
          key = "lemon"
          
          def vig_decrypt(ct, key):
              res = []
              ki = 0
              for c in ct:
                  if c.isalpha():
                      shift = ord(key[ki % len(key)]) - ord('a')
                      res.append(chr((ord(c) - ord('a') - shift) % 26 + ord('a')))
                      ki += 1
                  else:
                      res.append(c)
              return ''.join(res)
          
          print(vig_decrypt(ct, key))
          # -> vigenere_ok

## Flag:

    bab{vigenere_ok}
