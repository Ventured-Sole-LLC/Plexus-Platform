ADR 005: ArgoCD as the GitOps Reconciler

Status
Accepted

Context
ADR 002 decided Git holds the desired state. ADR 003 and 004 decided
what runs (Kubernetes) and how it's packaged (Helm). None of that
does anything by itself, something still has to actually watch Git,
compare it to what's running, and reconcile the difference. That's
the piece still missing.

Decision
ArgoCD watches the Git repo holding deployment config and
continuously reconciles the cluster to match it. No one runs
`kubectl apply` or `helm install` by hand, ArgoCD does it whenever
Git changes.

Reasoning
This is the actual mechanism behind ADR 002's promise. Git being the
source of truth only means something if there's a process making
that true, constantly, not just at deploy time. ArgoCD also
continuously checks for drift even when nobody's actively deploying,
if someone manually changes something in the cluster, ArgoCD notices
and can flag or correct it. That's the difference between "Git is
supposed to be the source of truth" and "Git actually is the source
of truth."

Alternatives Considered
Flux: a real, comparable alternative, similar pull-based GitOps
model. Rejected mainly on ecosystem and UI maturity for a first
GitOps platform, ArgoCD's dashboard makes drift and sync status
visible in a way that's easier to build on top of early.

CI-triggered deploys (push-based, GitHub Actions running `helm
upgrade` on merge): rejected as the primary mechanism. This is
closer to what SoleLoop and CleanLoop already do, and it was already
identified in ADR 002 as not providing continuous drift correction,
it only acts at deploy time, not constantly.

Consequences

Good:
Deploys happen by merging to Git, nothing else.
Drift outside of Git gets caught automatically instead of silently
accumulating.
A bad deploy is a Git revert, ArgoCD picks it up and rolls back on
its own.

Not so good:
ArgoCD itself is a real piece of infrastructure to run and secure,
one more thing that can break.
Anyone deploying has to actually trust and understand the
reconciliation loop instead of just running a command and watching it
finish.

Revisit If
ArgoCD's own operational overhead becomes a bigger problem than the
drift it prevents, or a simpler mechanism turns out to give the same
guarantees for how small this platform actually stays.
