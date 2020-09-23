The "constructor" is sneakily named `Fal1out` (note the 1, instead of l), which is different to the contract name. This means that it is just a normal function that we can call which happens to grant the sender ownership.

```
await contract.Fal1out()
```

Note that this syntax of using the same name for the constructor is deprecated, presumably for reasons like this, instead it is recommended to use the `constructor` keyword. 
