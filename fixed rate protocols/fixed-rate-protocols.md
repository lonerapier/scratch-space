# Fixed Income Protocols

DeFi's next wave of protocols has come through fixed income protocols. I will go deep into yield curves based protocols i.e. [Yield](https://yield.is), [Notional](https://notional.finance), [Uma]().
## Yield Protocol

Yield introduced *yTokens* i.e. `Yield Tokens` similar to compound's *cTokens* which essentially behaves as zero-coupon bonds expiring at a future date and can be redeemed 1-on-1 for the underlying asset.

*yTokens* can be building blocks that can be used to make many other interesting products. Market price of *yTokens* can be used as interest rate oracle. Each yToken has its own interest rate over the period to expiration date which can be used by many other protocols to settle on-chain interest rate derivatives.

*yTokens* differ from each other in four aspects:

1. Underlying Asset
2. Collateral Asset
3. Collateralization requirement
4. Expiration Time
## Actors
### Borrowers

Borrowers is when actor opens a vault, takes out *yToken* and sells it. It is essentially shorting the underlying asset or longing the collateral asset.
### Lenders

Buying yTokens are similar to lending the underlying asset in which the holder of yToken is earning an interest on the underlying asset in the form of the discount which it gets when buying the yTokens.
## Settlement

yTokens can be construcuted using 3 main principals.
### Cash settlement

Cash Settlement is paid in the collateral asset, which implies that it depends on a dependent price oracle that determines the price of underlying asset in terms of collateral asset.

At the moment of maturity, anyone can call the contract to trigger *settlement*, that redeems the *yTokens* for its equivalent value in collateral asset. After the moment of settlement, *yTokens* begin to track the price of collateral asset rather than the price of underlying asset but can only be redeemed at the price of settlement.

This mechanism has an advantage that it can support any asset and not just ERC20 assets as it just needs a price oracle to compare the price of yTokens with the collateral asset.
### Physical settlement through auctions

At the time of minting a yToken, it is backed using the collateral asset. Minting the tokens adds to the vault's owner debt which shouldn't be less than the value of the collateral asset plus some required margin.

When a target asset is also an ERC20 token, its settlement can be triggered physically i.e. holders get paid in underlying asset rather than collateral assets through auctions.

Gradual dutch auctions are held to sell the collateral for the underlying asset. Remaining collateral is returned back to the vault owner and underlying tokens earned during the auction is ditributed among *yToken* holders.
If auction is not completed successfully, collateral asset is distributed amont the holders along with physical assets.

