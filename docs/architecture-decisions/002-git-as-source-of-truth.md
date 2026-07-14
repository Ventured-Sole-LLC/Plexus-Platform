ADR 002: Git as the Source of Truth for Deployment State

Status
Accepted

Context
The platform needs a deployment model. Right now, what's actually
running lives wherever the last person left it, a manual command, a
CI run that's already finished, or just someone's memory.

Decision
Deployment state gets declared in Git. Whatever's committed is what
should be running, full stop, not something pushed one-off through a
command or pipeline.

Reasoning
If the desired state lives in Git, you can always check what's
actually running against what's supposed to be running. Drift becomes
something you can see and fix, not something invisible. And rolling
back a bad deploy becomes a Git revert instead of trying to remember
what the last good state even was.

Alternatives Considered
Keep pushing deploys manually or through one-off CI runs: this is the
status quo, and it's exactly the problem ADR 001 called out. No real
record of what's supposed to be running, no clean way to catch drift.

Treat CI run history as the source of truth instead: rejected. A CI
run is a past event, not a live declaration of current state. Trying
to diff two old runs to figure out what should be running right now
is way harder than just reading a file.

Consequences

Good:
Every deploy is reviewable and versioned, same as any other code
change.
Drift becomes something you can actually catch.
Rolling back is a Git revert, not manual cleanup.

Not so good:
Needs something watching Git and actually applying changes, that's
new infrastructure, not free.
Deploying now goes through Git instead of a quick one-off command.

Revisit If
The thing reconciling Git turns out unreliable or too costly to run,
or something better comes along that gives the same guarantees
without needing a Git-centric setup.
