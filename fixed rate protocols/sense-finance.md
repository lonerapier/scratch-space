# Sense Finance

## Sense space

Sense uses yieldspace invariant to create PT/target AMM pools built on balancer. YT can also be traded in the pool using *zap* transactions.

### Zap transactions

Yield token trading can occur using sense space.

![trade_yts](../assets/target_trading.png)

To trade target for YTs,

1. flash loan target from sense at 0% fees
2. Put target into sense and take PTs and YTs
3. swap PTs -> target
4. pay back flash loan
5. return YTs and extra target

Trade YTs for target

1. flash loan target from sense
2. swap target -> PTs
3. Put PTs and YTs in sense for target
4. pay back flash loan
5. return extra target

### LPs

Each series has its own space pool built, for which LPs can provide liquidity.

![LPs](../assets/space_lps.png)

To deposit into pools,

1. deposit target into sense for PTs and YTs
2. deposit target and PTs into space
3. LPs can choose to immediately sell YTs or keep it and trade it later
4. Return extra target, LP shares, and YTs

Removing the liquidity,

1. Get target and PTs back from space using LP shares
2. Either, swap PTs for target in sense after maturity or swap in space.

### LP Returns

Depends on three main criteria,

1. Fixed yield on PTs
2. yield on target i.e. yield bearing assets
3. space trading fees

## Use cases

- Fixed rate earners
- trading on future yield
- longing future yield
- lp returns
- series actors/managers
- fuse lenders/borrowers
- arbitrageurs

### Space pool LP returns


## Sense Implementation

Check out [Sense implementation file](./sense-finance-impl.md).


## Resources

- [Medium](https://medium.com/sensefinance)
- [Github](https://github.com/sense-finance)
- [Website](https://sense.finance)