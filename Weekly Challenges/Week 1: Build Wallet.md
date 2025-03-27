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
    
