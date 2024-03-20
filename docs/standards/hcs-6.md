# HCS-6 Standard: Dynamic Hashinals

### Status: Draft

### Table of Contents

- [HCS-6 Standard: Dynamic Hashinals](#hcs-6-standard-dynamic-hashinals)
    - [Status: Draft](#status-draft)
    - [Table of Contents](#table-of-contents)
  - [Authors](#authors)
  - [Abstract](#abstract)
  - [Motivation](#motivation)
  - [Specification](#specification)
    - [Creating an HCS-2 Topic ID](#creating-an-hcs-2-topic-id)
    - [Metadata](#metadata)
    - [Submitting Messages](#submitting-messages)
    - [Validation](#validation)
    - [Conclusion](#conclusion)

## Authors
- Kantorcodes [https://twitter.com/kantorcodes]()

## Abstract

This standard introduces a way to inscribe **Hashinals** whose `metadata` can be updated at a whim by utilizing an [HCS-2](./hcs-2.md), non-indexed Topic ID.

## Motivation

Creators of NFT Collections, sometimes desire the ability to change the metadata of an NFT, for example in cases of:

- correcting mistakes in metadata during initial inscription
- providing holders with new variations of artwork / attributes over time
- updating numerical stats from leveling up or down during a game

The list above is non-exhaustive and can be expanded to many use cases.

## Specification

Creating a Dynamic **Hashinal** involves two steps.

1. Creating a valid, `non-indexed` [HCS-2](./hcs-2.md) Topic ID
2. Minting a new Serial Number on a Token ID following the [Metadata](#metadata) format
3. Creating a valid [HCS-1](./hcs-1.md) Topic ID
4. Submitting a valid message to the HCS-2 Topic ID with the HCS-1 file.

Steps 2-4 can be repeated every time the NFT will be updated.

### Creating an HCS-2 Topic ID

The `memo` field for Dynamic Hashinals must follow this format to be valid.

``hcs-2:non-indexed:{ttl}`

The only variable element in the memo would be the `ttl` field. We suggest a longer `ttl` as this is the time in seconds, that gateways and clients will store the previous version of your `metadata` in their cache. In the future, gateways and clients may decide to prioritize Topics with longer `metadata`, impose fees, etc. The minimum `ttl` must be `86400` (1 day) to be valid.

### Metadata

Dynamic **Hashinals** follow all of the same rules described in [HCS-5](./hcs-5.md), with one main exception being that they will utilize the `HCS-2` hcsStandard instead of `HCS-1`.

The format of the `metadata` on a dynamic **Hashinal** is as follows:

`hcs://hcs-2/{topicId}`

`topicId` is a valid HCS-2 Topic ID in which data for this NFT written to.

### Submitting Messages

In a `non-indexed` Topic ID, the most recent `register` operation is the current pointer for your metadata. You can submit a new message to update the `metadata` within the `ttl` specified on the `memo` of your HCS-2 Topic ID.

Each message follows this format.

```json
{
  "p": "hcs-2",
  "op": "register",
  // the HCS-1 Topic ID
  "t_id": "0.0.12345",
  "m": "Season 2 update for the Hashinal Hounds",
}
```

Clients, dApps, gateways, etc, will read the most recent sequence number's `t_id` to locate the current `metadata` of this Dynamic **Hashinal**

### Validation

Dynamic **Hashinals** are only valid when

- Their HCS-2 Topic ID is `non-indexed`
- The latest message in the HCS-2 Topic ID has a valid [HCS-1 Topic ID](./hcs-1.md) for the `t_id` field
- They specify a `ttl` that is at least `86400` (1 day)

### Conclusion