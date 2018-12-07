<!-- TITLE: Blockstream Liquid Sidechain -->
<!-- SUBTITLE: A quick summary of Liquid -->


- https://blockstream.com/assets/downloads/strong-federations.pdf
- https://elementsproject.org/elements-code-tutorial/overview
- https://blockstream.com/elements/


## News
https://blockstream.com/2018/11/06/liquid-full-node-and-wallet-release/
https://blockstream.com/2018/08/02/accelerating-liquid-adoption-liquid-block-explorer.html
Liquid asset issuance: https://twitter.com/Blockstream/status/1058449958892920832

https://www.coindesk.com/liquid-goes-live-blockstreams-first-bitcoin-sidechain-has-finally-arrived/
Liquid is not exactly a "real" sidechain, or at least not the type Blockstream was founded to research and build. Instead of building a sidechain that allows users to swap bitcoins for sidechain coins with a trusted third party, Blockstream built what's called a "federated sidechain."  Federated sidechains rely on a group of companies – in this case, Blockstream's launch partners – to collectively manage the sidechain.

## Overview
https://blockstream.com/liquid/
Liquid is an inter-exchange settlement network linking together cryptocurrency exchanges and institutions around the world, enabling faster Bitcoin transactions and the issuance of digital assets.

Liquid is just a federated sidechain for moving Bitcoins around more quickly
a semi-permissioned mostly-trustless high-speed rail for bitcoins
and now Blockstream is marketing this "Liquid" BTC sidechain to financial institutions and centralized exchanges

“Liquid is a permissioned federated sidechain with a two-way peg to the Bitcoin blockchain.
Notable because it’s built by Blockstream, who employs most of the Bitcoin Core development team.

Liquid is a high-speed rail to move bitcoins between exchanges and other Liquid-integrated services, without paying high fees or cluttering the Bitcoin blockchain.  I am one of maybe a half-dozen people not employed by Blockstream who still think sidechains are fascinating and worthy of more research.  In the next 18 months you _will_ see interesting developments in both federated pegged sidechains and purpose-built merge-mined chains.”


## User Experience

The gist from a user perspective: do a BTC transaction to "lock up" BTC and it is reissued on Liquid.  Liquid is proof of authority, negligible fees and higher-speed blocks.

once you're on Liquid, you could slosh your BTC between participating exchanges quickly

Liquid has lots more features planned, including token issuance and confidential transactions

Trust model: you need to request the federation's approval, essentially, to unlock your Liquid BTC on the Bitcoin chain

## Getting Started on Liquid
Blockstream has created a block explorer for Liquid: https://blockstream.info/liquid/
Blocks are created every minute.

You can see that confidential transactions are live:
https://blockstream.info/liquid/address/QBDfwjQ2JzFwABdijUDRHA2NP51eFTwMyH

Today you can set up a node: https://blockstream.com/2018/11/06/liquid-full-node-and-wallet-release/
https://github.com/Blockstream/liquid/blob/liquid.3.14.1/doc/README.md

But how do you get L-BTC?  Who is allowed to “peg in”?

Give it a go by signing up to an exchange that offers Liquid service, and making an L-BTC withdrawal (only The Rock Trading is live at the time of this blog post, but more exchanges are coming soon). Send L-BTC to friends that set up their own Liquid wallet, and use L-BTC to create and transact Issued Assets. For advanced users, if you use the peg-in RPC API, note that for security reasons peg-outs can only be made to Liquid exchange cold wallets, so you need to peg-out from your exchange account. Please be aware before you peg-in.

> if you use the peg-in RPC API

This suggests that there is a peg-in API, and anyone can convert BTC to L-BTC.

> for security reasons peg-outs can only be made to Liquid exchange cold wallets

Okay, so you can only convert BTC into L-BTC if the BTC is deposited into a Liquid-participating exchange.

Check out what’s possible with the liquid node software:

$ liquid-cli help |less



$ liquid-cli sendtomainchain
sendtomainchain amount ( subtractfeefromamount ) 

Sends Liquid funds to the Bitcoin mainchain, through the federated withdraw mechanism. The wallet internally generates the returned `bitcoin_address` via `bitcoin_xpub` and `bip32_counter` previously set in `initpegoutwallet`. The counter will be incremented upon successful send, avoiding address re-use.

Arguments:
1. "amount"   (numeric, required) The amount being sent to `bitcoin_address`.
2. "subtractfeefromamount"  (boolean, optional, default=false) The fee will be deducted from the amount being pegged-out.

Result:
{
"bitcoin_address"   (string) The destination address on Bitcoin mainchain.
"txid"              (string) Transaction ID of the resulting Liquid transaction
"bitcoin_xpub"      (string) The xpubkey of the child destination address.
"derivation_path"   (string) The derivation path in text that leads to `bitcoin_address` from the `bitcoin_xpub`.
}

Examples:
> liquid-cli sendtomainchain 0.1
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "sendtomainchain", "params": [0.1] }' -H 'content-type: text/plain;' http://127.0.0.1:9041/

