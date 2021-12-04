# BitShares NodeJS Transaction Builder

Each private transaction is considered accepted after being included in the block. Blocks are created every 3 seconds. If we need to perform several operations, their sequential execution can take considerable time. Fortunately, several operations can be included in a single transaction. For this you need to use transaction builder.

## Installaing NodeJS on Ubuntu or OSX (In case you don't have it):

The easiest way to install NodeJS is to use Node Version Manager.

To install nvm for Ubuntu or OSX, use following commands in terminal using a non-root user:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
nvm install v10
nvm use v10
```

## Installing NodeJS BitShares Library:

```
git clone -b master https://github.com/bitshares/btsdex.git
cd btsdex
npm install
```

## Creating BitShares NodeJS script file (transaction.js):

The below example can be tuned to execute any transaction over BitShares Blockchain using API node hence you can use any BitShares Public API node, you will just need to replace parameters under *params* and the operation name *asset_update* in below example with your desired transaction parameters and its operation name.

Use the below links to determine your desired operation name and needed parameters along with their required order:

[bitshares-core available operation names](https://github.com/bitshares/bitshares-core/blob/master/libraries/protocol/include/graphene/protocol/operations.hpp)

[btsdex library parameters serialization and order](https://github.com/bitshares/btsdex/blob/master/packages/serializer/src/operations.js)


Create file transaction.js in main btsdex directory, copy paste the below example and read the notes next to hash:

```js

const BitShares = require("btsdex");
BitShares.connect("wss://dex.iobanker.com/ws"); # replace wss://dex.iobanker.com/ws with API node if you want to use another BitShares API node
BitShares.subscribe('connected', startAfterConnected);

async function startAfterConnected() {
let acc = await new BitShares("username", "password"); # replace with your username and password

# Below are required parameters and structure for the example of operation *asset_update*; every different operation would required different parameters and structure

# Finding what to use in these parameters would require understanding of how BitShares Blockchain is reading the objects, for example *issuer* is referred to the ID *1.2.1787259* of owner account of an Asset, use telegram [BitShares Development](https://t.me/BitSharesDEV) group to ask about required parameters.

let params = {
    fee: {amount: 0, asset_id: "1.3.0"},
    "issuer":"1.2.1787259",
    "asset_to_update":"1.3.5537", 
    "new_options": {
     "max_supply": "1000000000000000", 
     "market_fee_percent": 0, 
     "max_market_fee": "0", 
     "min_market_fee": 0, 
     "issuer_permissions": 79, 
     "flags": 6, 
     "core_exchange_rate": {
      "base": {"amount": 500000, "asset_id": "1.3.0"}, 
      "quote": {"amount": 10000, "asset_id": "1.3.5537"}
      }, 
    "whitelist_authorities": [], 
    "blacklist_authorities": [], 
    "whitelist_markets": [], 
    "blacklist_markets": [],
    "description": "{\"main\":\"Asset Info\",\"market\":\"Market info\"}", 
    "extensions": {"taker_fee_percent": 10}
    }
}

let tx = acc.newTx();
tx.asset_update(params); # Replace asset_update with your desired operation name
await tx.broadcast();
console.log(tx);
}
```

Execute the script file using node:

```
node transaction.js
```
