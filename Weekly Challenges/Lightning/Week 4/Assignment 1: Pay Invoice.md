# Assignment 1: Pay Invoice

# Open unannounced channel to lnd2

### LND2

Lightning node P2P address: `boss2025.xyz:9737`

Lightning node pubkey: `03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d`

### LND3

Lightning node P2P address: `boss2025.xyz:9738`

Lightning node pubkey: `02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5`

## Connect to lnd2:

    timrobertson@firefly:~$ lncli -n=signet connect 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d@boss2025.xyz:9737  
    {}  
    timrobertson@firefly:~$ lncli -n=signet listpeers  
    '''
      {  
          "peers": [  
              {  
                  "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                  "address": "178.128.153.52:9737",  
                  "bytes_sent": "641",  
                  "bytes_recv": "31020",  
                  "sat_sent": "0",  
                  "sat_recv": "0",  
                  "inbound": false,  
                  "ping_time": "-1",  
                  "sync_type": "ACTIVE_SYNC",  
                  "features": {  
                      "0": {  
                          "name": "data-loss-protect",  
                          "is_required": true,  
                          "is_known": true  
                      },  
                      "5": {  
                          "name": "upfront-shutdown-script",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "7": {  
                          "name": "gossip-queries",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "8": {  
                          "name": "tlv-onion",  
                          "is_required": true,  
                          "is_known": true  
                      },  
                      "12": {  
                          "name": "static-remote-key",  
                          "is_required": true,  
                          "is_known": true  
                      },  
                      "14": {  
                          "name": "payment-addr",  
                          "is_required": true,  
                          "is_known": true  
                      },  
                      "17": {  
                          "name": "multi-path-payments",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "19": {  
                          "name": "wumbo-channels",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "23": {  
                          "name": "anchors-zero-fee-htlc-tx",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "25": {  
                          "name": "route-blinding",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "27": {  
                          "name": "shutdown-any-segwit",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "31": {  
                          "name": "amp",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "45": {  
                          "name": "explicit-commitment-type",  
                          "is_required": false,  
                          "is_known": true  
                      },  
                      "2023": {  
                          "name": "script-enforced-lease",  
                          "is_required": false,  
                          "is_known": true  
                      }  
                  },  
                  "errors": [],  
                  "flap_count": 1,  
                  "last_flap_ns": "1739578827453442001",  
                  "last_ping_payload": ""  
              }  
          ]  
      }
    '''

## Open channel to lnd2
    '''
    timrobertson@firefly:~$ lncli -n=signet openchannel --node_key=03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d --local_amt=50000 --private
    {  
        "funding_txid": "10519d86c00e09d57e161a89314af4ff75e80d3b22befac323fed162c0bcf3c5"  
    }
    '''

## Check for open or pending channels:
    '''
    timrobertson@firefly:~$ lncli -n=signet listchannels  
    {  
        "channels": []  
    }  
    timrobertson@firefly:~$ lncli -n=signet pendingchannels  
    {  
        "total_limbo_balance": "0",  
        "pending_open_channels": [  
            {  
                "channel": {  
                    "remote_node_pub": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "channel_point": "10519d86c00e09d57e161a89314af4ff75e80d3b22befac323fed162c0bcf3c5:0",  
                    "capacity": "50000",  
                    "local_balance": "46530",  
                    "remote_balance": "0",  
                    "local_chan_reserve_sat": "500",  
                    "remote_chan_reserve_sat": "500",  
                    "initiator": "INITIATOR_LOCAL",  
                    "commitment_type": "ANCHORS",  
                    "num_forwarding_packages": "0",  
                    "chan_status_flags": "",  
                    "private": true,  
                    "memo": "",  
                    "custom_channel_data": ""  
                },  
                "commit_fee": "2810",  
                "commit_weight": "772",  
                "fee_per_kw": "2500",  
                "funding_expiry_blocks": 2016  
            }  
        ],  
        "pending_closing_channels": [],  
        "pending_force_closing_channels": [],  
        "waiting_close_channels": []  
    }
    '''

# Use my lightning node's cli to pay the invoice

## Lightning Invoice:

wallet_325,

lntbs17660n1pneuwuvpp5vs7acps7tuzt0wfqlhgz5rs2cpvvd3q3xjr70yk3fkf4ure0yp7sdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp50kzag0a42e6jcnc2jk4jqv0tfeptah8z36s88d8x0ruh2vp2hkaq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgq7l9jc70mgc67p6z8lz3fky5n9t4mtung8xnwrz52dpa7ur34ahgnc2u23vfmgjeck774ev9hnt3u0yr6jud42caqy2j22evqfsxefscpxez9d8,  
643ddc061e5f04b7b920fdd02a0e0ac058c6c4113487e792d14d935e0f2f207d

