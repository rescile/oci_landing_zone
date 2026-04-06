# OCI Landing Zone

This project automates the deployment of a hardened Oracle Cloud Infrastructure (OCI) Landing Zone. The primary goal is to maintain an On-Premise Source of Truth while treating the cloud purely as a resource provider. The rescile Universal Configuration Server (UCS) acts as the brain of the operation. It ingests local organizational data and transforms it into OCI-native API calls or OpenTofu/Terraform configurations.

### Deployment Logic
This project follows a "push-Only" methodology:
1. Extract: A local job exports dynamic asset descriptions like identities, applications and databases from a local inventory.
2. Transform: rescile UCS processes the data, stripping away confidential information and generating OCI-compliant resource definitions.
3. Load: The engine creates the configuration scripts, ensuring that OCI resources perfectly mirror the intended local state.

### Security Benefits
* **Zero Exposure:** No critical data like user names or emails ever exist in the OCI Global Directory.
* **Resilience:** If the cloud is compromised, base configuration data can be regenerated with a push of a button.
* **Auditability:** Every identity change is tracked in your local version control system (Git) before it is ever pushed to the cloud.
* **Sovereignty:** You maintain absolute "Digital Sovereignty" by keeping the Source of Truth on-premise; the cloud provider acts as a blind execution engine rather than a custodian of your organizational structure.

### Prerequisites
* Oracle Cloud Account with a designated Home Region.
* rescile Universal Configuration Server installed and reachable by your CI/CD runners.
* OCI API Signing Key with permissions to manage IAM at the Tenancy level.

## Assets

Unlike traditional cloud deployments, this architecture uses rescile Universal Configuration Server (UCS) to map on-premise identities to anonymized, synthetic pointers within OCI.

| Asset | Table | Description |
| :--- | :----: | :--- |
| Identity | identity.csv | Synthetic identities that maintain an on-prem source of truth. By decoupling the cloud login from personal data, cloud identities function as anonymized resource pointers that provide access while keeping the actual user's identity hidden from the cloud provider. |

## Base Resources

The foundation of the OCI Landing Zone is defined by three core configuration entities. In the rescile ecosystem, these resources are abstracted into .toml files, allowing you to define the "where," the "how much," and the "who" before a single line of infrastructure is even provisioned. By defining these as code, we ensure that every environment—from sandbox to production—adheres to the same structural standards and security guardrails.

| Resource | Model | Description |
| :--- | :----: | :--- |
| Provider | provider.toml | Defines the specific cloud entity (OCI) owning the physical infrastructure and data centers where your workloads will reside. |
| Subscription | subscription.toml | The legal and financial "container" (Tenancy/OCID) that bridges your identity to the provider's billing system and sets usage boundaries. |
| Login | login.toml | The administrative "Keycard" (API Keys/Principal) that permits the rescile engine to authenticate and manage services within the provider. |

### Why this Model matters for Sovereignty
By separating the Login (how we get in) from the Subscription (what we own), the rescile configuration server ensures that even if a specific login is rotated or revoked, the underlying subscription and its sovereign data remains intact. This structure allows for a "clean" handover and complete lifecycle management of the landing zone.
