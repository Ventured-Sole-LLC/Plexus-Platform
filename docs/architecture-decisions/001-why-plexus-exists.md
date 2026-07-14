ADR 001: Why an Internal Application Platform Exists

Status
Accepted

Context
Ventured Sole has a growing set of cloud applications, each deployed
and run its own way right now. Every new one means another pipeline,
another config setup, another set of operational habits, and another
place for things to drift out of sync. None of that knowledge lives
anywhere reusable, it just piles up inside each project.

Decision
Build an internal platform that handles the stuff every application
needs: deployment, secrets, networking, observability, rollback,
autoscaling, policy. One shared set of capabilities instead of
reinventing them every time.

Reasoning
This isn't about deploying one more app. It's about not having to
solve deployment, security, and ops from scratch every single time
something new gets built. What technology actually implements this is
a separate call, made later, one piece at a time.

Alternatives Considered
Keep doing it per-project: this is what's happening now, and it's the
actual problem. Drift, inconsistent controls, knowledge stuck in
whoever built the thing.

Fix each project's setup on its own: might reduce drift within one
project, but a new app still starts from zero instead of picking up
something already built.

Consequences

Good:
A new app gets real capabilities on day one instead of starting cold.
Ops knowledge (rollback, secrets, health checks) becomes something the
platform knows, not something stuck in someone's head.
Drift becomes visible instead of silent.

Not so good:
Building a platform is real work that doesn't ship product on its own.
Anything that adopts it takes on a dependency it didn't have before.

Revisit If
The portfolio stops growing and maintaining this costs more than the
drift it prevents, or it turns out these aren't even the right
capabilities to standardize.