$ liquid-cli initpegoutwallet bitcoin_xpub ( bip32_counter_counter liquid_pak )
initpegoutwallet bitcoin_xpub ( bip32_counter_counter liquid_pak )

This call is for Liquid network initialization on the Liquid wallet. The wallet generates a new Liquid pegout authorization key (PAK) and stores it in the Liquid wallet. It then combines this with the `bitcoin_xpub` to finally create a PAK entry for the network. This allows the user to send Liquid coins directly to a secure offline Bitcoin wallet at the `/0/k` non-hardened derivation path from the bitcoin_xpub using the `sendtomainchain` command. Losing the Liquid PAK or offline Bitcoin root key will result in the inability to pegout funds, so immediate backup upon initialization is required.

Arguments:
1. "bitcoin_xpub"        (string, required) The Bitcoin extended pubkey to be used as the root for the Bitcoin destination wallet. The derivation path from this key will be `0/k`.
2. "bip32_counter"       (numeric, default=0) The `k` in `0/k` to be set as the next address to derive from the `bitcoin_xpub`. This will be stored in the wallet and incremented on each successful `sendtomainchain` invocation.
3. "liquid_pak"          (string, optional) The Liquid wallet pubkey in hex to be used as the Liquid PAK for pegout authorization. The private key must be in the wallet if argument is given. If this argument is not provided one will be generated and stored in the wallet automatically and returned.

Result:
{
"pakentry"       (string) The resulting PAK entry to be used at network initialization time in the form of: `pak=<bitcoin_pak>:<liquid_pak>`.
"liquid_pak"     (string) The Liquid PAK pubkey in hex, which is stored in the local Liquid wallet. This can be used in subsequent calls to `initpegoutwallet` to avoid generating a new `liquid_pak`.
"liquid_pak_address" (string) The corresponding address for `liquid_pak`. Useful for `dumpprivkey` for wallet backup or transfer.
"address_lookahead"(array)  The three next Bitcoin addresses the wallet will use for `sendtomainchain` based on `bip32_counter`.
}
> liquid-cli initpegoutwallet tpubDAY5hwtonH4NE8zY46ZMFf6B6F3fqMis7cwfNihXXpAg6XzBZNoHAdAzAZx2peoU8nTWFqvUncXwJ9qgE5VxcnUKxdut8F6mptVmKjfiwDQ
> curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "initpegoutwallet", "params": [tpubDAY5hwtonH4NE8zY46ZMFf6B6F3fqMis7cwfNihXXpAg6XzBZNoHAdAzAZx2peoU8nTWFqvUncXwJ9qgE5VxcnUKxdut8F6mptVmKjfiwDQ] }' -H 'content-type: text/plain;' http://127.0.0.1:9041/



$ liquid-cli getpeginaddress
$ liquid-cli getpeginaddress
{
  "mainchain_address": "32jrrPoFeyxooRhZN1rTuRa8ZTX3CN1Tv3",
  "claim_script": "001403af84376a9e991ac06ba76106471d40e1aa45d6"
}
$ liquid-cli claimpegin bitcoinTx txoutproof ( claim_script )
claimpegin bitcoinTx txoutproof ( claim_script )

Claim coins from the main chain by creating a withdraw transaction with the necessary metadata after the corresponding Bitcoin transaction.
Note that the transaction will not be relayed unless it is buried at least 102 blocks deep.
If a transaction is not relayed it may require manual addition to a functionary mempool in order for it to be mined.

Arguments:
1. "bitcoinTx"         (string, required) The raw bitcoin transaction (in hex) depositing bitcoin to the mainchain_address generated by getpeginaddress
2. "txoutproof"        (string, required) A rawtxoutproof (in hex) generated by bitcoind's `gettxoutproof` containing a proof of only bitcoinTx
3. "claim_script"   (string, optional) The witness program generated by getpeginaddress. Only needed if not in wallet.

Result:
"txid"                 (string) Txid of the resulting sidechain transaction
----
bitcoin-cli gettxoutproof ["txid",...] ( blockhash )

Returns a hex-encoded proof that "txid" was included in a block.

NOTE: By default this function only works sometimes. This is when there is an
unspent output in the utxo for this transaction. To make it always work,
you need to maintain a transaction index, using the -txindex command line option or
specify the block in which the transaction is included manually (by blockhash).

Arguments:
1. "txids"       (string) A json array of txids to filter
    [
      "txid"     (string) A transaction hash
      ,...
    ]
2. "blockhash"   (string, optional) If specified, looks for txid in the block with this hash

Result:
"data"           (string) A string that is a serialized, hex-encoded data for the proof.





$ liquid-cli tweakfedpegscript "claim_script"


$ liquid-cli createrawpegin bitcoinTx txoutproof ( claim_script )



Asset Issuance
Check out what’s possible with the liquid node software:

$ liquid-cli help |less

destroyamount asset amount ( "comment" )
listissuances ( asset ) 
issueasset assetamount tokenamount ( blind )
reissueasset asset assetamount










