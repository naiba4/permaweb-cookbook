## Installing the arweave-js Package

```console:no-line-numbers
npm install --save arweave
```

## Initializing arweave-js
Direct Layer 1 transactions are posted using the `arweave-js` library.

```js:no-line-numbers
import Arweave from 'arweave';
import fs from "fs";

// load the JWK wallet key file from disk
let key = JSON.parse(fs.readFileSync("walletFile.txt").toString());

// initalize an arweave instance
const arweave = Arweave.init({});
```

## Posting a wallet-to-wallet Transaction
A basic transaction to move AR tokens from one wallet address to another.
```js:no-line-numbers
//  create a wallet-to-wallet transaction sending 10.5AR to the target address
let transaction = await arweave.createTransaction({
  target: '1seRanklLU_1VTGkEk7P0xAwMJfA7owA1JHW5KyZKlY',
  quantity: arweave.ar.arToWinston('10.5')
}, key);

// you must sign the transaction with your key before posting
await arweave.transactions.sign(transaction, key);

// post the transaction
const response = await arweave.transactions.post(transaction);
```

## Posting a Data Transaction
This example illustrates how load a file from disk and create a transaction to store its data on the network. You can find the current price the network is charging at [https://ar-fees.arweave.dev](https://ar-fees.arweave.dev)
```js:no-line-numbers
// load the data from disk
const imageData = fs.readFileSync(`iamges/myImage.png`);

// create a data transaction
let transaction = await arweave.createTransaction({
  data: imageData
}, key);

// add a custom tag that tells the gateway how to serve this data to a browser
transaction.addTag('Content-Type', 'image/png');

// you must sign the transaction with your key before posting
await arweave.transactions.sign(transaction, key);

// create an uploader that will seed your data to the network
let uploader = await arweave.transactions.getUploader(transaction);

// run the uploader until it completes the upload.
while (!uploader.isComplete) {
  await uploader.uploadChunk();
}
```
## Resources
* For an overview of all the ways you can post transactions, see the [Posting Transactions](../../concepts/postTransaction.md) section of the cookbook.

* For a more detailed description of all `arweave-js`'s features see the documentation [on github](https://github.com/ArweaveTeam/arweave-js)