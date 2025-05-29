# SanctuAIry-baseline-manifests

This repository serves as the Reference Value Provider for the SanctuAIry service’s Intel TDX attestation flow. It houses the versioned, cryptographically anchored baseline manifest that describes the known‑good measurements of the Intel TDX Confidential VM (Trust Domain) used by SanctuAIry.

The manifest file is pinned to a Git tag (e.g., v1.0.0) and contains only the immutable values that a Verifier must compare against live attestation evidence.

## 🔑 What is a Baseline Manifest?

A baseline manifest captures the static, build‑time measurements of the Confidential VM and its underlying TDX platform. When SanctuAIry’s Verifier receives a TDX Quote from a running VM, it fetches the corresponding manifest and performs exact-match or threshold checks on these fields:

| Field           | Description                                                                                          |
| --------------- | ---------------------------------------------------------------------------------------------------- |
| `version`       | Git tag for this manifest release (e.g. `v1.0.0`).                                                   |
| `createdAt`     | ISO-8601 timestamp when this manifest was generated.                                                 |
| `author`        | Email or identity of the CI system that generated the manifest.                                      |
| `sourceImage`   | GCP Compute Image URL used to launch the reference VM.                                               |
| `sourceImageId` | GCP-assigned numeric Image ID for the golden VM disk.                                                |
| `mrTd`          | **SHA-384** measurement of the VM’s initial memory state. Exact match required.                      |
| `mrSeam`        | **SHA-384** measurement of the Intel TDX Module’s code. Exact match required.                        |
| `tdAttributes`  | Flags describing the TD’s launch attributes (e.g. DEBUG bit). Exact match required.                  |
| `xfam`          | Extended-features-allowed mask (CPU features). Exact match required.                                 |
| `teeTcbSvn`     | Minimum Trusted Computing Base Security-Version Number. Verifier checks live value ≥ this threshold. |

## 🚀 How to Use

1. Clone the repo and check out the desired tag:

    ```
    git clone <https://github.com/YourOrg/SanctuAIry-baseline-manifests.git>
    cd SanctuAIry-baseline-manifests
    git checkout v1.0.0
    ```

2. Inspect the manifest in baseline-manifest.jsonc. Confirm its fields match the expected golden measurements.

3. Configure your Verifier to fetch the manifest at runtime. The Verifier parses and uses these values to validate incoming TDX Quotes.

    ```
    <https://raw.githubusercontent.com/YourOrg/SanctuAIry-baseline-manifests/v1.0.0/baseline-manifest.jsonc>
    ```

## 🔄 Versioning and Releases

-   New baseline manifests are released by creating a signed Git tag. The tag name (v1.1.0, v2.0.0, etc.) reflects the VM image version or security update.

-   Signed tags (git tag -s vX.Y.Z) ensure authenticity: the tag itself is GPG‑signed by the CI system’s key.

-   Verifiers pin to a specific tag or commit hash, guaranteeing immutability of the reference values.
