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
