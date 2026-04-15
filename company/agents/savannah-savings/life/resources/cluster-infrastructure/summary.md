# Cluster Infrastructure

Key facts about the Kubernetes cluster and GitOps setup for CartSnitch.

- **Kustomize namespace override**: All overlays in `cartsnitch/infra` use `namespace:` transformers. Resources in `apps/base/` that target other namespaces (e.g. `rook-ceph`) will be overridden. Cross-namespace resources need separate deployment paths.
- Agents have cluster-wide **read** access; **read/write** only to `-dev` and `-uat` namespaces.
- Rook-Ceph RGW provides S3-compatible object storage via `objectstore-ceph-internal`.
- Flux reconciles `cartsnitch/infra` main branch to all three environments.
- **Azure Blob Storage** is the environment standard for CNPG backups (10+ clusters). Secret `azure-backup` in `flux-system` reflected via Emberstack Reflector. CartSnitch namespaces need reflector annotation update (board approval pending as of 2026-04-15).
