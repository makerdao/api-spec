# Urn

Ilk objects hold Cdp state - `ink` and `art`.

```graphql
type Urn {
  id:      Address      # urn owner
  ilk:     Ilk          # ilk object
  ink:     Float        # locked gem
  art:     Float        # outstanding debt
  frobs:   [FrobEvent]  # state change events
  bites:   [BiteEvent]  # liquidation events
  created: Datetime
  updated: Datetime
}
```

## State change triggers

Urn state is changed via these functions:

| Call           | Log Event | State change           | Vulcanize Event |
| -------------- | --------- | ---------------------- | --------------- |
| Vat.frob       | DSNote    | Vat.urns[e.i][e.u].ink | FrobEvent       |
| Vat.frob       | DSNote    | Vat.urns[e.i][e.u].art | FrobEvent       |
| Vat.fork       | DSNote    | Vat.urns[e.i][e.u].ink | ForkEvent       |
| Vat.fork       | DSNote    | Vat.urns[e.i][e.u].art | ForkEvent       |
| Cat.bite       | Bite      | Vat.urns[e.i][e.u].ink | BiteEvent       |
| Cat.bite       | Bite      | Vat.urns[e.i][e.u].art | BiteEvent       |

## FrobEvent

An `FrobEvent` is created when `frob` is executed.

```graphql
type FrobEvent {
  ilk:   Ilk         # ilk object at event block height
  urn:   Urn         # urn object at event block height
  dink:  Float       # e.dink - ink delta
  dart:  Float       # e.dart - art delta
  tx:    Tx          # transaction meta
}
```


## ForkEvent

A `ForkEvent` is created when `fork` is executed.

```graphql
type ForkEvent {
  ilk:   Ilk         # ilk object at event block height
  src:   Urn         # urn from where collateral/stablecoin is withdrawn
  dst:   Urn         # urn in which collateral/stablecoin is deposited
  dink:  Float       # e.dink - ink delta
  dart:  Float       # e.dart - art delta
  tx:    Tx          # transaction meta
```

## BiteEvent

An `BiteEvent` is created when `bite` is executed.

```graphql
type BiteEvent {
  id:    Integer     # e.id - bite/bid identifier
  ilk:   Ilk         # ilk object at tx block height
  urn:   Urn         # urn object at tx block height
  bid:   Bid         # bid object at tx block height
  ink:   Float       # e.ink - siezed collateral
  art:   Float       # e.art - siezed debt
  tab:   Float       # e.tab - debt to recover
  tx:    Tx          # transaction meta
}
```

## Urn Query

Basic `Urn` retrieval with postgraphile-style collection filtering:

TODO: Aggregation. History.

```graphql
type Query {

   allUrns(
     first:     Int,
     last:      Int,
     offset:    Int,
     before:    Cursor,
     after:     Cursor,
     orderBy:   UrnOrderBy,
     condition: UrnCondition,
     filter:    UrnFilter
   ): [Urn]

   getUrn(
     ilk: String!
     urn: String!
     blockNumber: Int # optionally retrieve ilk state at a given block height
   ): Urn

   allFrobs(
     first:     Int,
     last:      Int,
     offset:    Int,
     before:    Cursor,
     after:     Cursor,
     orderBy:   FrobOrderBy,
     condition: FrobCondition,
     filter:    FrobFilter
   ): [FrobEvent]

   allBites(
     first:     Int,
     last:      Int,
     offset:    Int,
     before:    Cursor,
     after:     Cursor,
     orderBy:   BiteOrderBy,
     condition: BiteCondition,
     filter:    BiteFilter
   ): [BiteEvent]

   allVatForks(
     first:     Int,
     last:      Int,
     offset:    Int,
     before:    Cursor,
     after:     Cursor,
     orderBy:   ForkOrderBy,
     condition: ForkCondition,
     filter:    ForkFilter
   ): [ForkEvent]

}
```
