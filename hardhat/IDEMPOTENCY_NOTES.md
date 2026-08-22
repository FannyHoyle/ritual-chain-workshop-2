# Idempotency Notes

This was one of the concepts I had to think about for a while.

The resolution callback is scheduled more than once.

That creates a simple question:

What happens if the callback reaches the contract after the market has already
been resolved?

## Simple Example

Imagine:

Attempt 1
→ succeeds

The market is now resolved.

Then imagine a later scheduled execution still reaches the contract.

The contract must not:

- resolve the market again
- pay winners again
- change the result
- overwrite final state

## Why This Is Important

Cancelling future Scheduler calls reduces unnecessary work.

But cancellation should not be treated as the only protection.

The contract itself needs to protect its state.

## Idempotent Behavior

A useful mental model is:

Before resolution:

callback can perform resolution.

After resolution:

callback becomes harmless.

That is what I understand by an idempotent resolution path in this context.

## Invalid State

The same principle applies after an Invalid result.

If the market has already become Invalid, another callback should not turn it
into YES or NO.

## Claim State

This is also related to payouts.

Resolving a market and allowing a user to claim are separate operations.

The contract needs state to make sure a user cannot receive the same payout
multiple times.

## Scheduler and Contract

This gave me a useful separation:

Scheduler:
"Please run this operation."

Contract:
"Is this operation still valid?"

That makes the contract less dependent on the assumption that every scheduled
operation will execute exactly once.

## Why I Like This Design

Blockchain execution can involve asynchronous components.

The Scheduler and external executor introduce additional moving parts.

Making the final state transitions safe against repeated execution makes the
overall system easier to reason about.

## My Takeaway

I originally thought idempotency was just a technical detail.

After looking at the retry flow, I now see it as a necessary safety property
for an autonomous application.
