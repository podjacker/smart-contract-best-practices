# Token Implementation Best Practice
Although, dealing with Tokens should comply with other best practices, Tokens has there unique details.

## Comply with the latest standard:
Generally speaking, Token's smart contract has to comply with the latest standard. The current widely adapted is the ERC20. Check this industry standard at Wiki: https://theethereum.wiki/w/index.php/ERC20_Token_Standard
And you may like to check its original discussion on GitHub: https://github.com/ethereum/EIPs/issues/20

## Preventing transfer to the 0x0 address!
If you allow human mistake, they will happen. By the time or writing this document, the address 0x0000000000000000000000000000000000000000 contains tokens with a value or more than 80 millions! And it is still increasing: https://etherscan.io/address/0x0000000000000000000000000000000000000000. As you can see, there are also more than 7 thousands Ether at this address. But Ether could be easily resend back to its original addresses with a special or the next hard fork. Whereas, sending the tokens back to there owners will be very hard to manage and implement. So, consider that for the sack of your token holders.

## Preventing transfer to the contract address
Another human mistake that you should prevent is the transfer to the same address of the smart contract. To see an example of how this could be harmful to innocent people you can check that lots of EOS Token (more than 90,000) is just stuck at the contract address. They are just transferred to the contract address by mistake and the contract did not prevent that.
### Example
To see a good example of implementing both this and the above best practice, you can check the smart contract of KyberNetwork token: https://etherscan.io/address/0xdd974d5c2e2928dea5f71b9825b8b646686bd200#code
It is implemented using a modifier that validate that the "to" address is not 0x0:

    modifier validDestination( address to ) {
        require(to != address(0x0));
        require(to != address(this) );
        _;
    }
And the modifier should be applied on **both** "transfer" and "transferFrom" methods. The following is an example of how to do so:

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

