ADR 004: Helm for Application Packaging

Status
Accepted

Context
ADR 003 picked Kubernetes as the runtime. Raw Kubernetes manifests
work, but every app ends up with its own pile of near-identical YAML,
deployment, service, ingress, configmap, repeated and hand-edited for
each environment. That's exactly the kind of per-app drift ADR 001
was trying to avoid, just moved into YAML instead of pipelines.

Decision
Applications are packaged as Helm charts. One templated chart per
app, with values files supplying what's actually different per
environment (image tag, replica count, resource limits, and so on).

Reasoning
Helm turns "copy-paste this YAML and hand-edit it" into "fill in a
few values." A new app onboarding to the platform can start from a
shared base chart instead of writing Kubernetes manifests from
scratch, which is the actual point of ADR 001, giving every app a
head start instead of a blank page. It also means dev vs. staging vs.
prod is just a different values file, not a different set of hand-
maintained YAML files that can drift apart from each other.

Alternatives Considered
Raw Kubernetes manifests, no templating: rejected. This is the status
quo problem restated, works fine for one app, turns into copy-paste
drift the moment there's more than one.

Kustomize: a real alternative, patch-based instead of templated.
Rejected mainly because Helm's chart repository model and templating
are a better fit for "one shared golden-path chart reused across
many apps," which is closer to what this platform needs than
Kustomize's overlay-on-a-base approach.

Consequences

Good:
New apps start from a shared chart instead of writing manifests from
scratch.
Environment differences live in small values files, not duplicated
YAML.
Chart versioning gives a real, reviewable history of how an app's
deployment shape has changed over time.

Not so good:
Templating adds a layer of indirection, debugging "what YAML actually
got applied" means rendering the template first.
Chart design itself is a real skill and a real thing to maintain, a
badly designed shared chart can become its own source of drift.

Revisit If
The shared chart ends up so parameterized that it is harder to reason
about than plain manifests would have been, or an app's needs
diverge enough from the golden path that forcing it into the shared
chart causes more friction than it saves.
