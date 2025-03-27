# Block Builder
Bitcoin miners construct blocks by selecting a set of transactions from their mempool. Each transaction in the mempool:

includes a fee which is collected by the miner if that transaction is included in a block
has a weight, which indicates the size of the transaction
may have one or more parent transactions which are also in the mempool
The miner selects an ordered list of transactions which have a combined weight below the maximum block weight. Transactions with parent transactions in the mempool may be included in the list, but only if all of their parents appear before them in the list.

Naturally, the miner would like to include the transactions that maximize the total fee.

Your task is to write a program which reads a file mempool.csv, with the format:

<txid>,<fee>,<weight>,<parent_txids>

txid is the transaction identifier
fee is the transaction fee
weight is the transaction weight
parent_txids is a list of the txids of the transaction’s immediate parent transactions in the mempool. Ancestors that are not immediate parents (eg parents of parents) and parent transactions that are already in the chain are not included in this list. It is of the form: <txid1>;<txid2>;...
The output from the program should be txids, separated by newlines, which make a valid block, maximizing the fee to the miner. Transactions MUST appear in order. No transaction should appear unless its parents are included, no transaction should appear before its parents, and no transaction may appear more than once.

We've included a block_sample.txt file to demonstrate the expected format.

# Input file
Here are two lines of the mempool.csv file:

2e3da8fbc1eaca8ed9b7c2db9e6545d8ccac3c67deadee95db050e41c1eedfc0,452,1620,
This is a transaction with txid 2e3da8..., fees of 452 satoshis, weight of 1620, and no parent transactions in the mempool.

9d317fb308fd5451fd0ec612165638cb9e37bd8aa8918dff99a48fe05224276f,350,1400,288ea91bb52d8cb28289f4db0d857356622e39e78f33f26bf6df2bbdd3810fad;b5b993bda3c23bdefe4a1cf75b1f7cbdfe43058f2e4e7e25898f449375bb685c;c1ae3a82e52066b670e43116e7bfbcb6fa0abe16088f920060fa41e09715db7d
This is a transaction with txid 9d317f..., fees of 350 satoshis, weight of 1400 and three parent transactions in the mempool with txids 288ea9...., b5b993... and c1ae3a...

# Submission
Each student will get a private fork of this repository when they accept the GitHub Classroom assignment. You will commit and push your submissions to GitHub which will evaluate the answers automatically. You can commit and push as often as you like.

Your code must read the file mempool.csv and write its output file block.txt in the solution directory (./solution/block.txt). We do NOT expect that file to be committed in your repo when you submit.

You are allowed to write your block building code in any of the following programming languages:

Python
C++
Rust
Tip

Autograder WILL NOT score your submission! It will only test your code for errors Your actual score for this challenge will be computed offline, behind the scenes! So you may get a green check from Autograder even if your code generates an invalid block!

# Tip

We can only evaluate submissions committed to your main branch If you do your work on an alternate branch, remember to merge to main for scoring!

The autograder will run the bash script solution/run_builder.sh which MAY BE EDITED BY STUDENTS if you need to install additional dependencies.

The default language for this exercise and the autograder is Python. The easiest way to complete this project is to complete the obfuscated code template in solution/python/builder.py. No other files need to be modified unless you want to start from scratch in Python or write in one of the other languages.

If you choose to write code in something other than Python you MUST modify solution/run_builder.sh as well so that your code is compiled and executed appropriately!

# Hints
The total weight of transactions in a block must not exceed 4,000,000 weight. For this exercise assume that there is no coinbase transaction.
A transaction may only appear in a block if all of its ancestors appear earlier in the block.
A transaction must not appear more than once in the block.
A transaction may have zero, one or many ancestors in the mempool. It may also have zero, one or many children in the mempool.


# Python Script
Block Builder Challenge!
Task: read mempool.csv and parse each line for txid, fee, weight, and parents
Create output file block.txt with a list of txid's that maximize the miner fees.

## Requirements/Constraints:
- Transactions must be in the correct order
- The block must not exceed the maximum block weight of 4,000,000 weight units
- No transaction may appear more than once
- If a transaction has parents, the parent transactions must appear before the child transaction
- Selected transactions must maximize the miner fees

## Output:
- block.txt file with a list of [only] txid's that maximize the miner fees

# Functions

## parse_mempool_csv
  """Parse the CSV file and return a list of MempoolTransactions."""

## create_parent_lookup
    """
    The purpose of this function is to create a dictionary that contains txid, fee, weight, and FWR
    for all parent transactions. We'll need this dict when implementing our fee-maximizing logic.

    Inputs:
    - parent_txids: List of unique parent transaction IDs.
    - transactions: List of all transactions with txid, fee, weight, FWR.

    Returns:
    - parent_lookup: Dictionary {parent_txid: {"fee": int, "weight": int, "FWR": float}}


    """

## sort_transactions_fwr
    """
      Sort transactions by fee/weight ratio. We want the highest fees and lowest weight, so 
      we will sort by highest f/w ration to lowest f/w ratio    
    """

