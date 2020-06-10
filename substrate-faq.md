This page records Substrate related questions and answers that we know of.  
To contribute, refer to the [contribution rules](#contribution-rules).

#### Validators could not finalize blocks even when online all the time. What is the solution?

- applicable Substrate: <`v2.0.0-rc2`

Updating to Substrate v2.0.0-rc2 helps.

*Last updated: 2020-06-10*

#### How is transaction fee calculated?

- applicable Substrate: `master`

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

See [this PR](https://github.com/paritytech/substrate/pull/6297)

Under review now. Probably the code need to be updated. 

*Last updated: 2020-06-10*

#### How to migrate a Substrate network from PoA to PoS consensus?

- applicable Substrate: `v2.0.0-rc3`

1. To start proof-of-authority, the validators are just hard-coded into the chain specification.
Validator elections only take place on era change.

2. The era-forcing mode is described by the [forcing enum from the staking pallet](https://github.com/paritytech/substrate/blob/v2.0.0-rc3/frame/staking/src/lib.rs#L917).

3. The era-forcing mode for proof-of-authority is ForceNone.

4. Migrating to proof-of-stake only requires calling the [force_new_era function](https://github.com/paritytech/substrate/blob/v2.0.0-rc3/frame/staking/src/lib.rs#L1812), which changes the era-forcing mode from ForceNone to ForceNew.

*Last updated: 2020-06-10*

#### What is the recommended practice to perform runtime migration in a fail-safe / data-restorable way?

1. [Substrate Seminar on runtime upgrade](https://youtu.be/0eNGZpNkJk4).

2. [Gav script on upgrading Kusama network](https://hackmd.io/mGgNZX0VT4S0UTaq89-_SQ) for reference. Tweak accordingly.

3. Use `export-blocks` and `import-blocks` sub-commands to export blocks and try out the migration in a single node dev environment.

*Last updated: 2020-06-10*

---

## Contribution Rules

```
#### The question here

- applicable Substrate: (please use Substrate tag defined here: https://github.com/paritytech/substrate/tags) / `master`

The answer here

*Last updated: 2020-06-10 (optional: contributor)*
```
