# Socket Dot Tech

# [**Registry.sol**](https://etherscan.io/address/0xc30141B657f4216252dc59Af2e7CdB9D8792e1B0#code)

## Structs

   - RouteData

```other
address route;
bool isEnabled;
bool isMiddleware
```

   - MiddlewareRequest

```other
uint256 id;
uint256 optionalNativeAmount;
address inputToken;
bytes data;
```

   - BridgeRequest

```other
uint256 id;
uint256 optionalNativeAmount;
address inputToken;
bytes data;
```

   - UserRequest

```other
address receiverAddress
uint256 toChainId
uint256 amount
MiddlewareRequest middlewareRequest
BridgeRequest bridgeRequest
```

## State Variables

   - `RouteData[] routes`
   - `NATIVE_TOKEN_ADDRESS`

## Events

   - NewRouteAdded
   - RouteDisabled
   - RouteAdded
   - ExecutionCompleted

## Functions

   - addRoutes
   - rescueFunds
   - outboundTransferTo

```other
1. check for user amount != 0
2. bridge ID != 0
3. bridge input token != address(0)
4. load middleware info and validate
5. load bridge info and validate
6. emit ExecutionCompleted
7. if middlewareId is 0, then directly send to bridge
8. then perform action through middleware to perform swap on token and transfer on registry address on the basis of inputToken
9. grant approvals on basis of whether token == nativeToken
10. call bridge's outboundTransferTo
```

   - disableRoute

# ImplBase

## State Variable

- `address public registry;`
- `address public constant NATIVE_TOKEN_ADDRESS =
address(0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE);`

### Functions

- rescueFunds

```other
function rescueFunds(
        address token,
        address userAddress,
        uint256 amount
    ) external onlyOwner {
        IERC20(token).safeTransfer(userAddress, amount);
    }
```

- outboundTransferFrom

```other
function outboundTransferTo(
        uint256 _amount,
        address _from,
        address _receiverAddress,
        address _token,
        uint256 _toChainId,
        bytes memory _extraData
    ) external payable virtual;
```

   - if token == native token address, directly send to L2 else give approvals then send to L2

# MiddlewareImplBase

### State Variables

- `address public immutable registry;`
- `aggregator`

### Functions

- performAction

```other
function performAction(
        address from,
        address fromToken,
        uint256 amount,
        address receiverAddress,
        bytes memory data
    ) external payable virtual returns (uint256);
```

- rescueFunds

```other
function rescueFunds(
        address token,
        address userAddress,
        uint256 amount
    ) external onlyOwner {
        IERC20(token).safeTransfer(userAddress, amount);
    }
```

## Eth

- [Registry](https://etherscan.io/address/0xc30141B657f4216252dc59Af2e7CdB9D8792e1B0#code)
- [HopImpl](https://etherscan.io/address/0xd286595d2e3D879596FAB51f83A702D10a6db27b#code) → ImplBase
- [NativePolygonImpl](https://etherscan.io/address/0xa7649aa944b7dce781859c18913c2dc8a97f03e4#code) → ImplBase
- [NativeArbitrumImpl](https://etherscan.io/address/0x852C5DE08b9beB014caD171C16B12a8D7456ea3f#code) → ImplBase
- [AnySwapImplL1](https://etherscan.io/address/0x040993fbF458b95871Cd2D73Ee2E09F4AF6d56bB#code) → ImplBase
- [HyphenImplL1](https://etherscan.io/address/0xBE51D38547992293c89CC589105784ab60b004A9#code) → ImplBase
- [oneInchSwapImpl](https://etherscan.io/address/0x5800249621DA520aDFdCa16da20d8A5Fc0f814d8#code) → MiddlewareImplBase

## Arbitrum One

- Registry
- [HopImplL2](https://arbiscan.io/address/0x2b42AFFD4b7C14d9B7C2579229495c052672Ccd3#code) → ImplBase
- [AnyswapImplL2](https://arbiscan.io/address/0x8537307810fC40F4073A12a38554D4Ff78EfFf41#code) → ImplBase
- [OneInchImpl](https://arbiscan.io/address/0x565810cbfa3Cf1390963E5aFa2fB953795686339#code) → MiddlewareImplBase

## Polygon

- [Registry](https://polygonscan.com/address/0xc30141B657f4216252dc59Af2e7CdB9D8792e1B0#code)
- [HopImplL2](https://polygonscan.com/address/0x2b42AFFD4b7C14d9B7C2579229495c052672Ccd3#code) → ImplBase
- [HyphenImplL2](https://polygonscan.com/address/0x565810cbfa3Cf1390963E5aFa2fB953795686339#code) → ImplBase
- [AnyswapL2Impl](https://polygonscan.com/address/0x8537307810fC40F4073A12a38554D4Ff78EfFf41#code) → ImplBase
- [OneInchSwapImpl](https://polygonscan.com/address/0xc317144DE60E6bC9455363bB09852C00bd14CD61#code) → MiddlewareImplBase

## Optimism Mainnet

- [Registry](https://optimistic.etherscan.io/address/0xc30141B657f4216252dc59Af2e7CdB9D8792e1B0#code)
- [HopImplL2](https://optimistic.etherscan.io/address/0x8537307810fC40F4073A12a38554D4Ff78EfFf41#code) → ImplBase
- [OneInchSwapImpl](https://optimistic.etherscan.io/address/0x565810cbfa3Cf1390963E5aFa2fB953795686339#code) → MiddlewareImplBase

## Fantom

- [Registry](https://ftmscan.com/address/0xc30141B657f4216252dc59Af2e7CdB9D8792e1B0#code)
- [AnyswapL2](https://ftmscan.com/address/0x2b42AFFD4b7C14d9B7C2579229495c052672Ccd3#code) → ImplBase

## Binance Smart Chain

- [Registry](https://bscscan.com/address/0xc30141b657f4216252dc59af2e7cdb9d8792e1b0#code)
- [AnyswapImplL2](https://bscscan.com/address/0x2b42AFFD4b7C14d9B7C2579229495c052672Ccd3#code)
- [OneInchSwapImpl](https://bscscan.com/address/0x8537307810fC40F4073A12a38554D4Ff78EfFf41#code)

## Avalanche C-Chain

- [Registry](https://avascan.info/blockchain/c/address/0x2b42AFFD4b7C14d9B7C2579229495c052672Ccd3/contract)
- [AnySwapImplL2](https://avascan.info/blockchain/c/address/0x8537307810fC40F4073A12a38554D4Ff78EfFf41/contract)
- [HyphenImplL2](https://avascan.info/blockchain/c/address/0x565810cbfa3Cf1390963E5aFa2fB953795686339/contract)

