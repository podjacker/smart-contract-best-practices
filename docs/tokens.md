
# Token Implementation Best Practice
Although, implementing Tokens should comply with other best practices, Tokens has there unique details.

## Comply with the latest standard
Generally speaking, Token's smart contract has to follow the latest standard. The current widely adopted is the ERC20. You can find its original discussion on GitHub: https://github.com/ethereum/EIPs/issues/20. 

An example of a standard ERC20 token is [OmiseGo](https://etherscan.io/address/0xd26114cd6EE289AccF82350c8d8487fedB8A0C07#code). On the other hand, the  [DOT Allocation Indicator](https://etherscan.io/address/0xb59f67a8bff5d8cd03f6ac17265c550ed8f33907#code) is an example of a token that is not ERC20 because, for some purpose, it implements some but not all of ERC20 functions.
## Prevent transfer to the 0x0 address
If you allow human mistakes, it will happen. By the time of writing this document, the address [0x0000000000000000000000000000000000000000](https://etherscan.io/address/0x0000000000000000000000000000000000000000) contains tokens with a value of more than 80$ millions. And it is still increasing. 

Actually, you can see lots of token stuck at 0x0 address, for example: AMIS, Arcade, LAToken and many others. Just because of the careless or ignorance of the Smart Contract.

However, as you can see, there are also more than 7 thousands Ether at this 0x0 address. But, Ether could be easily resend back to its original addresses with a special or the next hard-fork. Whereas, sending the tokens back to there owners will nee to be managed and implemented on a case-by-case bases because every token has its own code. So, consider that for the sake of both yourself and your token's holders.

## Prevent transfer to the Contract address
Another human mistake that you should prevent is the transfer to the same address of the smart contract. To see an example of how this could be harmful to innocent people you can check that lots of EOS Tokens (more than 90,000) are just stuck at the contract address. Another example is the LATToken. Tokens were just transferred to the contract address by mistake and the contract did not prevent that.
### Example
To see a good example of implementing both: **Prevent transfer to the 0x0 address** and **Prevent transfer to the contract address**, you can check the smart contract of **[Kyber Network Crystal](https://etherscan.io/address/0xdd974d5c2e2928dea5f71b9825b8b646686bd200#code)** token: 
It is implemented using a modifier validating that the "to" address is not 0x0:

    modifier validDestination( address to ) {
        require(to != address(0x0));
        require(to != address(this) );
        _;
    }
And the modifier should be applied on **both** "transfer" and "transferFrom" methods. The following is an example:

    function transfer(address _to, uint _value)
        validDestination(_to)
        [other modifiers]
        returns (bool) {
        (... your logic ...)
    }

    function transferFrom(address _from, address _to, uint _value)
        validDestination(_to)
        [other modifiers]
        returns (bool) {
        (... your logic ...)
    }
To check a bad example of an ERC20 token that is mintable and have fair complexity, but yet did not take in consideration the above mentioned, you can see [LAToken](https://etherscan.io/address/0xe50365f5d679cb98a1dd62d6f6e58e59321bcddf#code).
