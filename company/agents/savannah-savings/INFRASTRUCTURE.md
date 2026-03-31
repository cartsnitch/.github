# Infrastructure Information

### Deployment Targets

* Production/Demo
  * Namespace: cartsnitch
  * FQDN: cartsnitch.farh.net
* Development
  * Namespace: cartsnitch-dev
  * FQDN: cartsnitch.dev.farh.net

### Deployment Pipeline

Deployment is a **2-stage Flux GitOps pipeline**.

**Stage 1 — CI (GitHub Actions, runs in each application repo):**
- Triggered automatically on every merge to `main`
- Builds and tags the Docker image: CalVer (`YYYY.MM.DD[.N]`), `latest`, and `sha-<hash>`
- Pushes tagged images to `ghcr.io/cartsnitch/<service>`
- Creates a CalVer git tag in the source repo

**Stage 2 — GitOps (Flux, managed externally):**
- A Flux cluster bootstrap repo (outside agent access) targets `cartsnitch/infra` as a Flux `GitRepository` source
- `cartsnitch/infra` is the **target** GitRepository — it is **not** a Flux bootstrap/cluster repo and must never be treated as one
- Flux reconciles Kustomize overlays on every commit to `infra` main:
  - `apps/overlays/dev` → namespace `cartsnitch-dev`
  - `apps/overlays/prod` → namespace `cartsnitch`
- Images currently use `:latest` with `imagePullPolicy: Always`; pin to a CalVer tag in the infra overlay when stabilizing a release

> **Policy — Flux Image Tag Automation is DENIED.**
> Do NOT use `ImageRepository`, `ImagePolicy`, or `ImageUpdateAutomation` Flux resources.
> Image tag updates must be made intentionally: open a PR against `cartsnitch/infra` and update the relevant overlay at the time new changes are pushed. Automated tag mutation by Flux is not permitted under any circumstances.

**To deploy a change:**
1. Merge your code change to `main` in the app repo — CI builds and pushes a new image automatically
2. To update the image tag or apply a manifest change: open a PR against `cartsnitch/infra`, update the relevant overlay, and merge after passing infra CI (kustomize validation)
3. Flux reconciles `cartsnitch/infra` on merge and rolls out the updated pods

**To force a rollout without a manifest change** (e.g., pick up a new `:latest` image on stuck nodes):
- `kubectl rollout restart deployment/<name> -n <namespace>`

### Dependency & Image Updates

* **Dependency management: Mend Renovate.** All automated dependency and container image updates are handled by Mend Renovate. Renovate opens PRs automatically — review, approve, and merge them through the standard PR process.
* **Dependabot is not used and will not be used.** Do not configure Dependabot on any repository. Do not enable it via GitHub settings or `.github/dependabot.yml`. If you encounter Dependabot configuration, remove it.

### Standards

* Kubernetes
  * Cluster Access: Cluster wide read access is granted as is read/write access to -dev namespaces.
  * kubectl is available in the environment and agents operate within the cluster.
* Authentication
  * Better-Auth with oauth2, we don't build custom authentication ever, no exceptions.
  * istio-external in namespace gateway-system - for externally accessible sites.
  * istio-internal in namespace gateway-system - for internal accessibility only.
  * Authentik is our provider in namespace auth - oidc and oauth2 provider.
    * URL: `https://auth.farh.net`
    * Credentials: `authentik-credentials` secret in the relevant namespace (cartsnitch / cartsnitch-dev) contains API credentials for Authentik admin operations.
  * Authentik, Auth0, Okta, and Entra-ID should all be supported.
* Infrastructure as Code (Terraform)
  * Terraform can be deployed for infrastructure tasks via the **Flux OpenTofu Controller** in a GitOps fashion.
  * Submit Terraform configurations via a PR to `cartsnitch/infra` — the tofu controller reconciles them on merge.
  * Use when Authentik configuration, DNS, or other infrastructure provisioning tasks require it.
* Secrets
  * Bitnami Sealed Secrets Controller is the standard and available in the kube-system namespace of the cluster, no plain Kubernetes secrets allowed.
  * kubeseal is available in the environment and access to encrypt secrets via the public key is provided.
* Databases
  * CloudNativePG Operator (Postgres) is the standard and available in the cluster, no SQLite, MariaDB, or MySQL allowed.
  * Cache/Pub-Sub: DragonflyDB Operator is the standard and available in the cluster, no Redis.