## Decode Invoice:
    '''
    timrobertson@firefly:~$ lncli -n=signet decodepayreq lntbs17660n1pneuwuvpp5vs7acps7tuzt0wfqlhgz5rs2cpvvd3q3xjr70yk3fkf4ure0yp7sdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp50kzag0a42e6jcnc2jk4jqv0tfeptah8z36s88d8x0ruh2vp2hkaq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgq7l9jc70mgc67p6z8lz3fky5n9t4mtung8xnwrz52dpa7ur34ahgnc2u23vfmgjeck774ev9hnt3u0yr6jud42caqy2j22evqfsxefscpxez9d8  
    {  
        "destination": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
        "payment_hash": "643ddc061e5f04b7b920fdd02a0e0ac058c6c4113487e792d14d935e0f2f207d",  
        "num_satoshis": "1766",  
        "timestamp": "1738423180",  
        "expiry": "4838400",  
        "description": "wallet_325",  
        "description_hash": "",  
        "fallback_addr": "",  
        "cltv_expiry": "80",  
        "route_hints": [],  
        "payment_addr": "7d85d43fb556752c4f0a95ab2031eb4e42bedce28ea073b4e678f975302abdba",  
        "num_msat": "1766000",  
        "features": {  
            "8": {  
                "name": "tlv-onion",  
                "is_required": true,  
                "is_known": true  
            },  
            "14": {  
                "name": "payment-addr",  
                "is_required": true,  
                "is_known": true  
            },  
            "17": {  
                "name": "multi-path-payments",  
                "is_required": false,  
                "is_known": true  
            },  
            "25": {  
                "name": "route-blinding",  
                "is_required": false,  
                "is_known": true  
            },  
            "263": {  
                "name": "bolt-11-blinded-paths",  
                "is_required": false,  
                "is_known": true  
            }  
        },  
        "blinded_paths": []  
    }
    '''
&nbsp;

## Command to pay invoice:  
    '''
    timrobertson@firefly:~$ lncli -n=signet payinvoice lntbs17660n1pneuwuvpp5vs7acps7tuzt0wfqlhgz5rs2cpvvd3q3xjr70yk3fkf4ure0yp7sdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp50kzag0a42e6jcnc2jk4jqv0tfeptah8z36s88d8x0ruh2vp2hkaq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgq7l9jc70mgc67p6z8lz3fky5n9t4mtung8xnwrz52dpa7ur34ahgnc2u23vfmgjeck774ev9hnt3u0yr6jud42caqy2j22evqfsxefscpxez9d8
    '''

will ask you to confirm payment!  
Payment hash: 643ddc061e5f04b7b920fdd02a0e0ac058c6c4113487e792d14d935e0f2f207d  
Description: wallet_325  
Amount (in satoshis): 1766  
Fee limit (in satoshis): 88  
Destination: 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d  
Confirm payment (yes/no): yes

## Submit invoice preimage for points!!!
    '''
    timrobertson@firefly:~$ lncli -n=signet payinvoice lntbs17660n1pneuwuvpp5vs7acps7tuzt0wfqlhgz5rs2cpvvd3q3xjr70yk3fkf4ure0yp7sdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp50kzag0a42e6jcnc2jk4jqv0tfeptah8z36s88d8x0ruh2vp2hkaq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgq7l9jc70mgc67p6z8lz3fky5n9t4mtung8xnwrz52dpa7ur34ahgnc2u23vfmgjeck774ev9hnt3u0yr6jud42caqy2j22evqfsxefscpxez9d8  
    Payment hash: 643ddc061e5f04b7b920fdd02a0e0ac058c6c4113487e792d14d935e0f2f207d  
    Description: wallet_325  
    Amount (in satoshis): 1766  
    Fee limit (in satoshis): 88  
    Destination: 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d  
    Confirm payment (yes/no): yes  
    +------------+--------------+--------------+--------------+-----+----------+------------------+----------------------+  
    | HTLC_STATE | ATTEMPT_TIME | RESOLVE_TIME | RECEIVER_AMT | FEE | TIMELOCK | CHAN_OUT | ROUTE |  
    +------------+--------------+--------------+--------------+-----+----------+------------------+----------------------+  
    | SUCCEEDED | 0.014 | 0.256 | 1766 | 0 | 7561 | 8219948929318912 | 03ebc7280ff486f94cee |  
    +------------+--------------+--------------+--------------+-----+----------+------------------+----------------------+  
    Amount + fee: 1766 + 0 sat  
    Payment hash: 643ddc061e5f04b7b920fdd02a0e0ac058c6c4113487e792d14d935e0f2f207d  
    Payment status: SUCCEEDED, preimage: 785014b78ee1c5e2c50903765bc81d8d78c986759cf06cc4d4e35aacdc76253a
    '''
&nbsp;

&nbsp;

