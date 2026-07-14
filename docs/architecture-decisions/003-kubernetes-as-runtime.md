ADR 003: Kubernetes as the Application Runtime

Status
Accepted

Context
ADR 001 set the goal, shared platform capabilities for any app. ADR
002 decided Git holds the desired state. Now something needs to
actually run applications and reconcile them to match what's in Git.

Decision
Kubernetes is the runtime. Applications run as pods, deployments, and
services on a cluster, with namespaces separating environments and
apps from each other.

Reasoning
This isn't about Kubernetes being better than ECS, ECS runs
CleanLoop fine and plenty of real platform teams build good internal
platforms on it. The reason for Kubernetes here specifically is
portability: it's a standardized API that isn't tied to one cloud
provider, so the platform isn't locked into AWS the way an ECS-based
one would be. Namespaces, RBAC, and service discovery also give a
real multi-app, multi-team model out of the box, which is exactly
what a shared platform needs instead of something built for a single
service.

Alternatives Considered
ECS/Fargate: rejected for this specific role, not because it's bad,
CleanLoop already proved it works well for a single containerized
service. It's AWS-specific, though, and a shared platform meant to
outlast any one product benefits from not being tied to one cloud's
container service.

Nomad: a real alternative, simpler to operate than Kubernetes in a
lot of ways. Rejected mainly because Kubernetes has the bigger
ecosystem (Helm, ArgoCD, the ingress/RBAC/namespace model already
decided on), and that ecosystem is a big part of what makes this
platform useful instead of just another orchestrator.

Consequences

Good:
Not locked into one cloud provider's container service.
Namespaces and RBAC give real per-app, per-environment isolation from
day one.
Huge ecosystem (Helm, ArgoCD, ingress controllers) to build the rest
of the platform on.

Not so good:
Real operational complexity, way more moving parts than ECS.
Genuinely new skill investment, this isn't something CleanLoop's ECS
work transfers into directly.

Revisit If
The operational overhead of running Kubernetes outweighs the
portability benefit for what's actually being deployed on it, or the
portfolio ends up staying AWS-only long enough that the multi-cloud
case never actually happens.
