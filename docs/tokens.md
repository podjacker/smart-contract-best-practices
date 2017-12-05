
# Token Implementation Best Practice
Although, implementing Tokens should comply with other best practices, Tokens has there unique details.

## Comply with the latest standard:
Generally speaking, Token's smart contract has to follow the latest standard. The current widely adopted is the ERC20. Check this industry standard at Wiki: https://theethereum.wiki/w/index.php/ERC20_Token_Standard.
And you may like to check its original discussion on GitHub: https://github.com/ethereum/EIPs/issues/20

An example of a standard ERC20 token is OmiseGo. You can check the code at: 
https://etherscan.io/address/0xd26114cd6EE289AccF82350c8d8487fedB8A0C07#code
An example of a token that does not follow the ERC20 standard is the  "DOT Allocation Indicator" at: https://etherscan.io/address/0xb59f67a8bff5d8cd03f6ac17265c550ed8f33907#code.  Where it implement some but not all of the API.
## Preventing transfer to the 0x0 address!
If you allow human mistakes, it will happen. By the time of writing this document, the address 0x0000000000000000000000000000000000000000 contains tokens with a value of more than 80$ millions! And it is still increasing: https://etherscan.io/address/0x0000000000000000000000000000000000000000. 
You can see at 0x0 address that lots of token stuck, for example: AMIS, Arcade, LAToken and many others. Just because of the careless or ignorance of the  Smart Contract!

However, as you can see, there are also more than 7 thousands Ether at this 0x0 address. But, Ether could be easily resend back to its original addresses with a special or the next hard-fork. Whereas, sending the tokens back to there owners will be very hard to manage and implement. So, consider that for the sake of your token holders.

## Preventing transfer to the contract address
Another human mistake that you should prevent is the transfer to the same address of the smart contract. To see an example of how this could be harmful to innocent people you can check that lots of EOS Tokens (more than 90,000) are just stuck at the contract address. Another example is the LATToken. Tokens were just transferred to the contract address by mistake and the contract did not prevent that.
### Example
To see a good example of implementing both: **Preventing transfer to the 0x0 address** and **Preventing transfer to the contract address**, you can check the smart contract of KyberNetwork token: https://etherscan.io/address/0xdd974d5c2e2928dea5f71b9825b8b646686bd200#code
It is implemented using a modifier validating that the "to" address is not 0x0:

    modifier validDestination( address to ) {
        require(to != address(0x0));
        require(to != address(this) );
        _;
    }
And the modifier should be applied on **both** "transfer" and "transferFrom" methods. The following is an example:

    function transfer(address _to, uint _value)
        [other modifiers]
        validDestination(_to)
        returns (bool) {
        (... your logic ...)
    }

    function transferFrom(address _from, address _to, uint _value)
        [other modifiers]
        validDestination(_to)
        returns (bool) {
        (... your logic ...)
    }
To check a bad example of an ERC 20 token that is min-able and have fair complexity, but yet did not take in consideration the above mentioned, you can see LAToken code at: https://etherscan.io/address/0xe50365f5d679cb98a1dd62d6f6e58e59321bcddf#code