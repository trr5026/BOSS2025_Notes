# Week 5: Lightning Route Builder

[Task](https://github.com/BOSS-2025/lightning-route-builder-trr5026/blob/main/README.md)

## Lightning Route Builder Challenge Summary
### Overview
This project was developed for the BOSS-2025 Lightning Route Builder challenge. The objective was to build a Python script that processes a Lightning Network payment route by parsing hop data from an input CSV, decoding a BOLT 11 payment request, calculating HTLC (Hashed Time-Locked Contract) values for each hop, and outputting the results to a CSV file. The script supports multi-path payments, fee computation, and TLV integration.

### Key Features
CSV Parsing: Extracts hop details (path ID, channel name, CLTV delta, base fee, proportional fee) from an input CSV.
BOLT 11 Decoding: Decodes a payment request to retrieve fields like amount (millisatoshis), minimum final CLTV expiry, and payment secret, tailored for regtest network invoices.
HTLC Computation: Calculates HTLC amounts and expiry values per hop, supporting multi-path routing with TLV data (payment secret and total amount) on the final hop of each path.
Fee Calculation: Applies the formula base_fee_msat + floor((amount_msat * proportional_fee_ppm) / 1,000,000) for per-hop fees.
Output Generation: Produces an output.csv file with path ID, channel name, HTLC amount, HTLC expiry, and TLV data.

### Implementation Details
Script Structure: Modular functions include:
parse_csv: Parses hop data.
decode_payment_request: Decodes BOLT 11 invoices using Bech32.
compute_fees: Computes hop fees.
compute_htlc_values_step2: Calculates HTLC values with multi-path support.
write_output_csv: Writes results to CSV.
Dependencies: Uses csv, math, sys, os, datetime, and bech32.
Debugging: Features detailed print statements and local test inputs.

### Key Pain Points
Decoding the Payment Request:
Challenge: This was the most time-intensive part due to the complexity of BOLT 11 invoices. Decoding involved parsing Bech32 strings, extracting timestamp and tagged fields (e.g., payment hash, secret, CLTV expiry), and handling signature verification data. The sheer volume of fields and bit-level conversions made it a steep learning curve.
Solution: Built a robust decode_payment_request function with step-by-step parsing, leveraging bech32 library utilities and custom bit-to-byte conversions.
Computing HTLC Values Backwards:
Challenge: Calculating HTLC values was counterintuitive because it required starting from the last hop and working backwards to the first. Determining which hop’s fee and CLTV delta to apply at each step was confusing—e.g., the final hop has no fee, and fees/CLTV deltas are based on the previous hop in the reverse calculation.
Solution: Implemented compute_htlc_values_step2 to iterate over hops in reverse, carefully tracking next_amount_msat and next_expiry while applying the prior hop’s fee and CLTV delta (except for the last hop). Extensive debugging clarified the logic.

### Outcome
The script successfully processes sample inputs (e.g., inputStep3.csv and a 1µBTC invoice), splits multi-path payments, computes fees and CLTVs, and generates a valid output.csv. It aligns with test case expectations from the challenge repository.

### Usage

    python script.py <output_csv_path> <input_csv_path> <payment_request> <current_block_height>

### Example:

    python script.py ./test output.csv ./test/inputStep3.csv <invoice> 200

    
