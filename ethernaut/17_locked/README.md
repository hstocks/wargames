This is a simple case of an uninitialised storage pointer. The `NameRecord` struct isn't initialised when it is created so it is assigned slot 0. The `unlocked` variable is also in slot 0, so all we have to do is specify a value in the `name` field which is truthy, which will overwrite `unlocked`.

More information about this kind of bug:
- https://blog.b9lab.com/storage-pointers-in-solidity-7dcfaa536089
- https://ethereum.stackexchange.com/questions/62384/bytes-variables-are-connected/62394#62394

```javascript
await contract.register("0xdeadbeefcafebabedeadc0deac1dc0dedeadbeefcafebabedeadc0deac1dc0de", player, {gas: 300000})
```
