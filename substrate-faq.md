This page records Substrate related questions and answers that we know of.  
To contribute, refer to the [contribution rules](#contribution-rules).

### Validators could not finalize blocks even when online all the time. What is the solution?

- Substrate ver: `< v2.0.0-rc2`

1. Updating to Substrate v2.0.0-rc2 helps.
2. (Fallback) Sometimes even rc2 could get stuck. So create a script to monitor the finlized block and restart Substrate if happening.

*Last updated: 2020-06-10*

### How is transaction fee calculated?

- Substrate ver: `v2.0.0-rc3`

Currently the code is a bit different from the specification.

What's in the spec:

```
some_value = (1 + (v * diff) + (v * diff)^2 / 2)
new_weight = old_weight * some_value
```

What's actually in the code:

```
some_value = (1 + (v * diff) + (v * diff)^2 / 2)
new_weight = old_weight + some_value
```

Refer to: [paritytech/substrate#6297](https://github.com/paritytech/substrate/pull/6297)

Under review now. Probably the code need to be updated. 

*Last updated: 2020-06-10*

### How to migrate a Substrate network from PoA to PoS consensus?

- Substrate ver: `v2.0.0-rc3`

1. To start proof-of-authority, the validators are just hard-coded into the chain specification.
Validator elections only take place on era change.

2. The era-forcing mode is described by the [forcing enum from the staking pallet](https://github.com/paritytech/substrate/blob/v2.0.0-rc3/frame/staking/src/lib.rs#L917).

3. The era-forcing mode for proof-of-authority is ForceNone.

4. Migrating to proof-of-stake only requires calling the [force_new_era function](https://github.com/paritytech/substrate/blob/v2.0.0-rc3/frame/staking/src/lib.rs#L1812), which changes the era-forcing mode from ForceNone to ForceNew.

5. Remove all the invulnerable validators by calling [set_invulnerables](https://github.com/paritytech/substrate/blob/v2.0.0-rc3/frame/staking/src/lib.rs#L1841). This removes the genesis authorities from the invulnerables.

*Last updated: 2020-06-10*

### What is the recommended practice to perform runtime migration in a fail-safe / data-restorable way?

- Substrate ver: `v2.0.0-rc3`

1. [Substrate Seminar on runtime upgrade](https://youtu.be/0eNGZpNkJk4).

2. [Gav script on upgrading Kusama network](https://hackmd.io/mGgNZX0VT4S0UTaq89-_SQ) for reference. Tweak accordingly.

3. Use `export-blocks` and `import-blocks` sub-commands to export blocks and try out the migration in a single node dev environment.

*Last updated: 2020-06-10*

### How to do a^(b/c), where 0 <= b/c <= 1?

- Substrate ver: `v2.0.0-rc3`

Use [substrate-fixed](https://github.com/encointer/substrate-fixed) library

Particularly, this is [where `pow` is implemented](https://github.com/encointer/substrate-fixed/blob/master/src/transcendental.rs#L320-L353).

*Last updated: 2020-06-19*

### How to use PolkadotJS-API to sign as secp256k1 (bitcoin) signature

Right now polkadot-JS API keyring only supports `ed25519`, `sr25519` and `ecdsa` signature. [Detail code here](https://github.com/polkadot-js/common/blob/master/packages/keyring/src/keyring.ts#L43).

At one point, Jaco seems to have integrated secp256k1 signature, but decided not to go ahead as it bloats the binary.

- https://github.com/polkadot-js/wasm/issues/6
- https://github.com/polkadot-js/wasm/pull/9/files

But Jaco confirm it supports secp256k1, via `ecdsa` type.

ref:

- https://cryptobook.nakov.com/digital-signatures/ecdsa-sign-verify-messages#ecdsa-sign

*Last updated: 2020-06-30*

### How do you set custom enum type in polkadot-JS API side?

Refer to here: https://polkadot.js.org/api/start/types.extend.html#user-defined-enum

*Last updated: 2020-06-30*

### How do submit extrinsics with dependencies?

Use [utility/batch] extrinsic call

*Last updated: 2020-06-30*

### What is the best way of signing extrinsic off-chain and then sign by a user and submit to Substrate via RPC?

Use [txwrapper](https://github.com/paritytech/txwrapper), and [substrate-subxt](https://github.com/paritytech/substrate-subxt)

*Last updated: 2020-08-18*

### Would a validator node be able to connect again if it has been disconnected for over a one epoch long? 

BABE has the requirement has one epoch need to have at least one block being produced. If only a few validators from the whole network are disconnected, the validators would still be able to reconnect, just staked tokens being slashed. If the all validators has been disconnected for an epoch, then it won't be able to produce block. You have to use the hack of reverting the node system clock time to less than one epoch.

*Last updated: 2020-08-18*

### How could you retrieve the previous events in the block?

- First, your node need to be run in archive mode (`--pruning archive`), or as validator. Otherwise it only keep historical events of the most recent 256 blocks. 

- Then 

  ```
  const hash = await api.query.system.blockHash(blockNum);
  const evs = await api.query.system.events.at(hash);

  evs.forEach(({event, phase}) => {
    console.log(`${event.section}:${event.method}, data: ${event.data}`);
  })
  ```

- check that `system::ExtrinsicSuccess` event exists.

*Last updated: 2020-08-18*

### How to convert a usual public address into polkadot live network address?

```
const keyring = new Keyring({ type: 'sr25519' });
const mnemonic = mnemonicGenerate();
const hexPair = keyring.addFromUri(mnemonic);
const polkadot_addr = keyring.encodeAddress(hexPair.address, 0);
```

Regarding which integer to pass in as the 2nd param, 

- For polkadot live, use `0`
- For Kusama, use `2`
- For generic Substrate addr, use `42`

Ref to [Polkadot wiki](https://wiki.polkadot.network/docs/en/learn-accounts)

*Last updated: 2020-08-18*

### How could you construct the extrinsics(transaction) details from traversing Substrate blockchain?

You need to retrieve the block info, with `api.rpc.chain.getBlock(Hash)` to get the blockInfo, and then you have enough details to construct the extrinsics detail back.

Refer to [this section of code in `api-sidecar`](https://github.com/paritytech/substrate-api-sidecar/blob/master/src/services/blocks/BlocksService.ts#L177-L205)

Right now there is no way to get the extrinsic details from just the extrinsic hash natively on a Substrate chain. You need the block number.

*Last updated: 2020-08-18*

## Unsolved Problems

### How could you add logic so only have one node to run offchain worker instead of all validator nodes running?

---

## Contribution Rules

Please follow this template:

```
### The question here

- Substrate ver: Substrate tag defined at: https://github.com/paritytech/substrate/tags.

The answer here

*Last updated: 2020-06-10 (optional: contributor)*
```
