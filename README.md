# raidencashd-rpc

[![Build Status](https://img.shields.io/travis/solaris-rig/raidencashcore-rpc.svg?branch=master)](https://travis-ci.org/solaris-rig/raidencashcore-rpc)
[![NPM Package](https://img.shields.io/npm/v/raidencashcore-rpc.svg)](https://www.npmjs.org/package/raidencashcore-rpc)

> RaidenCash Client Library to connect to RaidenCash Core (raidencashd) via RPC

## Install

raidencashd-rpc runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install raidencashcore-rpc
```

## Usage

### RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 7170) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('raidencashcore-rpc/promise')` to have promises returned
  - `require('raidencashcore-rpc')` to have callback functions returned
	
### Examples

Config:

```javascript
var config = {
    protocol: 'http',
    user: 'raiden',
    pass: 'local321',
    host: '127.0.0.1',
    port: 17170
};
```

Promise based:

```javascript
var RpcClient = require('raidencashcore-rpc/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })
```

Callback based (legacy):

```javascript
var run = function() {
  var bitcore = require('@dashevo/dashcore-lib');
  var RpcClient = require('raidencashcore-rpc');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

### Help

You can dynamically access to the help of each method by doing

```
const RpcClient = require('raidencashcore-rpc');
var client = new RPCclient({
    protocol:'http',
    user: 'raiden',
    pass: 'local321', 
    host: '127.0.0.1', 
    port: 17170
});

var cb = function (err, data) {
    console.log(data)
};

// Get full help
client.help(cb);

// Get help of specific method
client.help('getinfo',cb);
```

## Contributing


## License

[MIT](LICENSE) &copy; RaidenCash Core Group, Inc.
