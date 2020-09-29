Using delegatecall to call another contract will call it with the existing environment, and allow the callee to access the caller's state. The original `msg.sender` is preserved which allows us to call `pwn` with our address as the `msg.sender`.

To call `pwn` we need to determine the hash of the function prototype. For this we can use the Remix IDE to compile the function and tell us the hash.

```javascript
await sendTransaction({from: player, to: instance, data: "0xdd365b8b"})
```
