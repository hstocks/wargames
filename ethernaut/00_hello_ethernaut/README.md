First we must get the password:
```javascript
await contract.password()
```
This gives us `ethernaut0`.

Now we pass this to the `authenticate` method:
```javascript
await contract.authenticate("ethernaut0")
```
