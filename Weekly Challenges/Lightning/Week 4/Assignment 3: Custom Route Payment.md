# Assignment 3: Custom Route Payment

## nodes

LND2  
Lightning node P2P address: boss2025.xyz:9737

Lightning node pubkey: 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d

LND3  
Lightning node P2P address: boss2025.xyz:9738

Lightning node pubkey: 02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5

## Invoice

wallet_325,lntbs32540n1pneu0qjpp5llgu8evc65weq3mstxe7s3wxke7j9qzat9548w29xtzudtlug9msdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp5fzeulg9zsqsr5c9rnzkgu9wfxsfc9dsudv3hj4t0jfyfledpkkyq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgqcskzuugsk8nnjl3ryuh220eu3r43dgrrhqesy8fg52xsk9nlqj4s4590uq305uee2n6nhmrhpygqmsqu5m0ux6pdashny7vvgvx8fncq06kd03,

ffd1c3e598d51d90477059b3e845c6b67d22805d596953b94532c5c6affc4177

## Open unannounced channel to lnd3

First: connect to lnd3 node:
    
    '''
    timrobertson@firefly:~$ lncli -n=signet connect 02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5@boss2025.xyz:9738
    
    lncli -n=signet listpeers
    '''

## Next, open channel:


    timrobertson@firefly:~$ lncli -n=signet openchannel --node_key=02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5 --local_amt=50000 --private  
    {  
        "funding_txid": "546f6d4f7b0d6b5f475fb51275712f3e307dd2dda4de069b53abd5cbcd9aab9a"  
    }



## Check # of confirmations on channel just opened:

    timrobertson@firefly:~$ bitcoin-cli -signet getrawtransaction 546f6d4f7b0d6b5f475fb51275712f3e307dd2dda4de069b53abd5cbcd9aab9a 1 | jq '.confirmations'


## Decode invoice

    timrobertson@firefly:~$ lncli -n=signet decodepayreq lntbs32540n1pneu0qjpp5llgu8evc65weq3mstxe7s3wxke7j9qzat9548w29xtzudtlug9msdqswaskcmr9w30nxv34cqzzsxq9yn4qqsp5fzeulg9zsqsr5c9rnzkgu9wfxsfc9dsudv3hj4t0jfyfledpkkyq9p4gqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqpqysgqcskzuugsk8nnjl3ryuh220eu3r43dgrrhqesy8fg52xsk9nlqj4s4590uq305uee2n6nhmrhpygqmsqu5m0ux6pdashny7vvgvx8fncq06kd03  
    {  
        "destination": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
        "payment_hash": "ffd1c3e598d51d90477059b3e845c6b67d22805d596953b94532c5c6affc4177",  
        "num_satoshis": "3254",  
        "timestamp": "1738423314",  
        "expiry": "4838400",  
        "description": "wallet_325",  
        "description_hash": "",  
        "fallback_addr": "",  
        "cltv_expiry": "80",  
        "route_hints": [],  
        "payment_addr": "48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",  
        "num_msat": "3254000",  
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

## Buildroute command

We need to build this route: myLNDnode -> lnd3 -> lnd2

get channel Id's for lnd2 and lnd3:

LND2  
Lightning node P2P address: boss2025.xyz:9737  
Lightning node pubkey: 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d  
Channel ID: 8219948929318912

LND3  
Lightning node P2P address: boss2025.xyz:9738  
Lightning node pubkey: 02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5  
Channel ID: 8488229766496256

Use buildroute to create a route, then use sendtoroute with route created from buildroute

1. Build route:  
- amount in satoshis (get from invoice) 3254 - see above  
- final_cltv_delta (get from other nodes) 80  
- hops: comma separated hex pubkeys (lnd3 pk, lnd2 pk)  
- outgoing_chan_id: short cannel id of outgoing channel to use for the first hop of the payment (default: 0)   
- payment_addr: hex encoded payment address to set in the last hop's mpp record. Get this from invoice

amount: 3254  
final_cltv_delta: 80  
hops: 02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5, 03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d (lnd3, lnd2)  
outgoing_chan_id: 8488229766496256 (from lnd3)  
payment_addr: 48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588

terminal command:  

    lncli -n=signet buildroute --amt=3254 --final_cltv_delta=80 --hops=02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5,03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d --outgoing_chan_id=8488229766496256 --payment_addr=48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588  
         
Accepted Command:

    timrobertson@firefly:~$ lncli -n=signet buildroute --amt=3254 --final_cltv_delta=80 --hops=02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5,03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d --outgoing_chan_id=8488229766496256 --payment_addr=48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588

JSON Output:  

    {  
        "route": {  
            "total_time_lock": 7885,  
            "total_fees": "1",  
            "total_amt": "3255",  
            "hops": [  
                {  
                    "chan_id": "8488229766496256",  
                    "chan_capacity": "50000",  
                    "amt_to_forward": "3254",  
                    "fee": "1",  
                    "expiry": 7805,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "1003",  
                    "pub_key": "02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5",  
                    "tlv_payload": true,  
                    "mpp_record": null,  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                },  
                {  
                    "chan_id": "6102289534222336",  
                    "chan_capacity": "100000000",  
                    "amt_to_forward": "3254",  
                    "fee": "0",  
                    "expiry": 7805,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "0",  
                    "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "tlv_payload": true,  
                    "mpp_record": {  
                        "payment_addr": "48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",  
                        "total_amt_msat": "3254000"  
                    },  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                }  
            ],  
            "total_fees_msat": "1003",  
            "total_amt_msat": "3255003",  
            "first_hop_amount_msat": "0",  
            "custom_channel_data": ""  
        }  
    }

Payment Hash: ffd1c3e598d51d90477059b3e845c6b67d22805d596953b94532c5c6affc4177

## Assemble sendtoroute command:

&nbsp;

    lncli -n=signet sendtoroute --payment_hash=ffd1c3e598d51d90477059b3e845c6b67d22805d596953b94532c5c6affc4177 --routes='{  
        "route": {  
            "total_time_lock": 7885,  
            "total_fees": "1",  
            "total_amt": "3255",  
            "hops": [  
                {  
                    "chan_id": "8488229766496256",  
                    "chan_capacity": "50000",  
                    "amt_to_forward": "3254",  
                    "fee": "1",  
                    "expiry": 7805,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "1003",  
                    "pub_key": "02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5",  
                    "tlv_payload": true,  
                    "mpp_record": null,  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                },  
                {  
                    "chan_id": "6102289534222336",  
                    "chan_capacity": "100000000",  
                    "amt_to_forward": "3254",  
                    "fee": "0",  
                    "expiry": 7805,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "0",  
                    "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "tlv_payload": true,  
                    "mpp_record": {  
                        "payment_addr": "48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",  
                        "total_amt_msat": "3254000"  
                    },  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                }  
            ],  
            "total_fees_msat": "1003",  
            "total_amt_msat": "3255003",  
            "first_hop_amount_msat": "0",  
            "custom_channel_data": ""  
        }
    
    }'

