
# Mint
Give mintable right to customers.

# Bid
Bidding with ERC20 token.
```js
    function bid(uint256 _id, uint256 _price) external _validateBid(_id, _price) {
        BidEntity memory newBidEntity = BidEntity(_id, _msgSender(), _price);
        bidStatusOfToken[_id][_msgSender()] = true;
        bidArrayOfToken[_id].push(newBidEntity);

        emit BidCreate(_msgSender(), _id, _price);
    }
```

# Purchase
```js
    function buy(uint _id) external payable _validateBuy(_id) {
        address _previousOwner = ownerOf(_id);
        address _newOwner = _msgSender();

        address payable _buyer = payable(_newOwner);
        address payable _owner = payable(_previousOwner);

        _transfer(_owner, _buyer, _id);

        uint256 _commissionValue = price[_id] / (10**2) * feePercent;
        uint256 _sellerValue = price[_id] - _commissionValue;

        _owner.transfer(_sellerValue);
        payable(feeAddress).transfer(_commissionValue);

        if (msg.value > price[_id]) {
            _buyer.transfer(msg.value - price[_id]);
        }

        for (uint i = 0; i < bidArrayOfToken[_id].length; i ++ ) {
            bidStatusOfToken[_id][bidArrayOfToken[_id][i].buyer] = false;
        }
        delete bidArrayOfToken[_id];

        emit Purchase(_previousOwner, _newOwner, price[_id], _id, tokenURI(_id));
    }
```

# Market Fee
3%

# Live Site
https://https://app.artdeco.community/