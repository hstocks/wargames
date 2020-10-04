This one took a while. The goal is straightforward - we must deploy a contract consisting of 10 bytes of bytecode or less, which will return 42 when called. This turned out to be easier said than done due to not knowing exactly how contracts were deployed and the calling conventions for functions in the EVM. The best resource I found for this was https://medium.com/@hayeah/diving-into-the-ethereum-vm-part-5-the-smart-contract-creation-process-cb7b6133b855.

After much trial and error (despite the simplicity) I arrived at the following assembly. The first section simply copies the contract code into memory and then returns, and the second section is the actual code which will be deployed on the blockchain which will return 42 when called.
```javascript
// Contract initialization
PUSH1 0x0a
PUSH1 0x0c
PUSH1 0x00
CODECOPY
PUSH1 0x0a
PUSH1 0x00
RETURN

// Main contract code
PUSH1 0x2a
PUSH1 0x00
MSTORE
PUSH1 0x20
PUSH1 0x00
RETURN
```

We can assemble the bytecode with an [EVM assembler](https://github.com/ITSecLabs/EVM-Assembler) I found on GitHub, which spits out our bytecode as a hex string:
```bash
$ ./easm asm.evm
easm v0.1
0x600a600c600039600a6000f3602a60005260206000f3
```

Now we can deploy our contract with the following Javascript:
```javascript
var abi = [];
var new_contract = web3.eth.contract(abi);
new_contract.new({data: "0x600a600c600039600a6000f3602a60005260206000f3", gas: 7000000, gasPrice: 50000000000, from: player}, ()=>{});
```

Then set the solver to our deployed contract:
```javascript
await contract.setSolver("0xFc8f794d0410B8A3a5B0AF30591c6E835CDcC43E")
```

Now we can submit the level and win.
