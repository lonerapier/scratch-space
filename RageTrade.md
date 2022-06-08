# Perps

## RageTrade

- ETH perps with 10x lev
- Omnichain liq. using LayerZero
- Yield generating 80-20 vaults

## Core

### Interfaces

- IClearingHouse
   - IClearingHouseCustomErrors
   - ICHEvents
   - ICHEnums
   - ICHSystemActions
   - ICHOwnerActions
   - ICHActions
   - ICHStructures

	```solidity
	struct Pool {
		IVToken vToken; // address of the vToken, poolId = vToken.truncate()
		IUniswapV3Pool vPool; // address of the UniswapV3Pool(token0=vToken, token1=vQuote, fee=500)
		IVPoolWrapper vPoolWrapper; // wrapper address
		PoolSettings settings; // pool settings, which can be updated by governance later
	}

	struct PoolSettings {
		uint16 initialMarginRatioBps; // margin ratio (1e4) considered for create/update position, removing margin or profit
		uint16 maintainanceMarginRatioBps; // margin ratio (1e4) considered for liquidations by keeper
		uint16 maxVirtualPriceDeviationRatioBps; // maximum deviation (1e4) from the current virtual price
		uint32 twapDuration; // twap duration (seconds) for oracle
		bool isAllowedForTrade; // whether the pool is allowed to be traded at the moment
		bool isCrossMargined; // whether cross margined is done for positions of this pool
		IOracle oracle; // spot price feed twap oracle for this pool
	}

	```

- ICHView
- IInsuranceFund
- IVPoolWrapper
- IVQuote
- IVToken

### Protocol

- **ClearingHouse**

Manages 

- **ClearingHoueStorage**

```solidity
Protocol.Info
mapping(uint256 ⇒ Account.Info)
insuranceFund
```

### Libraries

- **Protocol**

```solidity
struct Info {
	// poolId => PoolInfo
	mapping(uint32 => IClearingHouseStructures.Pool) pools;
	// collateralId => CollateralInfo
	mapping(uint32 => IClearingHouseStructures.Collateral) collaterals;
	// settlement token (default collateral)
	IERC20 settlementToken;
	// virtual quote token (sort of fake USDC), is always token1 in uniswap pools
	IVQuote vQuote;
	// accounting settings
	IClearingHouseStructures.LiquidationParams liquidationParams;
	uint256 minRequiredMargin;
	uint256 removeLimitOrderFee;
	uint256 minimumOrderNotional;
	// reserved for adding slots in future
	uint256[100] _emptySlots;
}
```

- **Account**

```
/// @notice account info for user
/// @param owner specifies the account owner
/// @param tokenPositions is set of all open token positions
/// @param collateralDeposits is set of all deposits
struct Info {
    uint96 id;
    address owner;
    VTokenPosition.Set tokenPositions;
    CollateralDeposit.Set collateralDeposits;
    uint256[100] _emptySlots; // reserved for adding variables when upgrading logic
}
```

## Vaults

