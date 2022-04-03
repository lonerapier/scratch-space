# EVM

## Links

[Deep Analysis of VM: What virtual machines are used by Ethereum and EOS?](https://hashgard.medium.com/deep-analysis-of-vm-what-virtual-machines-are-used-by-ethereum-and-eos-af925b9408a3)

- [https://yoichihirai.com/malta-paper.pdf](https://yoichihirai.com/malta-paper.pdf)
[EVM: From Solidity to byte code, memory and storage](https://www.youtube.com/watch?v=RxL_1AfV7N4&t=10s)

- evm.codes
- [https://cse.iitk.ac.in/users/dwivedi/Blockchain/bytecode.pdf](https://cse.iitk.ac.in/users/dwivedi/Blockchain/bytecode.pdf)
[EVM bytecode programming - HackMD](https://hackmd.io/@e18r/r1yM3rCCd)

[Ethereum Virtual Machine (EVM) | ethereum.org](https://ethereum.org/en/developers/docs/evm/)

- [https://arxiv.org/pdf/2009.02663.pdf](https://arxiv.org/pdf/2009.02663.pdf)
- [https://personal.utdallas.edu/~kxh060100/ayoade19blockchain.pdf](https://personal.utdallas.edu/~kxh060100/ayoade19blockchain.pdf)
- [https://www.usenix.org/system/files/sec21_slides_rodler.pdf](https://www.usenix.org/system/files/sec21_slides_rodler.pdf)
- [file:///Users/sambhavdusad/Downloads/Thesis.pdf](file:///Users/sambhavdusad/Downloads/Thesis.pdf)
[Diving into Ethereum's Virtual Machine(EVM): the future of Ewasm - Second State News and Articles](https://blog.secondstate.io/post/20191029-ewasm/)

[Using the Compiler — Solidity 0.8.11 documentation](https://docs.soliditylang.org/en/v0.8.11/using-the-compiler.html)

[Reversing EVM bytecode with radare2](https://blog.positive.com/reversing-evm-bytecode-with-radare2-ab77247e5e53)

[Solidity tips and tricks to save gas and reduce bytecode size](https://blog.polymath.network/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size-c44580b218e6)

- [https://yinqian.org/papers/sec20.pdf](https://yinqian.org/papers/sec20.pdf)
- [file:///Users/sambhavdusad/Downloads/publi-5936.pdf](file:///Users/sambhavdusad/Downloads/publi-5936.pdf)
[Deconstructing a Solidity Contract — Part II: Creation vs. Runtime - OpenZeppelin blog](http://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-ii-creation-vs-runtime-6b9d60ecb44c/)

## Opcodes

32 bytes = 0x00..(60)..00

| tx                | 21000                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------- |
| creating contract | 32000                                                                                       |
| REVERT            | 0                                                                                           |
| PUSH1             |                                                                                             |
| DUP1              |                                                                                             |
| MSTORE            | stack[0] = stack[1]                                                                         |
| CALLVALUE         |                                                                                             |
| JUMPI             | jump to stack[0] if stack[1] non-zero                                                       |
| CODECOPY          | stack[0]: offset to copy to<br>stack[1]: offset to copy from<br>stack[2]: byte size to copy |

## Steps for constructor call:

1. Set up free-memory pointer

```other
PUSH1 0x80
PUSH1 0x40
MSTORE
```

2. REVERT if `wei` is sent to non-payable constructor

```other
CALLVALUE
DUP1
ISZERO
PUSH2 0x0010
JUMPI
PUSH1 0x00
DUP1
REVERT
JUMPDEST
```

3. store storage variables

```other
POP
PUSH1 0x03
PUSH1 0x00
SSTORE (store storage variables)
```

4. store storage variables and copy contract code to memory and set byte offset in memory

```code
PUSH1 0xc1
DUP1
PUSH2 0x0024
PUSH1 0x00
CODECOPY
PUSH1 0x00
RETURN
INVALID
```

## Function Calls:

1. public variables have getter functions setup by the compiler itself.
2. set free memory pointer
3. revert if `wei` sent to non-payable function
4. pop value
5. check CALLDATASIZE < 4 bytes

```other
PUSH1 0x04
CALLDATASIZE
LT
PUSH1 0x3c
JUMPI
```

6. Jump to function execution

## Function jump tables

1. Load calldata and shift to last 32 bits

```other
PUSH1 0x00
CALLDATALOAD
PUSH1 0xe0
SHR
```

2. check function selector equal to calldata and jump to offset

```other
DUP1
PUSH4 <function-selector>
EQ
PUSH1 0x<function-offset>
JUMPI
```

3. revert if no fn found in table
4. evm orders function based by its selector value, can save gas for frequently calling fns if < 3, else evm binary searches fn-selector

## functions

A.

```other
function set(uint256 _param) external { 
	val1 = _param;
}
```

1. return address is pushed

```other
PUSH1 0x5b
```

2. check if (calldatasize-4) < the size of uint256 0x20 (32 bytes)

```other
PUSH1 0x04
DUP1
CALLDATASIZE
SUB
PUSH 0x20
DUP2
LT
ISZERO
```

3. REVERT if its less or jump

```other
PUSH1 0x55
JUMPI
PUSH 0x00
DUP1
REVERT
```

4. pop the front i.e. (CALLDATASIZE-4)

```other
POP
```

5. now, set val1, which is in storage slot 1

```other
CALLDATALOAD
PUSH1 0x7b (dest)
JUMP
(return address) JUMPDEST
STOP


0x7b JUMPDEST
PUSH1 0x00
SSTORE
JUMP (stack now contains return address)
```

B.

```other
uint256 public val2; (getter)
val2()
```

1. set up return address

```other
PUSH1 0x63				|0x63|
```

2. Jump to code location

```other
PUSH1 0x80				|0x80|0x63|
JUMP
```

3. load variable from storage slot 1

```other
PUSH1 0x01			|0x01|0x63|
SLOAD				|val2|0x63|
DUP2 				|0x63|val2|0x63|
JUMP
```

4. store the variable in memory and return memory location and size

```other
JUMPDEST
PUSH1 0x40 		|0x40|val2|0x63|
DUP1				|0x40|0x40|val2|0x63|
MLOAD				|0x80|0x40|val2|0x63|
SWAP2 				|val2|0x40|0x80|0x63|
DUP3				|0x80|val2|0x40|0x80|0x63|
MSTORE				|0x40|0x80|0x63|
MLOAD				|0x80|0x80|0x63|
SWAP1				|0x80|0x80|0x63|
DUP2				|0x80|0x80|0x80|0x63|
SWAP1				|0x80|0x80|0x80|0x63|
SUB					|0x00|0x80|0x63|
PUSH1 0x20			|0x20|0x00|0x80|0x63|
ADD					|0x20|0x80|0x63|
SWAP1				|0x80|0x20|0x63|
RETURN
```

## Payable fallback

```other
function () external payable { }
```

1. check calldatasize < 4 bytes
2. Check function selector and jump
3. revert if callvalue non-zero

## Storage Layout

```other
contract StorageLayout { 
	byte private valByte; 				slot 0
	uint256 private valUint256a; 		slot 1
	uint32 private valUint32; 			slot 2
	uint64 private valUint64; 			slot 2
	address private valAddress; 		slot 2
	uint256 private valUint256b;		slot 3

	function set() external { 
		valByte = 0x10;
		valUint256a = 0x11; 
		valUint32 = 0x12;
		valUint64 = 0x13;
		valAddress = address(0x14); 
		valUint256b = 0x15;
	}
}
```

1. storing `valByte`

```other
PUSH1 0x00			|0x00|
DUP1				|0x00|0x00|
SLOAD				|0x00|0x00|
PUSH1 0x10			|0x10|0x00|0x00|
PUSH1 0xff			|0xff|0x10|0x00|0x00|
NOT					|0xff...ff00|0x10|0x00|0x00|
SWAP1				|0x10|0xff..ff00|0x00|0x00|
SWAP2				|0x00|0xff..ff00|0x10|0x00|
AND
OR					|0x10|0x00|
SWAP1				|0x00|0x10|
SSTORE
```

2. storing `valUint256a`

```other
PUSH1 0x11
PUSH1 0x01
SSTORE
```

3. storing `valUint32` `valUint64` `valAddress`

```other
PUSH1 0x02
DUP1
SLOAD
PUSH1 0x12
PUSH4 0xffffffff
NOT
SWAP1
SWAP2
AND
OR

PUSH12 0xffffffffffffffff00000000
NOT
AND
PUSH5 0x1300000000
OR

PUSH12 0xffffffffffffffffffffffff
AND
PUSH1 0x05
PUSH1 0x62
SHL
OR

SWAP1
SSTORE
```

4. store `valUint256b`

```other
PUSH1 0x15
PUSH 0x03
SSTORE
JUMP
```

## Storage arrays

```other
uint256[] private arrayUint256;
byte[] private arrayByte;
```

stored at `storage[keccak256(storage slot)+key] = value`

`storage[keccack256(storage slot)] = array length`

## Mappings

`mapping(uint256=>uint256) private map`

`storage[keccak256(key.storage slot number)] = val`