Advantage over cash settlement is that after the auction is successfully done, each *yToken* is backed equally with the underlying asset rather than some collateral asset. Holders can redeem the underlying asset (but doesn't earn yield on it).
### Synthetics Settlement

When the target asset itself is a collateralized synthetic asset like DAI, *yToken* uses token's own issuance mechanism as settlement.

In case of DAI, if yDAI backed with ETH as collateral matures, the protocol creates a vault in MakerDAO with ETH as collateral. When yDAI holders come to redeem, it borrows DAI from Maker and pay to the yDAI holders. Essentialy fixed rate position in yTokens turns into variable rate debt position at the time of maturity for these synthetic assets. yDAI holders need to pay the interest for their debt position and can earn DAI savings rate as well.

Advantage is that borrowers' and lenders' position is not settled and have the option to keep it open with the synthetic token's own mechanisms.
## Interest Rate oracle

*yTokens's* price in itself throughout the period until maturity can be treated as an interest rate oracle as the *yTokens* price floats freely depending on the supply and demand.

$$Y = (\frac{F}{P})^{\frac{1}{T}} - 1$$
$$$$

---
## YieldSpace AMM

A new invariant based AMM introduced to trade *fyTokens* introduced in yield paper which incorporates time into the AMM equation.

$$x^{1-t}+y^{1-t} = k$$

$y$ = reserves of *fyToken*,

$x$ = reserves of underlying token.

$t$ = time to maturity

---

![yieldspace curve](../assets/yieldspace_curve.png)

This formula works as constant sum protocol when $t->0$, and constant product formula when $t->1$.

This formula is defined in the yield space rather than the price space as designed in previous AMM formulas such that marginal interest rate of fyTokens at any time is equal to ratio of fyToken reserve to underlying token reserve minus 1.

$$r = \frac{y}{x} - 1$$

This formula does not have any time component, thus ensures that marginal interest rate remains proportional to fyToken and underlying token reserves at any point in time. This implies that as the allocation of fyToken in the pool increases or underlying token decreases so does the interest rate and buying pressure arises, and vice versa.
## Why not other invariants

1. Constant sum invariant only works for assets of similar value, and fyToken generally is priced at a discount until maturity date.
2. Constant product formula includes liquidity at whole price spectrum but when the fyToken approaches maturity, its price tend to be similar to underlying token and thus the liquidity at other price points are wasted and larger trades have significant impact on interest rates.
3. Curve's stableswap equation doesn't let it modify $\chi$ to account for variation in interest rates due to time to maturity.
## Properties

$$x^{1-t}+y^{1-t} = x_{start}^{1-t} + y_{start}^{1-t}$$

Marginal price for a given $x_{start}$, $y_{start}$, and $t$ is given by the formula:

$$(\frac{y}{x})^t = (\frac{(x_{start}^{1-t} + y_{start}^{1-t} - x^{1-t})^\frac{1}{1-t}}{x})^t$$

![token price vs reserves](../assets/dai_price_vs_dai_reserves.png)

Looking at interest rates,

![interest rate vs dai reserves](../assets/interest_rate_vs_dai_reserves.png)
$$\frac{y}{x} - 1 = \frac{(x_{start}^{1-t} + y_{start}^{1-t} - x^{1-t})^\frac{1}{1-t}}{x} - 1$$
## Fees

LP are incentivised to provide liquidity using the fees that they earn. Since, constant sum power formula is defined in yield space and not price space, it's not meaningful to impose fees on price and rather on interest rates i.e. any buyer of fyToken should get lesser interest rates or higher buy price.

Thus, the fee formula modifies the interest rate by adding a variable $g < 1$ to change interest rates.

$$r = (\frac{y}{x})^g - 1$$

> Note that this formula is used for buying fyTokens, $\frac{1}{g}$ is used when selling fyTokens.

Thus, the new AMM formula becomes

$$x^{1-gt}+y^{1-gt} = k$$
## Capital Efficiency

Original protocol allows user to mint 1 fyToken in exchange of 1 underlying token and there is no real incentive to buy a fyToken above the underlying token price. Thus, the pool always checks at the end of every trade that price of 1 fyToken is not greater than 1 underlying token or reserves of fyToken is greater underlying. Thus, Some portion of fyToken reserves in the pool is always inaccessible. Example can be when pool is first initialized, the equal fyToken in the pool is never utilised as the remaining fyToken can't be sold.

So, the capital efficieny of the pool is improved by making the excess fyToken's reserves `virtual`. LPs don't need to contribute these access reserves. Pool uses liquidity tokens `s` as the virtual fyTokens reserves. Whenever a trade occurs, `virtual` tokens are added to actual reserves to calculate the appropriate amount but whenver liquidity is added, only the real reserves are used to calculate fyTokens in proportion to the actual fyToken in pool.
## Resources
- [Yield Paper](https://research.paradigm.xyz/Yield.pdf)
- [Yieldspace paper](https://yield.is/YieldSpace.pdf)
- [Element finance paper](https://paper.element.fi//)
- [Sense](sense-finance.md)
- [Messari's Fixed Income Protocol](https://messari.io/article/fixed-income-protocols-the-next-wave-of-defi-innovation)
- [Designing Yield Tokens](https://medium.com/sensefinance/designing-yield-tokens-d20c34d96f56)
- [Swivel's cash flow instruments Pt.1  ](https://swivel.substack.com/p/cash-flow-instruments-pt-1-history?s=r)
- [Defization of fixed income products](https://medium.com/coinmonks/the-defization-of-fixed-income-products-7e72ed4f57b1)
- [Defixed income](https://medium.com/@exactly_finance/defixed-income-101-948976c0e2c6)
- [Notional](https://medium.com/coinmonks/notional-the-alpha-of-fixed-income-defi-products-a5637d2092b5)
- [](https://medium.com/finoa-banking/turning-proof-of-stake-yield-into-fixed-income-products-7de8a73097ac)
- [Fixed Income Protocols](https://medium.com/gamma-point-capital/fixed-income-protocols-the-next-wave-of-defi-innovation-69215be82b4e)