## find_missing_parents
    """
    Checks if all listed parent transactions exist in the dataset. We need to make sure they all exist or else our logic and assumptions 
    won't work.

    Assume: all parents are part of tx list and have associated fees and weights. This information will be considered when
    deciding which transactions to include in the block.
        
    """

## select_transactions_v2 (ignore v1)
    """
        Implements the block selection algorithm based on the flowchart logic. Logic is as follows:
        - Loop through all transactions in sorted_transactions list. Highest FWR transactions are first
        - If a transaction has no parents, include in block
        - If a transaction has parents, check if all parents are already in the block. If yes, include in block
        - If a transaction has parents, but not all parents are already in the block, get the fee, weight, and FWR for all transaction parents
        - Compare all tx parent FWR's to the current tx FWR using a threshold percentage. If all parent FWR's are greater than the threshold percentage,
            include all parents in the block first. Otherwise, skip transaction and move to next one

        Inputs:
        - sorted_transactions: List of transactions sorted by FWR (descending).
        - parent_lookup: Dictionary {parent_txid: {fee, weight, FWR}} for quick lookup.
        - threshold percentage: Threshold percentage of a parent FWR compared to its child tx FWR. Formula: threshold = parent FWR / child FWR

        Returns:
        - final_block: List of txids to include in the block.
    """

## main
    '''# Step 1: Parse the CSV file and create a list (and dict) of transactions
    TransactionDict, TransactionList, ParentTXIDs = parse_mempool_csv()
    # print(TransactionList)

    # Step 2: Check if all parent transactions exist in the dataset
    missing_parents = find_missing_parents(TransactionList)

    if missing_parents:
        print("Warning: csv contains parents not in txid list.")
        

    # Step 3: Create a parent lookup dictionary to store parent txid, fee, weight, and FWR. Will be used for tx selection logic
    Parent_Lookup = create_parent_lookup(TransactionList, ParentTXIDs)

    # Step 4: Sort transactions by fee/weight ratio
    sortedTXs = sort_transactions_fwr(TransactionList)

    # Step 5a: Strategy #1. Call select_transactions function to sort tranasctions into desired block. Return Final block and block info: v1 (skip tx's with parents)
    FinalBlock, blockinfo = select_transactions(sortedTXs, Parent_Lookup)
    # print(f"Block info v1: {blockinfo}")

    # Step 5b: Strategy #2. Add parent tx's not already included based on FWR threshold percentage of child tx. (strategy 2 is better!)
    # Define threshold percentages (in decimal) for parent/child FWR comparison to determine loop iterations
    thresholds = [i/100 for i in range(100)]

    # Store block results for each threshold tested in loop
    results = {}

    # Loop through thresholds and store results
    for threshold in thresholds:
        # Final block and block info: v2
        FinalBlock2, blockinfo2 = select_transactions_v2(sortedTXs, Parent_Lookup, threshold)
        results[threshold] = {"BlockInfo": blockinfo2, "FinalBlock": FinalBlock2}
        # print(f"Block info v2 {round(threshold*100,1)}%: {blockinfo2}")

    # Determine the optimal threshold that maximizes total fees for a block
    OptimalThreshold = max(results, key=lambda x: results[x]["BlockInfo"]["Total Fees"])
    WinningBlockInfo = results[OptimalThreshold]["BlockInfo"]
    WinningBlock = results[OptimalThreshold]["FinalBlock"]

    print(f"Winning Block: {OptimalThreshold*100}%, {WinningBlockInfo}")



    # Write the winning block to block.txt. Use .. before forward slash to write to parent directory (solution)
    FileName = "solution/block.txt"
    
    with open(FileName, 'w') as f:
        for txid in WinningBlock:
            f.write(f"{txid}\n")

    print(f"Winning block written to {FileName}")

    # ******************************VALIDATION************************************
    
    with open(FileName, 'r') as f:
        lines = f.readlines()
    
    # Check if the number of lines in block.txt is equal to the number of transactions in the winning block
    assert len(lines) == len(WinningBlock), "Warning: Number of lines in block.txt does not match the number of transactions in the winning block."
    print(f"Validation 1: Lines in {FileName}: {len(lines)}. Transactions in winning block: {len(WinningBlock)}. Match!")
    
    # Ensure Transactions Appear in Correct Order
    included = set()
    valid_order = True
    
    for txid in WinningBlock:
        parents = Parent_Lookup.get(txid, {}).get("parents", [])
        if any(parent not in included for parent in parents):
            print(f"Invalid order: Tx {txid} appears before its parents {parents}")
            valid_order = False
        included.add(txid)
    
    if valid_order:
        print("Validation 2: Transactions are in correct order!")
    
    #  Verify No Duplicate Transactions
    assert len(set(WinningBlock)) == len(WinningBlock), "Duplicate transactions found in block.txt"
    print("Validation 3: No duplicate transactions found!")
    
    #  Confirm Block Weight is Under 4,000,000
    total_weight = sum(TransactionDict[txid]["weight"] for txid in WinningBlock)
    assert total_weight <= 4000000, f"Block weight too high: {total_weight}"
    print(f"Validation 4: Block weight is {total_weight} (<= 4,000,000)")
    '''


