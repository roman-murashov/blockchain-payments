# Install
`npm install blockchain-payments`

# About
Blockchain.info payment api (v2) module

# Example
```js
const BlockchainPayments = require("blockchain-payments");
const express = require("express");
const app = express();

let xpub = "xpub"; // xpub
let key = "key"; // blockchain key
let notifySecret = "secret"; // secret key for notification

const blockchain = new BlockchainPayments(xpub, key, notifySecret);

// Create address for payment
app.post('/payments/bitcoin/address/', (req, res, next) => {    
    blockchain.createAddress({
        callback: 'http://example.com'
    }, {
      someCallbackField1: '1',
      someCallbackFieldw2: '2',
    }).then((data) => {
        console.log(data) // {address: ..., index: ..., callback: ...}
    })
});

BlockchainPayments.toBTC(500, 'USD').then((amountInBTC) => {
    // converting USD(or other) to BTC;
});

// notification handler
let successHandler = (data, callback) => {
    // data === req.query    
    // save payment info in db e.t.c    
    // callback() or return promise
};

let errorHandler = (err, meta) => {
    // you can save something to a file, db e.t.c.
    // operation must be synchronous or in the background
};

let confirmationsCount = 8; // count of confirmations for acception, default is 6

app.get('payments/notification/', blockchain.notify(successHandler, errorHandler, confirmationsCount));

```

# Description  
You can write custom notification handler, but library version includes data/authentication validation and automatically sends all headers in the necessary format

# API
### .constructor(xpub, key, notifySecret, [notifyCallback])  
you can find "xpub" and "key" in your blockchain account, notifySecret you have to come up yourself

### .createAddress(query, [callbackQuery])
returns promise, creates bitcoin address for client payment  
you can set callback url query params with callbackQuery as js object

### .notify(fn, onError, [confirmationsCount])
notification handler, it is "connect" middleware

# Class methods
### .toBTC(amount, currency)
converts an amount from a given currency to BTC, returns promise
