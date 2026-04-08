# Private Cloud Connectivity

This project automates the deployment of a Mini-Landing Zone to establish a secure, private connection to a cloud provider, governed by the principle of "Compliance by Design." At its core, the rescile Universal Configuration Server (UCS) maintains an On-Premise Source of Truth while treating the cloud purely as a resource provider. By ingesting local organizational data and transforming it into cloud-native API calls, UCS ensures that technical functionality is balanced with strict governance. This architecture moves away from reactive yearly security audits toward proactive, automated guardrails that technically prohibit platform engineers from bypassing private connections, ensuring a permanent and compliant network perimeter.

### Deployment Logic
This project follows a "push-Only" methodology:
1. Extract: A local job exports dynamic asset descriptions like identities, applications and databases from a local inventory.
2. Transform: rescile UCS processes the data, stripping away confidential information and generating cloud resource definitions.
3. Load: The engine creates the configuration scripts, ensuring that cloud resources perfectly mirror the intended local state.

### Security Benefits
* **Zero Exposure:** No critical data like user names or emails ever exist in the OCI Global Directory.
* **Resilience:** If the cloud is compromised, base configuration data can be regenerated with a push of a button.
* **Auditability:** Every identity change is tracked in your local version control system (Git) before it is ever pushed to the cloud.
* **Sovereignty:** You maintain absolute "Digital Sovereignty" by keeping the Source of Truth on-premise; the cloud provider acts as a blind execution engine rather than a custodian of your organizational structure.

### Prerequisites
* A cloud account with a designated home region.
* (rescile Universal Configuration Server installed and reachable by your CI/CD runners.
* An API Signing Key with permissions to manage IAM at the Tenancy level at a cloud provider.

## Assets

Unlike traditional Infrastructure-as-Code approaches, this projects leverages the Rescile Universal Configuration Server (UCS) to decouple resource definitions from the execution code. By mapping internal data to anonymized, synthetic pointers for a cloud deployment, the engine ensures that the cloud provider maintains zero-knowledge of the sensitive data while still providing seamless access to authorized resources.

| Asset | Table | Description |
| :--- | :----: | :--- |
| Application | applications.csv | A registry of cloud-native and legacy workloads authorized to interact with the UCS. This table maps specific application IDs to their required resource scopes, ensuring that service-to-service communication remains compartmentalized and secure. |
| Database | database.csv | A catalog of OCI-hosted data stores and their associated synthetic access keys. It defines the connection strings and schema permissions required for applications to store or retrieve data without exposing the underlying physical infrastructure details. |
| Identity | identity.csv | Synthetic identities that maintain an on-prem source of truth. By decoupling the cloud login from personal data, cloud identities function as anonymized resource pointers that provide access while keeping the actual user's identity hidden from the cloud provider. |

 ## Required Resources

| Category | Component | Purpose for Switzerland/FINMA |
| :--- | :--- | :--- |
| **Structure** | Control Tower | Standardized, multi-account governance. |
| **Location** | Region `eu-central-2` | **Local Data Residency** (Zurich). |
| **Transport** | Transit Gateway | Centralized, auditable traffic control. |
| **Security** | SCPs (Guardrails) | **Prevention** of data leaving Switzerland. |
| **Visibility** | Centralized Logging | **Immutable logs** for the "Log Archive" account. |

## Governance Layer

The foundation of the landing zone enables core functionality like billing, logging, security and logging. The respective resources are defined by three core configuration entities. In the rescile ecosystem, these resources are abstracted into .toml files, allowing you to define the "where," the "how much," and the "who" before a single line of infrastructure is even provisioned. By defining these as code, we ensure that every environment—from sandbox to production—adheres to the same structural standards and security guardrails.

| Resource | Model | Description |
| :--- | :----: | :--- |
| Provider | provider.toml | Defines the specific cloud entity (OCI) owning the physical infrastructure and data centers where your workloads will reside. |
| Subscription | subscription.toml | The legal and financial "container" (Tenancy/OCID) that bridges your identity to the provider's billing system and sets usage boundaries. |
| Login | login.toml | The administrative "Keycard" (API Keys/Principal) that permits the rescile engine to authenticate and manage services within the provider. |

### Why this Model matters for Sovereignty
By separating the Login (how we get in) from the Subscription (what we own), the rescile configuration server ensures that even if a specific login is rotated or revoked, the underlying subscription and its sovereign data remains intact. This structure allows for a "clean" handover and complete lifecycle management of the landing zone.

| Login | Description | AWS | OCI |
| :--- | :--- | :----: | :--- |
| **Root Administration** | The root administrator creates a supueruser for a cloud account with full access to all settings. | Control Tower | Root Compartment |
| **Account Management** | The account manager is a commercial contact at a cloud provider, responsible for billing and resource management. | Control Tower | Root Compartment |
| **Audit Report** | ... | CloudTrail, VPC Flow Logs | ... |
| **Log Archive** |  ... | ... | ... |
| **Hub Network** |  ... | ... | ... |


## Connectivity Layer
* **Direct Connect Gateway (DXGW):** Acts as a global anchor for your Megaport VXC.
* **Transit Gateway (TGW):** The central router in your Network Account. It allows the Megaport line to be shared securely across multiple AWS accounts.
* **Interface VPC Endpoint:** This is the **Salesforce Private Connect** resource. It places a private IP (e.g., `10.0.1.50`) directly into your VPC in Zurich.


## Compliance Guardrails
In **AWS Control Tower**, you must activate specific **Controls** (Guardrails) to ensure your data stays in Zurich and the environment remains secure.

### Data Residency Controls (The "Swiss Lock")
1.  **Region Deny (Preventive):** Activate this in Landing Zone settings to block all AWS regions **except `eu-central-2` (Zurich)** and `eu-central-1` (Frankfurt, as a common backup/management region). This ensures no one accidentally spins up a database in the US or Asia.
2.  **Disallow Internet Gateways (Preventive):** This prevents anyone from attaching an Internet Gateway (IGW) to your Salesforce VPC. This forces all traffic to stay on your private Megaport line.
3.  **Disallow Virtual Private Gateways (Preventive):** Ensures that "Shadow IT" doesn't set up unapproved VPNs outside of your controlled Megaport/Transit Gateway architecture.

### Security & Audit Controls 
4.  **Disallow Changes to CloudTrail (Mandatory):** Ensures that no user (even an admin) can stop the recording of API actions.
5.  **Detect Public Read/Write on S3 (Detective):** If anyone accidentally makes a storage bucket public, you get an immediate alert in the Security Account.
6.  **Enable Integrity Validation for CloudTrail (Mandatory):** Proves to auditors that the logs have not been tampered with since they were recorded.
