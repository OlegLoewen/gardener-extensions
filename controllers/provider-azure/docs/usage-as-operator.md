# Using the Azure provider extension with Gardener as operator

The [`core.gardener.cloud/v1alpha1.CloudProfile` resource](https://github.com/gardener/gardener/blob/master/example/30-cloudprofile.yaml) declares a `providerConfig` field that is meant to contain provider-specific configuration.

In this document we are describing how this configuration looks like for Azure and provide an example `CloudProfile` manifest with minimal configuration that you can use to allow creating Azure shoot clusters.

## `CloudProfileConfig`

The cloud profile configuration contains information about the update and failure domain counts in the Azure regions you want to offer.
Additionally, it contains the real machine image identifiers in the Azure environment (publisher, offer, etc.).
You have to map every version that you specify in `.spec.machineImages[].versions` here such that the Azure extension knows the machine image identifiers for every version you want to offer.

An example `CloudProfileConfig` for the Azure extension looks as follows:

```yaml
apiVersion: azure.provider.extensions.gardener.cloud/v1alpha1
kind: CloudProfileConfig
countUpdateDomains:
- region: westeurope
  count: 5
countFaultDomains:
- region: westeurope
  count: 3
machineImages:
- name: coreos
  version: 2135.6.0
  urn: "CoreOS:CoreOS:Stable:2135.6.0"
```

## Example `CloudProfile` manifest

Please find below an example `CloudProfile` manifest:

```yaml
apiVersion: core.gardener.cloud/v1alpha1
kind: CloudProfile
metadata:
  name: azure
spec:
  type: azure
  kubernetes:
    versions:
    - version: 1.16.1
    - version: 1.16.0
      expirationDate: "2020-04-05T01:02:03Z"
  machineImages:
  - name: coreos
    versions:
    - version: 2135.6.0
  machineTypes:
  - name: Standard_D4_v3
    cpu: "4"
    gpu: "0"
    memory: 16Gi
  volumeTypes:
  - name: standard
    class: standard
    usable: true
  - name: premium
    class: premium
    usable: false
  regions:
  - name: westeurope
  providerConfig:
    apiVersion: azure.provider.extensions.gardener.cloud/v1alpha1
    kind: CloudProfileConfig
    countUpdateDomains:
    - region: westeurope
      count: 5
    countFaultDomains:
    - region: westeurope
      count: 3
    machineImages:
    - name: coreos
      version: 2135.6.0
      urn: "CoreOS:CoreOS:Stable:2135.6.0"
```