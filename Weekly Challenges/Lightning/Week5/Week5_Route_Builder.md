# Week 5: Lightning Route Builder

[Task](https://github.com/BOSS-2025/lightning-route-builder-trr5026/blob/main/README.md)

## Lightning Route Builder Challenge Summary
## Overview
This project was developed for the BOSS-2025 Lightning Route Builder challenge. The objective was to build a Python script that processes a Lightning Network payment route by parsing hop data from an input CSV, decoding a BOLT 11 payment request, calculating HTLC (Hashed Time-Locked Contract) values for each hop, and outputting the results to a CSV file. The script supports multi-path payments, fee computation, and TLV integration.

## Key Features
CSV Parsing: Extracts hop details (path ID, channel name, CLTV delta, base fee, proportional fee) from an input CSV.

BOLT 11 Decoding: Decodes a payment request to retrieve fields like amount (millisatoshis), minimum final CLTV expiry, and payment secret, tailored for regtest network invoices.

HTLC Computation: Calculates HTLC amounts and expiry values per hop, supporting multi-path routing with TLV data (payment secret and total amount) on the final hop of each path.

Fee Calculation: Applies the formula base_fee_msat + floor((amount_msat * proportional_fee_ppm) / 1,000,000) for per-hop fees.

Output Generation: Produces an output.csv file with path ID, channel name, HTLC amount, HTLC expiry, and TLV data.

## Implementation Details
Script Structure: Modular functions include:
- parse_csv: Parses hop data.
- decode_payment_request: Decodes BOLT 11 invoices using Bech32.
- compute_fees: Computes hop fees.
- compute_htlc_values_step2: Calculates HTLC values with multi-path support.
- write_output_csv: Writes results to CSV.

Dependencies: Uses csv, math, sys, os, datetime, and bech32.

Debugging: Features detailed print statements and local test inputs.

## Key Pain Points
### Decoding the Payment Request:

Challenge: This was the most time-intensive part due to the complexity of BOLT 11 invoices. Decoding involved parsing Bech32 strings, extracting timestamp and tagged fields (e.g., payment hash, secret, CLTV expiry), and handling signature verification data. The sheer volume of fields and bit-level conversions made it a steep learning curve.
One particular issue I had was converting between 5-bit and 8-bit fields. There is a convertbits method within the bech32 library that would just not work. The solution I found was to implement a method 'bech32toInt', which converted a bech32 field in 5-bit format to an integer, which was super useful for outputting the cltv_expiry and expiration_time. Here is Grok's explanation of that function:

    The bech32toInt function converts a sequence of 5-bit values (from Bech32 decoding) into a single integer. This is critical when decoding fields like cltv_expiry or     expiration_time in a BOLT 11 invoice, where the data is stored as a series of 5-bit groups that need to be interpreted as a base-32 number.

    What’s Happening in the Formula
    The function processes a list of 5-bit integers (bits)—each value ranging from 0 to 31—and computes their combined value as if they were digits in a base-32 number. Here’s how it works step-by-step:
    
    bits Input:
    bits is a list of integers, where each integer is a 5-bit value (0–31) from the Bech32-decoded data.
    Example: [1, 2, 3] could represent three 5-bit groups.
    enumerate(bits):
    This pairs each value in bits with its index (i), starting from 0.
    For [1, 2, 3], it yields (0, 1), (1, 2), (2, 3).
    len(bits) - 1 - i:
    This calculates the "position" of each 5-bit value in a base-32 number, counting from the most significant (leftmost) digit to the least significant (rightmost).
    If len(bits) = 3:
    For i = 0: 3 - 1 - 0 = 2 (highest position)
    For i = 1: 3 - 1 - 1 = 1
    For i = 2: 3 - 1 - 2 = 0 (lowest position)
    32 ** (len(bits) - 1 - i):
    This computes the place value of each digit in base-32.
    Base-32 means each position represents a power of 32:
    Position 2: 32^2 = 1024
    Position 1: 32^1 = 32
    Position 0: 32^0 = 1
    val * (32 ** (len(bits) - 1 - i)):
    Multiplies each 5-bit value (val) by its corresponding base-32 place value.
    For [1, 2, 3]:
    1 * 32^2 = 1 * 1024 = 1024
    2 * 32^1 = 2 * 32 = 64
    3 * 32^0 = 3 * 1 = 3
    sum(...):
    Adds up all the weighted values to get the final integer.
    1024 + 64 + 3 = 1091
    Example Walkthrough
    Let’s say bits = [1, 2, 3]:
    
    Length of bits is 3.
    For each value:
    i = 0, val = 1: 1 * 32^(3-1-0) = 1 * 32^2 = 1 * 1024 = 1024
    i = 1, val = 2: 2 * 32^(3-1-1) = 2 * 32^1 = 2 * 32 = 64
    i = 2, val = 3: 3 * 32^(3-1-2) = 3 * 32^0 = 3 * 1 = 3
    Total: 1024 + 64 + 3 = 1091.
    In base-32 notation, [1, 2, 3] is like the number "123" in base-32, which equals 1 * 32^2 + 2 * 32^1 + 3 * 32^0 = 1091 in decimal.
    
    Why This Instead of bech32.convertbits?
    You mentioned issues with bech32.convertbits. That function converts between bit widths (e.g., 5-bit to 8-bit), but it returns a list of bytes, which might not directly give you the integer value you need for fields like cltv_expiry. Your bech32toInt skips the byte conversion and directly interprets the 5-bit values as a base-32 integer, which aligns with how BOLT 11 encodes numeric fields. It’s a cleaner workaround for your specific use case.
    
    Intuition
    Think of it like converting a base-10 number (e.g., "123" = 1 * 10^2 + 2 * 10^1 + 3 * 10^0 = 123), but here the base is 32 because each 5-bit value can represent 0–31. The formula reverses the order (left-to-right) to match how Bech32 data is structured.






Solution: Built a robust decode_payment_request function with step-by-step parsing, leveraging bech32 library utilities and custom bit-to-byte conversions.

### Computing HTLC Values Backwards:

Challenge: Calculating HTLC values was counterintuitive because it required starting from the last hop and working backwards to the first. Determining which hop’s fee and CLTV delta to apply at each step was confusing—e.g., the final hop has no fee, and fees/CLTV deltas are based on the previous hop in the reverse calculation.
Solution: Implemented compute_htlc_values_step2 to iterate over hops in reverse, carefully tracking next_amount_msat and next_expiry while applying the prior hop’s fee and CLTV delta (except for the last hop). Extensive debugging clarified the logic.

## Outcome
The script successfully processes sample inputs (e.g., inputStep3.csv and a 1µBTC invoice), splits multi-path payments, computes fees and CLTVs, and generates a valid output.csv. It aligns with test case expectations from the challenge repository.

## Usage

    python script.py <output_csv_path> <input_csv_path> <payment_request> <current_block_height>

## Example:

    python script.py ./test output.csv ./test/inputStep3.csv <invoice> 200

    
