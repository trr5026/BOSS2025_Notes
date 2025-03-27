# Lightning RPC Commands

# create wallet:

user@machine:~$ bitcoin-cli -signet createwallet wallet_325  
{  
  "name": "wallet_325"  
}

* * *

# import descriptor:

descriptor:  
wallet_325: wpkh(tprv8ZgxMBicQKsPcyrUyDu72orgqegsBPZbbayGeTNWSsjUuSc62v5mCnnCky82jUDpPxHnoLcd9abuZ4S2fJhvov3u1TpJ8XTCtKcXjNjZQpb/84h/1h/0h/0/*)#gk2cjxnc

example:  
'[{"desc":"wpkh(tprv8ZgxMBicQKsPeL511efCQGDW1hocWZj4abUE9vG58f3dxtC21e5Q3P123hCZRUd5b4byF7R72WoJSKDvf6vJPu14Au96M2ygkCLm1bSLhj1/84h/1h/0h/0/*)#ua9ymdwq", "timestamp":1, "active":true}]'

Mine:  
'[{"desc":"wpkh(tprv8ZgxMBicQKsPcyrUyDu72orgqegsBPZbbayGeTNWSsjUuSc62v5mCnnCky82jUDpPxHnoLcd9abuZ4S2fJhvov3u1TpJ8XTCtKcXjNjZQpb/84h/1h/0h/0/*)#gk2cjxnc", "timestamp":1, "active":true}]'  
<br/>call:

user@machine:~$ bitcoin-cli -signet -rpcwallet=wallet_325 importdescriptors \ '[{"desc":"wpkh(tprv8ZgxMBicQKsPcyrUyDu72orgqegsBPZbbayGeTNWSsjUuSc62v5mCnnCky82jUDpPxHnoLcd9abuZ4S2fJhvov3u1TpJ8XTCtKcXjNjZQpb/84h/1h/0h/0/*)#gk2cjxnc", "timestamp":1, "active":true}]'  
[  
  {  
    "success": true,  
    "warnings": [  
      "Range not given, using default keypool range"  
    ]  
  }  
]

# Get Wallet descriptors

user@machine:~$ bitcoin-cli -rpcwallet=wallet_325 listdescriptors  
{  
  "wallet_name": "wallet_325",  
  "descriptors": [  
    {  
      "desc": "pkh([419fc333/44h/1h/0h]tpubDCcrv8sExt6XxY4MFFapHhUCeWJnuNUPF8oyMKcam4Fff1HYER2bKWhYQi8SNjAnbuBrx6LLc6hdNbp1K8myEjno5CTuCCDekqai9v6mBKe/0/*)#5zn5u96f",  
      "timestamp": 1739473541,  
      "active": true,  
      "internal": false,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "pkh([419fc333/44h/1h/0h]tpubDCcrv8sExt6XxY4MFFapHhUCeWJnuNUPF8oyMKcam4Fff1HYER2bKWhYQi8SNjAnbuBrx6LLc6hdNbp1K8myEjno5CTuCCDekqai9v6mBKe/1/*)#9kk4ps23",  
      "timestamp": 1739473542,  
      "active": true,  
      "internal": true,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "sh(wpkh([419fc333/49h/1h/0h]tpubDCy9QHq3vnt4Uarwg41iJbRNKpcoXifeUvMV4t1FaKHyGhFqGbbu98tT5FM4QPofRQfJTBSAMHaxco14diMmKDsJbaaf1k3GqVZSo21uP3j/0/*))#x4t9zfrs",  
      "timestamp": 1739473541,  
      "active": true,  
      "internal": false,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "sh(wpkh([419fc333/49h/1h/0h]tpubDCy9QHq3vnt4Uarwg41iJbRNKpcoXifeUvMV4t1FaKHyGhFqGbbu98tT5FM4QPofRQfJTBSAMHaxco14diMmKDsJbaaf1k3GqVZSo21uP3j/1/*))#n59n6kk0",  
      "timestamp": 1739473542,  
      "active": true,  
      "internal": true,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "tr([419fc333/86h/1h/0h]tpubDCcau29vRectGKRZaC1pW6xs1P5aUPC5UUbKcHPHpFeAsEgv8Vsnbn7wGrrWdUjduQ8PFEh7ySxHKLXpL8PRMJqsrEXBJHumoezwo16os2R/0/*)#y9eusckv",  
      "timestamp": 1739473541,  
      "active": true,  
      "internal": false,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "tr([419fc333/86h/1h/0h]tpubDCcau29vRectGKRZaC1pW6xs1P5aUPC5UUbKcHPHpFeAsEgv8Vsnbn7wGrrWdUjduQ8PFEh7ySxHKLXpL8PRMJqsrEXBJHumoezwo16os2R/1/*)#43uaddx5",  
      "timestamp": 1739473542,  
      "active": true,  
      "internal": true,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "wpkh([0c7db3c1/84h/1h/0h]tpubDCDidXU4hoK3WzFfzqx9RFk14wQ7aymtUWqGLyGareEoidKmdSumvmsdrSpJMNoKaZo6B64pnYRwmms5W7116ZoCqRm63TWSgYJ6pzBZAHG/0/*)#yv5vq7gd",  
      "timestamp": 1,  
      "active": true,  
      "internal": false,  
      "range": [  
        0,  
        2327  
      ],  
      "next": 1328,  
      "next_index": 1328  
    },  
    {  
      "desc": "wpkh([419fc333/84h/1h/0h]tpubDCta8pWMXu2f99T4SMV5eMjobbhtH1QxWZEXiK6EhVybWs2uioQJZDfy15RhbRvboQ9vXat6nCbbbL8KHKpkjdJqPDummXkK75sTKBWT54b/0/*)#kfxxsqa5",  
      "timestamp": 1739473541,  
      "active": false,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    },  
    {  
      "desc": "wpkh([419fc333/84h/1h/0h]tpubDCta8pWMXu2f99T4SMV5eMjobbhtH1QxWZEXiK6EhVybWs2uioQJZDfy15RhbRvboQ9vXat6nCbbbL8KHKpkjdJqPDummXkK75sTKBWT54b/1/*)#8ar8d4dv",  
      "timestamp": 1739473542,  
      "active": true,  
      "internal": true,  
      "range": [  
        0,  
        999  
      ],  
      "next": 0,  
      "next_index": 0  
    }  
  ]  
}

# Get wallet address

user@machine:~$ bitcoin-cli -signet -rpcwallet=wallet_325 getnewaddress  
tb1qlzxwfy80xq7ghhgm6q98hesulksjpqe0s7mram

&nbsp;

bitcoin-cli listunspent now works too!

# Get lightning node on-chain address

user@machine:~$ lncli -n=signet newaddress p2wkh  
{  
    "address": "tb1q3r8zln3wjptcxc8c8x0ev7sjtg8vkfak7fr0wr"  
}

# Fund lightning node!

bitcoin-cli -signet -named -rpcwallet=wallet_000 sendtoaddress \  
 address="tb1q3r8zln3wjptcxc8c8x0ev7sjtg8vkfak7fr0wr" amount=0.01 fee_rate=1

&nbsp;don't use spaces or backslashes!   
working command:  
user@machine:~$ bitcoin-cli -signet -named -rpcwallet=wallet_325 sendtoaddress address="tb1q3r8zln3wjptcxc8c8x0ev7sjtg8vkfak7fr0wr" amount=0.01 fee_rate=1  
<br/>response: txid with transaction. Look it up on mempool testnet!  
70371ecc2a2a3fd6189eadbe90359c1e0f65884065cac8b40547779209c042d3

# Confirm transaction

user@machine:~$ bitcoin-cli gettransaction 70371ecc2a2a3fd6189eadbe90359c1e0f65884065cac8b40547779209c042d3  
{  
  "amount": -0.01000000,  
  "fee": -0.00000208,  
  "confirmations": 1,  
  "blockhash": "000002d215f4268477009773cc5d803327534f3d0ac8fe1f6b8bcad8181185d5",  
  "blockheight": 7472,  
  "blockindex": 1,  
  "blocktime": 1739576831,  
  "txid": "70371ecc2a2a3fd6189eadbe90359c1e0f65884065cac8b40547779209c042d3",  
  "wtxid": "3db6bb319df6b9a7199857b00db66dbb45a7eff16afae7a6cd5873e6ec88ac7b",  
  "walletconflicts": [  
  ],  
  "mempoolconflicts": [  
  ],  
  "time": 1739576741,  
  "timereceived": 1739576741,  
  "bip125-replaceable": "no",  
  "details": [  
    {  
      "address": "tb1q3r8zln3wjptcxc8c8x0ev7sjtg8vkfak7fr0wr",  
      "category": "send",  
      "amount": -0.01000000,  
      "vout": 0,  
      "fee": -0.00000208,  
      "abandoned": false  
    }  
  ],  
  "hex": "02000000000102efc8aa7250f8eee98eb1dcdac0e1631136f09514ef15d26d4b33095b6b56e67ff700000000fdffffffb8f9ebe5e200a07f097f3a9ce21f867fd67304c1a804ce0d2d0648b26865037ac700000000fdffffff0240420f000000000016001488ce2fce2e90578360f8399f967a125a0ecb27b6f588890000000000160014c084364ffdc77c3a8c674041f83cc1885e56c0dd0247304402206cee8a516733919e877c00af4fa8a765cedd5bafc9fc9637eca1aec5bbf2a11602205f57d4c0f35cabba9a5d9662953fe5c8c16775d22b39dff59c876cc341a5a4f3012103a5027e6370b292dc5b32f622930a4574598db922d599422c8727fcaa09fa39040247304402205dc7b841d2acdd87eff9a523fca5b50c2bedb749b7cd402b3264b05c84af132a0220369516e7cca79e0f2aef8d831870dca9241400e7bca40f95920c4d7a9935e8520121036a90c1962fd3dbaac32dbd16d21877f5bfe2af29ad29084ce9d03a507930c4e32f1d0000",  
  "lastprocessedblock": {  
    "hash": "000002d215f4268477009773cc5d803327534f3d0ac8fe1f6b8bcad8181185d5",  
    "height": 7472  
  }  
}

# Confirm lnd wallet balance

user@machine:~$ lncli -n=signet walletbalance  
{  
    "total_balance": "1000000",  
    "confirmed_balance": "1000000",  
    "unconfirmed_balance": "0",  
    "locked_balance": "0",  
    "reserved_balance_anchor_chan": "0",  
    "account_balance": {  
        "default": {  
            "confirmed_balance": "1000000",  
            "unconfirmed_balance": "0"  
        }  
    }  
}

&nbsp;
