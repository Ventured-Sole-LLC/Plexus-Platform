ADR 006: Ingress for Platform Networking

Status
Accepted

Context
Right now the only way to reach hello-plexus is kubectl port-forward,
a debugging tool, not a real access path. Every app on the platform
is going to need a real way in, and doing that per-app with one-off
LoadBalancer services or port-forwards doesn't scale and doesn't give
a consistent pattern.

Decision
Install an Ingress controller (ingress-nginx) on the cluster.
Applications expose themselves through Ingress resources, one shared
entry point routing to many apps by hostname or path, instead of each
app inventing its own way to be reached.

Reasoning
An Ingress controller gives every app on the platform the same
pattern for exposing itself: define routing rules in Git (same as
everything else on this platform), and the controller handles actual
traffic routing, TLS termination, and host-based routing. That's
consistent with ADR 001, one shared capability instead of every app
solving networking on its own.

Alternatives Considered
LoadBalancer service per app: rejected. Works fine for one app, but
each additional app needing its own external IP/load balancer is
exactly the kind of per-app sprawl the platform exists to prevent.

NodePort: rejected. Works for local testing, doesn't give a real
routing layer, no host-based or path-based rules, no shared TLS
handling.

Consequences

Good:
One consistent way for any app to become reachable.
Routing rules live in Git like everything else on the platform.
Real path to TLS termination and host-based routing later, without
changing the pattern.

Not so good:
One more piece of shared infrastructure to run and keep healthy, if
it goes down, every app behind it is unreachable.
Local kind clusters need extra setup to actually expose the ingress
controller outside Docker, not as simple as it'll be on EKS.

Revisit If
A simpler pattern proves sufficient for how few apps actually end up
on this platform, or the shared ingress controller becomes an
operational bottleneck.
