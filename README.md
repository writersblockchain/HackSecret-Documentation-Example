## HackSecret Sponsor Documentation Example

This repo serves as a template for how HackSecret sponsors can format their documentation for developers. It uses the example of a Secret Business Card dApp. Please adjust your documentation as you see fit, this is simply a template.

#### About your dApp

Secret Business Card is a decentralized application that allows users to create Business Cards on Secret Network and share them with select wallets, with customizable privacy. Users can create cards, edit existing cards, delete cards, and choose which wallets have access to cards through the use of Viewing Keys.

You can demo the dApp [here](https://scrt-business-card.vercel.app/)

```
const chainId = "pulsar-2";
const LCD_URL = "https://api.pulsar.scrttestnet.com"
```

#### dApp Components

###### Create Card

Users have the ability to create cards.

###### Burn Card Card

Users have the ability to delete cards.

###### Query Cards

Users have the ability to Query Cards they've been given access to.

#### Challenge

(Highlights specific interests for Grants, etc)

- Challenge #1: Use Query Permits to grant user access to business cards, in addition to Viewing Keys (Reward: 10k SCRT vested over 1 year)
- Challenge #2: Turn Business Card structs into SNIP-721s (Reward: 1k SCRT)
- Challenge #3: IBC Integration that transfers business cards to other chains in the cosmoos(Reward: 5k SCRT vested over 1 year)

#### Documentation

(Developer Documentation for how HackSecret devs can integrate your dapp/protocol into their project)

Secret Business Card is live on pulsar testnet with contract address: `"secret1auclmxkpw28g8qpxe57gqt4t2hgmuuuapa6sta"`

##### Execute Messages

What follows are examples in secret.js to execute the Secret Business Card Application:

###### Create Card/Viewing Key

```javascript
let createCard = async (name, address, phone, index) => {
  const card_creation_tx = new MsgExecuteContract(
    {
      sender: myAddress,
      contract_address: contractAddress,
      msg: {
        create: {
          card: { name: name, address: address, phone: phone },
          index: parseInt(index),
        },
      },
      code_hash: contractCodeHash,
    },
    { gasLimit: 100_000 }
  );

  let viewing_key_creation = new MsgExecuteContract(
    {
      sender: myAddress,
      contract_address: contractAddress,
      msg: {
        generate_viewing_key: {
          index: parseInt(index),
        },
      },
      code_hash: contractCodeHash,
    },
    { gasLimit: 100_000 }
  );

  const txs = await secretJs.tx.broadcast(
    [card_creation_tx, viewing_key_creation],
    {
      gasLimit: 300_000,
    }
  );
  const viewing_key = txs.jsonLog[1].events
    .filter((x) => x.type === "wasm")[0]
    .attributes.filter((x) => x.key === "viewing_key")[0].value;
  setViewingKey(viewing_key);
  console.log("Viewing Key: ", viewing_key);
};
// createCard();
```

##### Query Messages

What follows are examples in secret.js to query the Secret Business Card Application:

###### Query Existing Cards:

```javascript
let queryCard = async (walletAddress, viewingKey, cardNumber) => {
  let business_card_query_tx = await secretJs.query.compute.queryContract({
    contract_address: contractAddress,
    query: {
      get_card: {
        wallet: walletAddress,
        viewing_key: viewingKey,
        index: parseInt(cardNumber),
      },
    },
    code_hash: contractCodeHash,
  });
};
```
