# OCI Landing Zone

This project automates the deployment of a hardened Oracle Cloud Infrastructure (OCI) Landing Zone. The primary goal is to maintain an On-Premise Source of Truth while treating the cloud purely as a resource provider. The rescile Universal Configuration Server (UCS) acts as the brain of the operation. It ingests local organizational data and transforms it into OCI-native API calls or OpenTofu/Terraform configurations.

### Deployment Logic
The project follows a Push-Only methodology:
1. Extract: A local job exports dynamic resource description like identities, applications and databases from a local inventory.
2. Transform: rescile UCS processes the data, stripping away PII (Personally Identifiable Information) and generating OCI-compliant resource definitions.
3. Load: The engine executes the configuration, ensuring that OCI Users, Groups, and IAM Policies perfectly mirror the intended local state.

### Security Benefits
* **Zero Exposure:** No real user names or emails ever exist in the OCI Global Directory.
* **Resilience:** If your on-premise IdP goes offline, existing cloud workloads and pre-authenticated synthetic sessions remain unaffected.
* **Auditability:** Every identity change is tracked in your local version control system (Git) before it is ever pushed to the cloud.

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



| Resource | Model | Description |
| :--- | :----: | :--- |
| Provider | provider.toml | A cloud provider owns and operates data centers to "rent out" computing power, storage, and/or software. |
| Subscription | subscription.toml | A subscription is an active contract that grants permission to use the resources at a cloud provider. It acts as the bridge between an identity and a provider's billing system. |
| Login | login.toml | A login is a credential that enables identities to rent resources, manage servicdes, or change settings at a cloud provider. |
