# What is APP in the asset module?

```
cSwap APP-ID: 1
```

Consider the app as an isolated container for all or specific modules. 

In this case `app_id 1` is specific to cswap, and the liquidity module leverages its ability of it to isolate all the liquidity pairs and pools within this app only.

One can create a new app and have its very own DEX-like cSwap functioning independently and at the same time leveraging all the features provided by the liquidity module. 

eg. `pool 1` will be completely different on `app 1` compared to `app 2`
<br>

# How to create a liquidity pool?

<ol>
<li>

##  Add asset    
<ol>            
    <li>

### Verify if required assets are whitelisted. 

Verify if the assets/token used for pair & pool creation are already whitelisted in the asset module by the following command.

```sh
$ comdex q assetv1 assets
``` 
If the results from the above query contain the asset/token which is required you can skip the part below and hop on step 2 of this guide.
    </li>
    <li>

### Asset whitelisting in the asset module

(link for a guide to add asset in asset module)
    </l1>
</ol>
</li>

<li>

## Create liquidity pair
Anyone can create a pair of two coins, which is an ordered pair. The ordered coin pair is unique in the liquidity module, which means only a single pair of a given ordered coin pair is allowed. For example, the liquidity module can have only one pair for A-B coin pair consisting of Coin A and Coin B.

The coins in pairs are further identified as <b>Base Coin</b> and <b>Quote Coin</b>. 

On a trading exchange, assets are traded in pairs; in other words, one asset (Base asset) is sold against the other (Quote asset). The spot rate (spot price) reflects how much of the Quote asset is required to buy 1 unit of the Base asset.

For example, if our desired trading pair is CMDX/CMST, and the spot rate is 1 CMST, we will be trading CMDX, which is the Base asset, against CMST, which is the Quote asset. The spot rate of 1 CMST means that we have to sell 1 CMST to obtain 1 CMDX.

To check existing pairs use the following command -
```sh
$ comdex q liquidityV1 comdex query liquidityV1 pairs [app-id]
```

The Liquidity pair can be created in two different ways, 
<ol>
<li> 

### Permission Less
A certain amount of fee needs to be paid for creating pair. The fee paid is non-refundable. A pair can be launched instantly with this method. (Check liquidity params for fees charged)

```sh
$ comdex tx liquidityV1 create-pair [app-id] [base-coin-denom] [quote-coin-denom] -–from wallet –-chain-id comdex-1 -–node https://comdex-rpc:443
```

</li>
<li>

### Governance
A proposal needs to be raised for creating pairs via the governance module. In this method, no fee is charged for creating the pair but the community decides if the pair needs to add or not.

```sh
$ comdex tx gov submit-proposal create-liquidity-pair [app-id] [base-coin-denom] [quote-coin-denom] -–from wallet -–chain-id comdex-1 -–node https://comdex-rpc:443
```

</li>
</ol>

What next?

Once the liquidity pair is created it is ready to be used in multiple ways- 
<ol>
<li>
Asset/token can be traded (even without pool) via this pair and features like <b>Limit Order</b> and <b>Market Orders</b> can be used. 
</li>
<li>
This pair can be used to create liquidity pools. 
</li>
</ol>
</li>

<li>

## Create a liquidity pool

Pool(s) belong to a pair. Therefore, a pair must exist in order to create a pool
For pool creation, a certain amount of fees needs to be paid (check liquidity parameters for the same). Each pool is unique. At the initial creation of the pool, a fixed amount of `max(deposit_coins.Amount)` pool tokens is minted and sent to the creator of the pool’s account. The pool coin denom is in the form of `pool{app_id}-{pool_id}`.

eg.
`app 1` and `pool 2` will have pool denom as `pool1-2`.

The following command is used to create a liquidity pool -
```sh
$ comdex tx liquidityV1 create-pool [app-id] [pair-id] [deposit-coins] -–from wallet -–chain-id comdex-1 -–node https://comdex-rpc:443
```

Once done you can query for the pool using the following command
```sh
$ comdex query liquidityV1 pools [app-id]
```
The last object from the result of the above query will show your pool details along with `pool_id`. 

</li>
<li>

## That's it
Your pool is live on the chain and will be acting as AMM for completing all the eligible limit and market orders.

How to incentivize these pools is explained in the next section of this documentation.
</li>

</ol>


# How to incentivize liquidity pool?

The rewards module allows the community and third parties to add incentives to a pool that will be distributed to the farmer of that particular pool.
For example, in an ATOM-CMDX pool, incentivized by CMDX rewards, any individual or organization can provide additional incentives to this pool or create a new incentive for any other pool.

Apart from this, they can create an incentivization system in which the rewards are distributed over a defined period of time. For example, the XYZ foundation allocates 90,000 XYZ tokens to be distributed over three months; in this case, 1000 XYZ tokens will be distributed per day over the next 90 days

You can find the detailed explanation of the farming-rewards mechanism [here](https://docs.cswap.one/farming-rewards)

The following command can be used to create the incentive for the specific pool of the given app - 
```sh
$ comdex tx rewardsV1 create-gauge [gauge-type-id] [trigger-duration] [deposit-amount] [total-triggers] --app-id=[app-id] --pool-id=[pool-id] --start-time=[epoch-time] --from wallet --chain-id comdex-1 --node https://comdex-rpc.one:443
```

eg. 

```sh
comdex tx rewardsV1 create-gauge 1 24h 100000000000ucmdx 60 --app-id=1 --pool-id=5 --start-time=1668953133 --from cooluser --chain-id comdex-1 --node https://comdex-rpc.one:443
```
`cooluser` wants to incentivize the `pool 5` in `app 1`.<br>

The total incentives are `100000000000ucmdx` over `60 epochs` and each epoch is of `24h` i.e `1666666666ucmdx` will be distributed every `24h` for total `60` epochs.<br>

This gauge will stay inactive until the `start-time 1668953133` translates to (`Sunday, 20 November 2022 14:05:33`) is reached. Once the start time has passed this gauge becomes active and the distribution will begin at the upcoming epoch trigger. 


The parameters in the above example are explained below -
<ol>
<li>

`gauge-type-id` :

This defines if the rewards created are for pools in the liquidity module. This should be <b>always keep as 1</b>
</li>
<li>

`trigger-duration` :

At what interval reward should be distributed is defined by this parameter. The minimum interval is 12h and the default is 24h. 
</li>
<li>

`deposit-amount` :

The total amount that needs to be allocated as an incentivization.
</li>
<li>

`total-triggers` : 

This defines, the total how many parts your deposit amount should be split. 
The  (deposit-amount / total-triggers) is distributed each time at the interval of trigger-duration.
</li>
<li>

`--app-id` :

App ID in which our pool exists that we need to be incentivized
</li>
<li>

`--pool-id` :

Pool ID that needs to be incentivized

</li>
<li>

`--start-time`:

Epoch time, The time at which the incentive will be marked as active and will be ready for distribution. 
</li>

</ol>
<br>
<br>

Once the gauge is created it can be checked via the following command -
```sh
$ comdex q rewardsV1 gauges
``` 
After `start-time` has been passed it can also be checked using the following command, this won't show the created gauge until the star-time has been passed - 
```sh
$ comdex query liquidityV1 pool-incentives [app-id]
```