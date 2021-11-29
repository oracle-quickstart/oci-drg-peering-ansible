# Ansible DRG Peering

## Introduction
This automation example enables you to peer two DRGs inside of the same or from different regions. 

This also gets the route tables for VCNs and populates them with route rules that send the traffic through the DRGs.

## Prerequisites

### OCI

To use these examples the following resources should already exist in OCI:

- 1 or more compartments for the following resources
- 2 VCNs in different / same regions
- 2 DRGs in different / same regions

### Personal Computer

On a Linux or Mac host you will also need to install and/or configure the following:

- Docker
- Python 3.8+ (may already be installed on your machine)
- An OCI CLI config file configured in your home directory at `$HOME/.oci` that contains the credentials and tenancy/compartment information to operate against your OCI resources

Once Python 3 is installed, then install the following:

- Ansible
- Ansible Runner

Pip is the easiest installation method. 

```
pip3 install ansible ansible-runner
```

Alternatively, you can also use Ansible Automation Controller (F.K.A. Ansible Tower) to run your automation(s) through execution environments.

## Project Information

In the `project` directory there are 3 files:

- `peering_create.yaml`
  - In charge of creating the DRG connections and the necessary route rules
- `peering_delete.yaml`
  - In charge of delete the DRG connection and the necessary route rules
- `populate_peering.yaml`
  - OPTIONAL, you may use this only if you created your resources using OCI Resource Manager to populate the route_rules_inventory file

In the env directory there is 1 file:

- `extravars` -> Inventory file containing all the prerequisites variables

## Populating Extra Vars

In order to prepare the variables for your OCI tenancy in the `env/extravars` file, we have to first understand what arguments we need and what are they doing:

- `compartment_first` - The id of the compartment from the 1st region
- `first_drg` - The id of the DRG from the 1st region
- `first_vcn_id` - The id of the VCN from the 1st regions
- `compartment_second` - The id of the compartment from the 2nd region
- `second_drg` - The id of the DRG from the 2nd region
- `second_vcn_id` - The id of the VCN from the 2nd region
- `second_region` - The region name of the 2nd region

Now that we know what types of values we have to provide in the file, let's populate it according to their description.

In the end, the file should be similar to this:
```
---
first_drg: ocid1.drg.oc1.iad.aaaaaaa...
compartment_first: ocid1.compartment.oc1..aaaaaaaag...
first_vcn_id: ocid1.vcn.oc1.iad.amaaaaaa...

second_drg: ocid1.drg.oc1.iad.aaaaaaa...
compartment_second: ocid1.compartment.oc1..aaaaaaaag...
second_vcn_id: ocid1.vcn.oc1.iad.amaaaaaa...
second_region: us-ashburn-1
```

## Running the Automation

In order to run the three automation files you may use the `ansible-runner` CLI or configure an Ansible Automation Controller (F.K.A. Ansible Tower) to run the playbooks.  The Ansible playbooks will be executed inside of an Ansible Execution Environment container that has OCI dependencies such as the OCI Python SDK and OCI CLI pre-installed and isolated from conflicts on your host computer. From the root directory of this project, use the following command to run the `peering_create.yaml` automation.

```
ansible-runner run \
--process-isolation \
--process-isolation-executable docker \
--container-image quay.io/scottharwell/oci-execution-env:latest \
--playbook peering_create.yaml \
--container-volume-mount=$HOME/.oci:/home/runner/.oci \
./
```

The connection will be successfully established and the route rules will be created when the playbook completes running.  This normally takes about 10 minutes.

If you want to delete the connection after it is created and to remove the route rules, then you will have to run the `peering_delete.yaml` playbook:

```
ansible-runner run \
--process-isolation \
--process-isolation-executable docker \
--container-image quay.io/scottharwell/oci-execution-env:latest \
--playbook peering_delete.yaml \
--container-volume-mount=$HOME/.oci:/home/runner/.oci \
./
```

Again, in approximately 10 minutes, the connection and route rules will be removed.