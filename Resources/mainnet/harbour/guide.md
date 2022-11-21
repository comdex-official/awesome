```
Harbor APP-ID: 2
```

### Asset whitelisting in the asset module

</l1> \* [Whitelisting Asset](/Resources/mainnet/base.md)

### Pair whitelisting in the asset module

</l1> \* [Pair Asset](/Resources/mainnet/base.md)

### Adding an Extended Pair

---

ExtendedPair as the name suggests is an extension of a pair to be used by a specific app for vault creation containing key params for each Collateral - Borrow asset pair.

- Creation of ExtendedPairVault is done through app governance(CosmWasm contracts)

Steps:

1. Formulate the Execute json

```
EXECUTE='{"propose":{"propose":
            {"title":"New proposal for add vault pair for CMDX-A - CMST",
             "description" :"This is a base execution proposal to add CMDX-A - CMST vault pair with given Vault properties",
             "msgs" : [{"msg_add_extended_pairs_vault":
                        {"app_id":2,
                        "pair_id":2,
                        "stability_fee":"0.045",
                        "closing_fee":"0.01",
                        "liquidation_penalty":"0.15",
                        "draw_down_fee":"0.01",
                        "is_vault_active":true,
                        "debt_ceiling":"100000000000000",
                        "debt_floor":"1000000",
                        "is_stable_mint_vault":true,
                        "min_cr":"1.7",
                        "pair_name":"CMDX-A",
                        "asset_out_oracle_price":false,
                        "asset_out_price":1000000,
                        "min_usd_value_left":10000000}}],
                        "app_id_param" :2
                       }}}'
```

2. Add the Proposal

```
comdex tx wasm execute comdex17p9rzwnnfxcjp32un9ug7yhhzgtkhvl9jfksztgw5uh69wac2pgs4jg6dx  "$EXECUTE" --amount 100000000uharbor --from walletName -–chain-id comdex-1 -–node https://comdex-rpc:443
```

3. Formulate the proposal Id to be executed.

```
EXECUTE='{"execute": {"proposal_id":proposal_ID}}'
```

4. Execute the Proposal after it Passes

```
comdex tx wasm execute comdex17p9rzwnnfxcjp32un9ug7yhhzgtkhvl9jfksztgw5uh69wac2pgs4jg6dx  "$EXECUTE" --from walletName -–chain-id comdex-1 -–node https://comdex-rpc:443
```

ExtendedPairVault:

- app_id: app_id designated for the extended_pair_vault. Can only be used by this app_id.
- pair_id:base pair_id
- stability_fee: interest charge on debt issued annually (accrued per block)
- closing_fee: fee charged on closing a vault
- liquidation_penalty: Penalty charged of debt if vault is liquidated due to bad collateral ratio
- draw_down_fee:fee charged by protocol of minting the borrowed token.
- Is_vault_active: flag check if vault operations are active for the extended_pair_vault
- debt_ceiling : maximum debt that can be borrowed under the extended_pair_vault.
- debt_floor: Minimum debt that must be borrowed in a vault.
- Is_stable_mint_vault: flag check if vault created under this extended_pair_vault acts as stable mint vault
- min_cr : minimum collateralization ratio to maintain in vault to avoid liquidation
- pair_name : a unique pair name for the extended_pair_vault
- asset_out_oracle_price : boolean flag denoting if asset_out price for the pair to be taken via oracle
- asset_out_price: asset out price if Asset_out_oracle_price is false
- min_usd_value_left: minimum asset value below that should not be in auction

### Activete External Rewards For Locker

---

```
comdex tx rewards activate-external-rewards-locker [appMappingID] [asset_id] [totalRewards] [durationDays] [minLockupTimeSeconds] --from walletName --chain-id comdex-1 --node https://comdex-rpc.one:443
```

- appMappingID : app_id 2 for Harbor.
- asset_id: asset id for which rewards to be given.
- totalRewards: Amount to be given as rewards.
- durationDays : days for which rewards will be active.
- minLockupTimeSeconds: Minimum lockup time required to be eligible for rewards.

### Activate External Rewards for Vault

---

```
comdex tx reward activate-external-rewards-vault [appMappingID] [extendedPairID] [totalRewards] [durationDays] [minLockupTimeSeconds] --from walletName --chain-id comdex-1 --node https://comdex-rpc.one:443
```

- appMappingID : app_id 2 for Harbor.
- extendedPairID: extendedPairID for which rewards to be given.
- totalRewards: Amount to be given as rewards.
- durationDays : days for which rewards will be active.
- minLockupTimeSeconds: Minimum lockup time required to be eligible for rewards.
