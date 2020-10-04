
This was a fun one and requires multiple steps to complete. Firstly we need to "make contact" with the contract. We can do this by calling `make_contact`, however we must pass the assertion `assert(_firstContactMessage.length > 2**200);`. It's impossible to specify an array with 2**200 items so we'll have to fake this. One of the fields in the ABI when dealing with variable length arrays is reserved for the size. Therefore if the contract doesn't actually verify whether the length matches the number of items we have provided then we can set this length to an arbitrarily large value.

We can trigger this like so:
```javascript
await sendTransaction({from: player, to: instance, data: "0x1d3d4c0b" + "0000000000000000000000000000000000000000000000000000000000000020" + "f000000000000000000000000000000000000000000000000000000000000003", gas: 3000000})
```

The second bytes32 is the offset of the start of the array in the call data (minus 4 for the function hash). The value starting `f000...` is the length we're specifying. Initially I was getting Out of Gas errors here because of a `calldatacopy` with the very large size we specified, which became apparent when looking in the Remix debugger. However we can use another integer overflow to bypass this, by making the size large enough such that when the contract calculates the size argument for `calldatacopy` the size overflows to some smaller value which doesn't trigger the gas errors.

Now we have `contact` set to `true` we're able to call any of the other functions in the contract. Interestingly we can call `retract` which does `codex.length--`. If the array length is 0 then this will cause the length to overflow to the maximum possible integer, `0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`. 

We can do this like so:
```javascript
await contract.retract()
```

With `codex` having a very large size it is possible for us to index into it at any index, because any index (aside from the maximum integer) we choose is always going to be valid. Therefore we can use this to write elsewhere in the contract's storage and overwrite the `owner` field which is stored in slot 0.

Variable-length arrays are stored at storage address calculated by `sha3(slot_index_32_bytes)`. In our case the slot index is `1`, therefore the array is stored at `0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6`. If we want to access the Nth element in the array then the calculation is `0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6 + N`. Therefore we can calculate the index needed to wrap this calculation round to 0 and access the value at the 0th slot.

This value is `2**256 - 0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6 = 0x4ef1d2ad89edf8c4d91132028e8195cdf30bb4b5053d4f8cd260341d4805f30a`.

Now we know the index we need to specify to write into the `owner` slot, we can simply pass our own address (importantly this must be padded to 32-byte width) as the value to write, which will overwrite the existing owner with us.

```javascript
await contract.revise("0x4ef1d2ad89edf8c4d91132028e8195cdf30bb4b5053d4f8cd260341d4805f30a", "0x0000000000000000000000008e0ff062059376013fa56470805bec01b5ad06cc", {gas: 300000})
```
