# Can You Hear the Secret? (350 pts)


## Desc:

    Someone hid a secret message deep inside this innocent-sounding audio clip.
    At first glance — and even on the spectrogram — it looks like pure noise…
    But those who truly listen carefully may uncover the hidden flag.
    
    You’ll need to analyze the file in both the time and frequency domains.
    The flag format is:
    bab{}


## Solution

```python
#!/usr/bin/env python3
"""
decode_only_from_final.py
Standalone decoder adapted from audio_stego_final.py.

Usage:
    python decode_only_from_final.py stego.wav --seed 999
    python decode_only_from_final.py stego.wav --seed 999 --bit_duration 0.06 --f_low 1500 --f_high 10000 --n_carriers 120 --redundancy 3
"""

import argparse
import numpy as np
import soundfile as sf
from scipy.signal import get_window

def bits_to_bytes(bits: np.ndarray) -> bytes:
    arr = np.packbits(bits.astype(np.uint8))
    return arr.tobytes()

def crc16(data: bytes) -> int:
    crc = 0xFFFF
    for byte in data:
        crc ^= (byte << 8)
        for _ in range(8):
            if crc & 0x8000:
                crc = ((crc << 1) & 0xFFFF) ^ 0x1021
            else:
                crc = (crc << 1) & 0xFFFF
    return crc & 0xFFFF

def verify_and_extract_payload(payload_bytes: bytes):
    if len(payload_bytes) < 3:
        return None
    l = payload_bytes[0]
    if len(payload_bytes) != 1 + l + 2:
        return None
    secret = payload_bytes[1:1+l]
    crc = int.from_bytes(payload_bytes[1+l:1+l+2], 'big')
    if crc != crc16(secret):
        return None
    return secret.decode('utf-8', errors='replace')

def seed32(x: int) -> int:
    return int(x) % (2**32)

def extract_from_audio(stego, sr,
                       seed=12345,
                       bit_duration=0.06,
                       f_low=1500, f_high=10000,
                       n_carriers=120,
                       window_len=1024,
                       redundancy=3,
                       expected_max_payload_bytes=300):
    samples_per_bit = int(round(bit_duration * sr))
    total_samples = len(stego)
    max_bits_possible = total_samples // samples_per_bit
    if max_bits_possible <= 8:
        raise ValueError("Audio too short or bit_duration too long for extraction.")

    carriers = np.linspace(f_low, f_high, n_carriers)
    tt = np.arange(samples_per_bit) / sr

    recovered_bits = []
    for bit_i in range(max_bits_possible):
        start = bit_i * samples_per_bit
        end = start + samples_per_bit
        if end > len(stego):
            break
        slice_sig = stego[start:end]

        rng_seed_val = seed ^ (bit_i * 1315423911)
        rng = np.random.RandomState(seed32(rng_seed_val))
        phases = rng.rand(n_carriers) * 2 * np.pi
        pn = rng.choice([-1.0, 1.0], size=n_carriers)

        ref = np.zeros(samples_per_bit, dtype=np.float32)
        for ci, freq in enumerate(carriers):
            ref += pn[ci] * np.sin(2*np.pi*freq*tt + phases[ci])

        win = get_window("hann", samples_per_bit, fftbins=False)
        ref *= win

        corr = np.dot(slice_sig, ref)
        recovered_bits.append(1 if corr > 0 else 0)

    recovered_bits = np.array(recovered_bits, dtype=np.uint8)

    # Attempt to recover payload using redundancy (collapse repeated payloads)
    bitstream = recovered_bits
    max_payload_bytes = expected_max_payload_bytes

    for payload_bytes in range(1, max_payload_bytes+1):
        bitlen = payload_bytes * 8
        if bitlen > len(bitstream):
            break
        if redundancy > 1 and (bitlen % redundancy == 0):
            per_copy_bits = bitlen // redundancy
            candidate = np.zeros(per_copy_bits, dtype=np.uint8)
            ok = True
            for i in range(per_copy_bits):
                slice_bits = bitstream[i::per_copy_bits][:redundancy]
                if len(slice_bits) < redundancy:
                    ok = False
                    break
                candidate[i] = 1 if np.sum(slice_bits) > redundancy/2 else 0
            if not ok:
                continue
            try:
                b = bits_to_bytes(candidate[:per_copy_bits])
            except Exception:
                continue
            maybe = verify_and_extract_payload(b)
            if maybe is not None:
                return maybe
        else:
            try:
                b = bits_to_bytes(bitstream[:bitlen])
            except Exception:
                continue
            maybe = verify_and_extract_payload(b)
            if maybe is not None:
                return maybe

    # fallback: try first 2048 bits interpreted as bytes
    try:
        b = bits_to_bytes(bitstream[:2048])
        maybe = verify_and_extract_payload(b)
        if maybe is not None:
            return maybe
    except Exception:
        pass

    return None

def main():
    parser = argparse.ArgumentParser(description="Standalone decoder for audio_stego_final")
    parser.add_argument("infile", help="Input stego WAV file")
    parser.add_argument("--seed", type=int, required=True, help="PRNG seed used for embedding")
    parser.add_argument("--bit_duration", type=float, default=0.06)
    parser.add_argument("--f_low", type=float, default=1500.0)
    parser.add_argument("--f_high", type=float, default=10000.0)
    parser.add_argument("--n_carriers", type=int, default=120)
    parser.add_argument("--redundancy", type=int, default=3)
    parser.add_argument("--window_len", type=int, default=1024)
    args = parser.parse_args()

    try:
        sig, sr = sf.read(args.infile, always_2d=True)
    except Exception as e:
        print("Error opening file:", e)
        return

    if sig.ndim == 2 and sig.shape[1] > 1:
        sig = sig.mean(axis=1)
    else:
        sig = sig.flatten()

    print(f"Extracting with seed={args.seed} bit_duration={args.bit_duration} f_low={args.f_low} f_high={args.f_high} n_carriers={args.n_carriers} redundancy={args.redundancy}")
    try:
        secret = extract_from_audio(sig, sr,
                                    seed=args.seed,
                                    bit_duration=args.bit_duration,
                                    f_low=args.f_low, f_high=args.f_high,
                                    n_carriers=args.n_carriers,
                                    window_len=args.window_len,
                                    redundancy=args.redundancy)
    except Exception as e:
        print("Extraction error:", e)
        return

    if secret:
        print("Extracted secret:", secret)
    else:
        print("No valid secret found.")

if __name__ == "__main__":
    main()
```

 Run this python script using 
 
```
python decode.py challaudiofileee.wav --seed 999
 ```

## Flag

    bab{l34rn_t0_l15t3n_c4r3fully!!}
