For this level we need to call `unlock` and pass a 16 byte key matching the stored data. This is easy because all of the state variables in a contract are publicly viewable on the blockchain, whether they're marked private or not.

Looking at the [transaction which created the contract](https://ropsten.etherscan.io/tx/0xf657a9ebb924dcfdcbd6ee7ff34ee76f0a447699e44bb874f5411f9d3756cfa3#statechange) in Etherscan we can see the relevant state changes and find the 32 byte string we care about - `0x952c7816ca3343fea396ce79734ff03974e8fa4ed63cc5da433428961164a4ed`.

Importantly the `unlock` function downcasts the stored data from `bytes32` to `bytes16`, which means the low 16 bytes of the stored data are taken and compared against our input. This means that we can use `0x952c7816ca3343fea396ce79734ff039` as our key.

```javascript
await contract.unlock("0x952c7816ca3343fea396ce79734ff039", {gas: 300000})
```
