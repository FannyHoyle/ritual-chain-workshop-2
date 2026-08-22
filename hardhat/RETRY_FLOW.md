# Scheduler Retry Flow

I wrote this down because the three-attempt resolution process was easier to
understand as a state machine.

## Normal Success

Attempt 1
  ↓
success
  ↓
market resolved
  ↓
cancel remaining attempts

This is the simplest case.

## First Failure

Attempt 1
  ↓
failure
  ↓
Attempt 2
  ↓
success
  ↓
market resolved

The first failure does not determine the market result.

## Two Failures

Attempt 1
  ↓
failure
  ↓
Attempt 2
  ↓
failure
  ↓
Attempt 3
  ↓
success
  ↓
market resolved

The market gets another opportunity.

## Three Failures

Attempt 1
  ↓
failure

Attempt 2
  ↓
failure

Attempt 3
  ↓
failure

No valid external result exists.

The market can become Invalid.

## Why Retry?

There are several possible reasons an attempt can fail:

- executor unavailable
- HTTP failure
- invalid response
- jq extraction failure
- external service problem

These failures do not necessarily mean the market itself is invalid.

They may simply be temporary.

## Why Three?

The workshop uses a fixed number of attempts.

This provides bounded retry behavior.

The contract does not wait forever.

## New Executor Selection

The retry flow is also interesting because the executor selection can use a
different seed on another attempt.

That means the system is not necessarily asking the same service to repeat the
same failed operation.

## What Happens After Success?

The market has a final result.

The remaining scheduled calls are unnecessary.

They can be cancelled.

## What If a Leftover Call Arrives?

The callback should be safe.

The market already has a final state.

The second execution should not create a second settlement.

## My Takeaway

The Scheduler is not the part that decides whether the prediction is YES or
NO.

It is the mechanism that gives the market an opportunity to execute its
resolution logic at the appropriate time.

The contract remains responsible for the final state transition.
