# Callback Edge Cases

I made this list to understand what could happen if callbacks arrive at
unexpected times.

## Case 1: First Callback

The market is open for resolution.

The callback starts the resolution process.

## Case 2: First Callback Fails

The resolution does not produce a final result.

A later attempt can run.

## Case 3: Second Callback

The market is still unresolved.

The second attempt can try to resolve it again.

## Case 4: Second Callback Succeeds

The market reaches a final state.

The remaining attempts are no longer necessary.

## Case 5: Third Callback After Success

This is the case I found most interesting.

The contract must recognize that the market has already been resolved.

It should not settle the market again.

## Case 6: Callback After Invalid

If all resolution attempts failed and the market became Invalid, another callback
should not turn it back into a normal YES/NO result.

## Case 7: Duplicate Resolution

A repeated callback should not create a second payout.

This is one of the reasons state checks are important.

## Case 8: Claim After Resolution

A winning user can claim after a valid resolution.

This is separate from the Scheduler callback itself.

## Case 9: Repeated Claim

A user who already claimed should not be able to claim the same entitlement
again.

## Case 10: Cancelled Execution

The contract attempts to cancel unnecessary scheduled executions after
successful resolution.

The callback should nevertheless be safe if an execution arrives after the
market has already reached a final state.

## General Principle

I think the useful principle here is:

"Do not assume the scheduler behaves exactly like a single-use queue."

Instead, the contract should make its own state transitions safe.

## What I Learned

The Scheduler handles scheduling.

The contract handles correctness.

That separation is probably the most useful thing I learned from following
the callback path.
