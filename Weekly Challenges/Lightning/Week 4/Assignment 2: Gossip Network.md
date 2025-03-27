# Assignment 2: Gossip Network

## Bash Script 
The key to this assignment is using BOTH channel id and node pubkey to identify which node within a channel we want to extract the fee_base_msat rate. Once we've identified the correct node, it's simply a matter of using jq to extract the node#_policy.fee_base_msat. See below for solution. Got em!
    
    '''
    #!/bin/bash
    # Fill in lncli commands to lookup the base fee of the node below broadcast for the channel id provided.
    
    node="03ebc7280ff486f94cee51cff45a0fc521e9fe2560c02563ab191552fa90d4017d"
    channel_id="6102289534222336"
    
    # This lncli command allows the script to run against signet nodes for testing purposes, refer to it using $lncli.
    # You may comment it out and use your own testing node if you would like, but this line *must* be uncommented when you push to test.
    wd=$(pwd)
    lncli="lncli -n=signet --rpcserver=boss2025.xyz:10007 --tlscertpath="$wd"/test/lnd2-tls.cert --macaroonpath="$wd"/test/lnd2-readonly.macaroon "
    
    # Use local node for testing script. Comment out for submission!
    # lncli="lncli -n=signet --tlscertpath=$HOME/.lnd/tls.cert --macaroonpath=$HOME/.lnd/data/chain/bitcoin/signet/readonly.macaroon"
    
    # Get channel info
    channel_info=$($lncli getchaninfo "$channel_id")
    
    # Extract node1 and node2 pubkeys
    node1=$(echo "$channel_info" | jq -r '.node1_pub')
    node2=$(echo "$channel_info" | jq -r '.node2_pub')
    
    # Determine which node's fee to extract
    if [[ "$node" == "$node1" ]]; then
        fee_base_msat=$(echo "$channel_info" | jq -r '.node1_policy.fee_base_msat')
    elif [[ "$node" == "$node2" ]]; then
        fee_base_msat=$(echo "$channel_info" | jq -r '.node2_policy.fee_base_msat')
    else
        echo "Error: Node pubkey does not match either participant in the channel."
        exit 1
    fi
    
    # Print the base fee
    echo "$fee_base_msat"
    '''


# Use local node for testing script. Comment out for submission!  
lncli="lncli -n=signet --tlscertpath=$HOME/.lnd/tls.cert --macaroonpath=$HOME/.lnd/data/chain/bitcoin/signet/readonly.macaroon"

# Get channel info  
channel_info=$($lncli getchaninfo "$channel_id")

# Extract node1 and node2 pubkeys  
node1=$(echo "$channel_info" | jq -r '.node1_pub')  
node2=$(echo "$channel_info" | jq -r '.node2_pub')

# Determine which node's fee to extract  
if [[ "$node" == "$node1" ]]; then  
    fee_base_msat=$(echo "$channel_info" | jq -r '.node1_policy.fee_base_msat')  
elif [[ "$node" == "$node2" ]]; then  
    fee_base_msat=$(echo "$channel_info" | jq -r '.node2_policy.fee_base_msat')  
else  
    echo "Error: Node pubkey does not match either participant in the channel."  
    exit 1  
fi

# Print the base fee  
echo "$fee_base_msat"