Preimage: 8cae6321d4dd24d6a5c50ede0feaf04a042e4102e70dd5a6f4a54fc4c2b88daf

## Final Sendtoroute command (successful)

Define route variable

    timrobertson@firefly:~$ Route='{  
        "route": {  
            "total_time_lock": 7886,  
            "total_fees": "1",  
            "total_amt": "3255",  
            "hops": [  
                {  
                    "chan_id": "8488229766496256",  
                    "chan_capacity": "50000",  
                    "amt_to_forward": "3254",  
                    "fee": "1",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "1003",  
                    "pub_key": "02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5",  
                    "tlv_payload": true,  
                    "mpp_record": null,  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                },  
                {  
                    "chan_id": "6102289534222336",  
                    "chan_capacity": "100000000",  
                    "amt_to_forward": "3254",  
                    "fee": "0",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "0",  
                    "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "tlv_payload": true,  
                    "mpp_record": {  
    }' } "custom_channel_data": """0", "3254000"80203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",
    
    text cut off for some reason...



## Verify Route variable is correct


    timrobertson@firefly:~$ echo "$Route"  
    {  
        "route": {  
            "total_time_lock": 7886,  
            "total_fees": "1",  
            "total_amt": "3255",  
            "hops": [  
                {  
                    "chan_id": "8488229766496256",  
                    "chan_capacity": "50000",  
                    "amt_to_forward": "3254",  
                    "fee": "1",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "1003",  
                    "pub_key": "02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5",  
                    "tlv_payload": true,  
                    "mpp_record": null,  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                },  
                {  
                    "chan_id": "6102289534222336",  
                    "chan_capacity": "100000000",  
                    "amt_to_forward": "3254",  
                    "fee": "0",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "0",  
                    "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "tlv_payload": true,  
                    "mpp_record": {  
                        "payment_addr": "48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",  
                        "total_amt_msat": "3254000"  
                    },  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                }  
            ],  
            "total_fees_msat": "1003",  
            "total_amt_msat": "3255003",  
            "first_hop_amount_msat": "0",  
            "custom_channel_data": ""  
        }  
    }


## Assemble sendtoroute command

    timrobertson@firefly:~$ lncli -n=signet sendtoroute --payment_hash=ffd1c3e598d51d90477059b3e845c6b67d22805d596953b94532c5c6affc4177 --routes="$Route"

## Output

    {  
        "attempt_id": "2",  
        "status": "SUCCEEDED",  
        "route": {  
            "total_time_lock": 7886,  
            "total_fees": "1",  
            "total_amt": "3255",  
            "hops": [  
                {  
                    "chan_id": "8488229766496256",  
                    "chan_capacity": "50000",  
                    "amt_to_forward": "3254",  
                    "fee": "1",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "1003",  
                    "pub_key": "02ef09cab9ba578cfa5158d6951148a276cbb005bba5d61b8f630b75769d8df3f5",  
                    "tlv_payload": true,  
                    "mpp_record": null,  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                },  
                {  
                    "chan_id": "6102289534222336",  
                    "chan_capacity": "100000000",  
                    "amt_to_forward": "3254",  
                    "fee": "0",  
                    "expiry": 7806,  
                    "amt_to_forward_msat": "3254000",  
                    "fee_msat": "0",  
                    "pub_key": "03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d",  
                    "tlv_payload": true,  
                    "mpp_record": {  
                        "payment_addr": "48b3cfa0a280203a60a398ac8e15c9341382b61c6b2379556f92489fe5a1b588",  
                        "total_amt_msat": "3254000"  
                    },  
                    "amp_record": null,  
                    "custom_records": {},  
                    "metadata": "",  
                    "blinding_point": "",  
                    "encrypted_data": "",  
                    "total_amt_msat": "0"  
                }  
            ],  
            "total_fees_msat": "1003",  
            "total_amt_msat": "3255003",  
            "first_hop_amount_msat": "3255003",  
            "custom_channel_data": ""  
        },  
    "attempt_time_ns": "1739729497570831389",  
    "resolve_time_ns": "1739729497923469391",  
    "failure": null,  
    "preimage": "8cae6321d4dd24d6a5c50ede0feaf04a042e4102e70dd5a6f4a54fc4c2b88daf"  
}
