# `neutron-1` Chain Details

The `neutron-1` chain will be launched as a consumer chain with Cosmos Hub network as provider chain.

## Parameters

Below are the `neutron-1` chain parameters:

| Name          | Value                 |
|-----------|------------------|
| Chain-ID  | `neutron-1`      |
| denom     | `untrn`          |
| minimum-gas-prices | `0untrn` |
| timeout_commit | `1s`          |
| genesis_time | `2023-05-10T15:00:00.000000Z` |
| spawn_time | `2023-05-08T11:00:00Z` |


## Binary

The release binary information is provided below:

| Item         | Description                                             |
|--------------|---------------------------------------------------------|
| GitHub repo  | [neutron-org/neutron](https://github.com/neutron-org/neutron.git) |
| Release      | [`v1.0.1`](https://github.com/neutron-org/neutron/releases/tag/v1.0.1) |
| Reference binary | [neutrond-linux-amd64](./neutrond-linux-amd64) |
| Checksum (sha256) | 82b608543f2989c7c631d555767800e48f54ede389d193913136b41e5c3293ab |

> The `neutrond-linux-amd64` binary is only provided to verify the SHA256. It was built with Interchain Security release [`v1.0.1`](https://github.com/neutron-org/neutron/releases/tag/v1.0.1). You can generate the binary following the build instructions in that repo.

The Cosmos Hub was recently upgraded to [v9.1.0](https://github.com/cosmos/gaia/releases/tag/v9.1.0), which bumps ICS to [v1.1.0-multiden](https://github.com/cosmos/interchain-security/tree/v1.1.0-multiden). This version introduces two new parameters to prevent Replicated Security’s logic for handling rewards to be abused as a DOS vector.

This required a release of a new version of Neutron, `v1.0.1`, which must be used for the mainnet launch. The only difference between `v1.0.0-rc1`, which was used in the proposal, and `v1.0.1` is the upgrade of Interchain Security to the latest release, `v1.2.0-multiden`: https://github.com/neutron-org/neutron/compare/v1.0.0-rc1..v1.0.1.

**⚠️ All validators are required to use `v1.0.1` during the mainnet launch. The instructions on how to build `v1.0.1` can be found in the [How to Join](#how-to-join) section.**

## Genesis

The final `genesis.json` information is provided below:

| Item         | Description                                             |
|--------------|---------------------------------------------------------|
| Genesis  |  | [genesis.json](neutron-1-genesis.json)| 
| Checksum (sha256) | b9c73221623ef35a2772babff3ce55ee95f8f8fed23a2efcefe1cb79cd092cf5 |


**⚠️ All validators are required use the [genesis.json](neutron-1-genesis.json) file provided in this instruction.**

If you want to get a better idea of what was changed in the genesis since Proposal 792, please read the sections below.

---

#### `ccvsonsumer` section updates

Due to the upgrade of the ICS dependency to [v1.1.0-multiden](https://github.com/cosmos/interchain-security/tree/v1.1.0-multiden), and due to using a version of ICS with soft opt-out,  there is 3 parameters that were added to the `ccvconsumer` section of the genesis:

```json
{
  "soft_opt_out_threshold": "0.05",
  "reward_denoms": [
    "untrn"
  ],
  "provider_reward_denoms": [
    "uatom"
  ]
}
```

1. `rewards_denoms`: rewards denominations from the Consumer chain (e.g. uNTRN or IBC denoms used on the consumer chain);
2. `provider_rewards_denoms`: rewards denomination from the Provider chain (e.g. uATOM);
3. `soft_opt_out_threshold`: this parameter was set to `0.05` to allow the smaller validators to opt out of validating Neutron without being slashed.

#### `slashing` parameters

In the pre-genesis, we used incorrect `slashing` parameters. Below you can see the `slashing` parameters diff:

 ```
+ "signed_blocks_window": "140000",
+ "min_signed_per_window": "0.050000000000000000",
+ "slash_fraction_downtime": "0.000100000000000000"
- "signed_blocks_window": "100",
- "min_signed_per_window": "0.500000000000000000",
- "slash_fraction_downtime": "0.010000000000000000"
```

1. `signed_blocks_window` was increased to approximately 4 days given a `2.5s` block production time that we are expecting. This is done to give the validators more time to recover a failed node;
2. `min_signed_per_window` was adjusted to match the value used by he Cosmos Hub;
3. `slash_fraction_downtime` was adjusted to match the value used by he Cosmos Hub.

#### `wasm` artifacts

Neutron genesis instantiates dozens of smart contracts that are used by the Neutron DAO and the Neutron Token Generation Event. Since the time when Proposal 792 was published, we fixed some bugs and introduced some improvements to our smart contracts.

#### Building the genesis from scratch

We have a [script](https://github.com/neutron-org/tools/blob/mainnet/genesis/genesis.sh) that can be copied to an empty directory and run to get the final genesis. We provide this script **for information purposes only**, and we do not guarantee that it will work on your machine (although if you have a Mac and the Docker demon is running, it should produce the final `genesis.json` within approximately 30 minutes). **PLease use the [genesis.json](neutron-1-genesis.json) file provided in this instruction.**

## Endpoints

Seed nodes:

1. `e2c07e8e6e808fb36cca0fc580e31216772841df@p2p-erheim.neutron-1.neutron.org:26656`

Persistent nodes:

**TBA**

The following state sync node serve snapshots every 1000 blocks:

**TBA**

## How to Join

### Hardware Requirements

* 4 Cores
* 32 GB RAM
* 2x512 GB SSD

### Software Versions

| Name               | Version  |
|--------------------|----------|
| Neutron            | v1.0.1   |
| Go                 | >1.20    |


### Node manual installation

Build and install the Neutron binary. 

```
$ git clone -b v1.0.1 https://github.com/neutron-org/neutron.git
$ cd neutron
$ make install
```

after installation please check installed version by running:

`neutrond version --long`

You should see the following:
```
name: neutron
server_name: neutrond
version: 1.0.1 
commit: 2a9333c935b321cfa2cf213fb9adb38661d7f349
``` 

or you can download binary directly from our [official release](https://github.com/neutron-org/neutron/releases/tag/v1.0.1).
