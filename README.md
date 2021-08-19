# Anthos Config Management Directory

Anthos Config Management is a key component of [Anthos](https://cloud.google.com/anthos/).
Enabling rapid and secure application development can be challenging for
administrators working across a hybrid container environment. With Anthos Config
Management, you can create a common configuration for all administrative
policies that apply to your Kubernetes clusters both on-premises and in the
cloud. Anthos Config Management evaluates changes and rolls them out to all
clusters so that your desired state is always reflected.

Anthos Config Management reads its configuration and the Kubernetes objects it
needs to create from a Git repository. Multiple Kubernetes clusters can be
managed from a single repository. While you can create any Kubernetes object
with Anthos Config Management, it is particularly useful for "policy" objects
such as RBAC policies, ResourceQuotas, NetworkPolicies, etc.

This is a template repository for Anthos Config Management to get you started.
We suggest that you read the following documentation to start:

* [Anthos Config Management Quickstart](https://cloud.google.com/anthos-config-management/docs/quickstart)
* [Installing Anthos Config Management](https://cloud.google.com/anthos-config-management/docs/how-to/installing)
* [Using the Anthos Config Management repo](https://cloud.google.com/anthos-config-management/docs/how-to/repo)

Anthos and Anthos Config Management are Google Cloud products.

## Anthos Config Management testing with GitLab CI

You can implement tests on your Anthos Config Management repository with
GitLab CI. This sample repository contains examples for two kinds of tests.

### Running nomos vet

`nomos vet` validates your repository by checking that its syntax is OK
and that the objects you want to create are in APIs that actually exists
in the clusters you are managing.

Follow these steps to setup this test:

1. Create a GitLab CI runner in each of the clusters
you are managing.
  * This runner needs a specific RBAC configuration that's available in the `cluster/gitlab-runner-acm-rbac.yaml` file.
  * Each runner should have a [tag](https://docs.gitlab.com/ee/ci/runners/#using-tags)
    `cluster:[NAME_OF_CLUSTER]`.
2. Edit the `.gitlab-ci.yml` file to:
  * Uncomment the `vet` stage.
  * Uncomment the `.vet` job template.
  * Create a job per cluster with the following definition:
    ```
    Vet [NAME_OF_CLUSTER]:
    <<: *vet_definition
    tags:
    - cluster:[NAME_OF_CLUSTER]
    ```

### Testing changes against Policy Controller constraints
[Policy Controller](https://cloud.google.com/anthos-config-management/docs/concepts/policy-controller)
is a part of Anthos Config Management, and allows to enforce policies
with a dynamic admission webhook in Kubernetes. You can test changes
against those policies before they are applied by Anthos Config Management.

For this, define your policies in this repository, and add to your `.gitlab-ci.yml`
file the `Prepare`, `Read YAML`, and `Validate` jobs that are in this repository's `.gitlab-ci.yml`.