## base58_decode
    """
    Decodes a Base58 encoded string into its original byte sequence, checking the integrity with a checksum.

    Inputs:
    - base58_string (str): The Base58 encoded string to be decoded.

    Returns:
    - bytes: The original byte sequence represented by the Base58 string, excluding the checksum.

    Raises:
    - Exception: If the checksum does not match, indicating potential data corruption or input error.

    Example:
    >>> encoded_string = '3J98t1WpEZ73CNmQviecrnyiWrnqRhWNLy'
    >>> decoded_bytes = base58_decode(encoded_string)
    >>> decoded_bytes
    b'\\x00\\x1d\\xb2\\x2c\\xf3\\x6a\\x31\\xb9\\xde\\x8a\\x6a\\x22\\x3b\\x6f\\x53\\xee'
    """

## deserialize_key
    """
    Deserializes a byte array into a dictionary representing BIP32 key information.

    This function interprets a binary representation of an extended key (private or public) according to BIP32 specifications 
    and returns a dictionary containing the key information.

    Inputs:
    - b (bytes): A byte array of the serialized key, expected to be exactly 78 bytes long.

    Returns:
    - dict: A dictionary with the deserialized key information including the version, depth, parent fingerprint, 
    child number, chain code, and key data. It also conditionally includes either the 'public_key' or 'private_key' based on the version.

    Raises:
    - Exception: If the byte array is not exactly 78 bytes or if the version is unrecognized.

    Example:
    >>> key_bytes = bytes.fromhex('0488ade4...')
    >>> key_info = deserialize_key(key_bytes)
    >>> key_info['Version']
    '0488ade4'
    """

## get_pub_from_priv
    """
    Derives the secp256k1 compressed public key from a given private key using elliptic curve cryptography.

    Inputs:
    - priv (bytes): The private key in bytes format.

    Returns:
    - bytes: The compressed public key derived from the input private key.

    Example:
    >>> private_key_bytes = bytes.fromhex('d1db2f0e0dfc10ff0dcb589a011d2985e67ab0f6127f5a8e24c6f3af6dd9d2af')
    >>> public_key_bytes = get_pub_from_priv(private_key_bytes)
    >>> public_key_bytes.hex()
    '02818a3f4b3828b2d126ad8571d1ec53db9deac0b7996f69b2da832e8b5cd13494'
    """

## derive_priv_child
    """
    Derives a child private key and chaincode from a given parent private key and chaincode using BIP32 key derivation specifications.

    This function supports both hardened and non-hardened key derivation.

    Inputs:
    - key (bytes): The parent private key as bytes.
    - chaincode (bytes): The chaincode associated with the parent private key.
    - index (int): The child key index. For hardened keys, this index will be modified internally to signify a hardened key derivation.
    - hardened (bool): A boolean indicating whether the derivation should be hardened.

    Returns:
    - dict: A dictionary containing the derived child private key and new chaincode in hexadecimal format.

    Raises:
    - ValueError: If the derived child private key is invalid (e.g., equals zero or greater than the order of the curve).

    Example:
    >>> parent_key = bytes.fromhex('abcd1234...')
    >>> parent_chaincode = bytes.fromhex('1234abcd...')
    >>> index = 0
    >>> hardened = True
    >>> child_key_info = derive_priv_child(parent_key, parent_chaincode, index, hardened)
    >>> child_key_info['Key']
    'abcd1234...'
    """

## get_wallet_privs
    """
    Derives a list of child private keys based on a given BIP32 derivation path.

    This function iterates over a specified derivation path, deriving child private keys for each step in the path.
    It then continues to derive a specified number of child keys (e.g., 2000) from the last key in the path.

    Inputs:
    - key (bytes): The parent private key from which child keys are derived.
    - chaincode (bytes): The parent chain code.
    - path (List[Tuple[int, bool]]): A list of (index, hardened) tuples representing the derivation path.

    Returns:
    - List[bytes]: A list of derived private keys in byte format, each 32 bytes long.

    Raises:
    - Exception: If derivation fails due to invalid key or path conditions.

    Example:
    >>> priv_key = bytes.fromhex('abcd1234...')
    >>> chain_code = bytes.fromhex('1234abcd...')
    >>> path = [(44, True), (0, True), (0, False)]
    >>> priv_keys = get_wallet_privs(priv_key, chain_code, path)
    >>> len(priv_keys)
    2000
    """

## get_p2wpkh_program
    """
    Generates a P2WPKH (Pay-to-Witness-Public-Key-Hash) ScriptPubKey for the given compressed public key.

    This function constructs the scriptPubKey needed for P2WPKH transactions in Bitcoin. It encodes the public key into
    a witness program using a SHA256 followed by a RIPEMD160 hash.

    Inputs:
    - pubkey (bytes): A 33-byte compressed public key.
    - version (int): The witness version (default is 0 for P2WPKH).

    Returns:
    - bytes: The resulting ScriptPubKey, formatted as [0x00] [0x14] [20-byte pubkey hash].

    Raises:
    - ValueError: If the public key is not a 33-byte compressed key or the witness version is not 0.

    Example:
    >>> pubkey = bytes.fromhex('02c0...ab1')
    >>> script = get_p2wpkh_program(pubkey)
    >>> print(script.hex())
    '0014751e76e8199196d454941c45d1b3a323f1433bd6'
    """

## bcli
    """
    Executes a Bitcoin Core command using the bitcoin-cli interface on the Signet test network.

    Inputs:
    - cmd (str): The bitcoin-cli command to execute, passed as a single string.

    Returns:
    - str: The stdout response from bitcoin-cli if the command is executed successfully.

    Example:
    >>> command = "getblockcount"
    >>> block_count = bcli(command)
    >>> print(block_count)
    '200000'
    """

## get_blockhash_from_txid (not template function)
    """
    Search through BlockTXs list of dictionaries to find the blockhash associated with a given txid

    Inputs: 
    - txid (str): transaction id to search for
    - BlockTXs (list): list of dictionaries containing block height, block hash, and txids

    Returns:
    - str: Return blockhash as string

    Example: 
    >>> get_blockhash_from_txid("txid", BlockTXs)
    '000002cb37e17cd821eecc6450a35b084137ff1952a0e345321c2a8fd40f6ebc'
    """

# recover_wallet_state
    """
    Recover the wallet state from the blockchain:
    - Parse tprv and path from descriptor and derive 2000 key pairs and witness programs
    - Request blocks 0-310 from Bitcoin Core via RPC and scan all transactions
    - Return a state object with all the derived keys and total wallet balance

    Inputs:
    - tprv (str): The extended private key in base58 format
    - path (List[Tuple[int, bool]]): The derivation path as a list of (index, hardened) tuples

    Steps:
    - Base58 decode tprv
    - Deserialize key
    - Get private key and chain code
    - Derive 2000 child private keys
    - Derive subsequent public keys for all child private keys
    - Calculate ScriptPubKey (programs) for all public keys
    - Prepare a wallet state data structure
    - Loop through blocks 0-300:
        - identify all tx outputs for our own witness programs
        - identify all tx inputs for our own compressed public keys to subtract spent coins
        - add up all the UTXOs to calculate the total balance
    
    Returns:
    - dict: A dictionary with the wallet state containing balance, privs, pubs, programs, and utxos

    Example:
    >>> recover_wallet_state(Base58_ExtendedPrivateKey, derivation_path)
    wallet_325 24.70030740
    
    """
