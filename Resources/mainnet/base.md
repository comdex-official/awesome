### Whitelisting an Asset
____________________
An asset to be used in the chain has to be added via governance proposal.An asset is a common entity and can be used globally by any app .i.e the app can control the use of any in their protocol but cannot control/stop other apps to use the same.
```
comdex tx gov submit-proposal add-assets --add-assets-file assetsample.json --from walletName -–chain-id comdex-1 -–node https://comdex-rpc:443
```
assetsample.json file structure :
```
{
 "name" :"CMDX",
 "denom" :"ucmdx",
 "decimals" :"1000000",
 "is_on_chain" :"1",
 "asset_oracle_price" :"1",
 "is_cdp_mintable" :"0",
 "title" :"Add assets for applications to be deployed on comdex mainnet",
 "description" :"This proposal it to Whitelist CMDX"
 "deposit" :"3000000000ucmdx"
}
```
Name: Name of the Asset\
Denom: IBC denom\
Decimals: coin decimal of the Asset\
IsOnChain: for on chain (1) and off chain (0)\
AssetOraclePrice: If prices are required from oracle (1) or not(0)\
IsCdpMintable: If It is supposed to be minted through CDP (1) or Not(0)
### Whitelisting a Pair
____________________
A pair is an ordered combination of 2 assets where first asset is an asset accepted as deposit by protocol and second asset is the one minted by protocol for the user.
```
comdex tx gov submit-proposal add-pairs [asset-in] [asset-out] --from walletName -–chain-id comdex-1 -–node https://comdex-rpc:443
```
