# OCI Landing Zone

## Assets

| Asset | Table | Description |
| :--- | :----: | ---: |
| Identity | identity.csv | Synthetic identities that maintain an on-prem source of truth. By decoupling the cloud login from personal data, cloud identities function as anonymized resource pointers that provide access while keeping the actual user's identity hidden from the cloud provider. |

## Base Resources

| Resource | Model | Description |
| :--- | :----: | ---: |
| Provider | provider.toml | A cloud provider owns and operates data centers to "rent out" computing power, storage, and/or software. |
| Subscription | subscription.toml | A subscription is an active contract that grants permission to use the resources at a cloud provider. It acts as the bridge between an identity and a provider's billing system. |
| Login | login.toml | A login is a credential that enables identities to rent resources, manage servicdes, or change settings at a cloud provider. |
