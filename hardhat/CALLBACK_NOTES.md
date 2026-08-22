# Scheduler Callback Notes

The Scheduler was one of the parts of the workshop that made the project feel
different from a normal smart contract.

In a normal contract, something usually happens because a user sends a
transaction.

Here, the market needs to resolve later without requiring the market creator
to manually call it.

## Basic Flow

The market is created.

The contract schedules resolution.

Time passes.

The Scheduler calls the contract.

The contract starts the external resolution process.

This is the basic idea behind the self-resolving market.

## Why Schedule Multiple Calls?

The workshop schedules three attempts.

The attempts are separated by a fixed block frequency.

The reason is that external resolution can fail.

A single failure should not necessarily make the market unusable.

## Attempt 1

The Scheduler triggers the resolution.

If everything succeeds, the market resolves.

If something fails, another attempt remains.

## Attempt 2

The second attempt provides another opportunity.

The executor selection can also be changed through the retry mechanism.

## Attempt 3

The final scheduled attempt provides one last chance.

If all attempts fail, the market can become Invalid.

## Successful Resolution

When resolution succeeds, the remaining scheduled executions are no longer
needed.

The contract can cancel the remaining calls.

## Why Cancellation Is Not Enough

This was an interesting part for me.

I initially assumed:

cancel scheduled call
=
callback can never happen

But a distributed system needs to be defensive.

The callback itself should be safe if it arrives after the market has already
reached a final state.

## Idempotent Callback

The resolution callback needs to be idempotent.

In simple terms:

running it again should not settle the market a second time.

For example:

First callback:
market = resolved

Later callback:
market is already resolved

The second callback should not distribute another payout.

## My Mental Model

The Scheduler controls when an attempt happens.

The contract controls whether that attempt is still relevant.

This distinction helped me understand why both scheduling and contract-side
state checks are necessary.
