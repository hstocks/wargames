Private variables in a contract only disallow other contracts from accessing them, but they are still publicly viewable on the blockchain. 

We can use the etherscan browser to find the transaction which created the contract and then observe the storage state after the creation, which contains the password.

https://ropsten.etherscan.io/tx/0xc8c03670fe54ef8e1b5ba54a0f2179c6d02db4351b70f2cd4cdf595cbbc28da7#statechange

This gives us `0x412076657279207374726f6e67207365637265742070617373776f7264203a29`, and decoding to ASCII we get `A very strong secret password :)`.
