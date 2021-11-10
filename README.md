# Ansible DRG Peering

## Introduction
In this automation, you are able to peer two drgs inside of the same or from different regions. 
This also gets the route tables for vcns and populates them with route rules that send the traffic through the drgs.

## Prerequisites
The following resources should already exist in OCI:

Compartment
2 VCNs in different / same regions
2 DRGs in different /same regions
You will also need to install:

oci_cli
oci ansible modules -> ansible-galaxy collection install oracle.oci
In the root directory of the downloaded code, there are 4 files:

route_rules_inventory.ini -> Inventory file containing all the prerequisites variables
route_rules.yaml -> In charge of creating the drg connections and the necessary route rules
route_rule_delete.yaml -> In charge of delete the drg connection and the necessary route rules
populate_route_rules_ini.yaml -> OPTIONAL, you may use this only if you created your resources using OCI Resource Manager to populate the route_rules_inventory file

## Populating the Inventory file

In order to prepare the inventory file (route_rules_inventory.ini) we have to first understand what arguments we need and what are they doing:

first_drg - The id of the drg from the 1st region
compartment_first - The id of the compartment from the 1st region
first_vcn_id - The id of the vcn from the 1st regions
second_drg - The id of the drg from the 2nd region
compartment_second - The id of the compartment from the 2nd region
second_vcn_id - The id of the vcn from the 2nd region
second_region - The region name of the 2nd region (the first region should be available in oct-cli config)
Now that we know what types of values we have to provide in the inventory file, let's populate it according to their description.

In the end, the file should be similar to this:
```
[all:vars]
first_drg=ocid1...
compartment_first=ocid1...
first_vcn_id=ocid1...

second_drg=ocid1...
compartment_second=ocid1...
second_vcn_id=ocid1...
second_region=us-phoenix-1
```

## Running the code

In order to create the connection between the drgs and the necessary route rules, you will have to go to the root directory and run the following:

`ansible-playbook -i route_rules_inventory.ini route_rules.yaml`

In less than 10 minutes, the connection will be successfully established and the route rules will be created.

If you want to delete the connection after it is created and to remove the route rules, you will simply have to run:

`ansible-playbook -i route_rules_inventory.ini route_rules_delete.yaml`

Again, in less than 10 minutes, the connection and route rules will be removed.