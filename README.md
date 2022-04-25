# Objective: This document describes the process of automating ACI basic tenant configuration using Intersight Cloud Orchestrator (ICO).

# Table of Contents
1. Overview
2. Prerequisites
3. Index
4. Workflow - ACI Basic Tenant Config
5. Workflow - ACI Update Filter
6. Rollbacks
7. Documentation reference

# Overview

The ICO `Workflow`  leverages the Invoke Web API Request functionality of the `Tasks` construct to create objects in ACI APIC controller.

![10_37_31](https://user-images.githubusercontent.com/104349654/165052705-a05eb5a3-4429-4243-9beb-8bb2763aa133.jpg)
![10_39_14](https://user-images.githubusercontent.com/104349654/165052699-3ef7df7f-86cd-4484-bea0-eb6a4157cbf3.jpg)
![10_43_55](https://user-images.githubusercontent.com/104349654/165053540-13ffeab8-fea7-4cac-9a0b-ea999d8fffab.jpg)



## Prerequisites:
To execute the workflow, the APIC controller must be connected to Intersight by adding it as a `Target`. [Link to adding targets](https://intersight.com/help/saas/getting_started/claim_targets#target_claim_for_compute/fabric_hyperconverged_orchestrator_and_platform_services_targets).

## Index 
- ``Task input variables``: An input parameter (optional or mandatory) that defines a variable in the API request. Task inputs are referenced as ``{{.global.task.input.InputName}}``.
- ``Workflow input variables``: An variable (optional or mandatory) that can be mapped to task inputs. 
- `Body`: Part of mandatory task input variables that contains the JSON body request sent to the APIC.  

##  Workflow - ACI Basic Tenant Config:
The workflow contains all the tasks required to achieve the objective as shown above under objective. The workflow accomplishes the tasks as follow:
- Create a `Tenant` in ACI.


- Create a Virtual Routing and Forwarding (`VRF`) construct.


- Create a `Bridge Domain` (BD) construct and define a gateway `IP` address. Assigning the BD to the VRF created in the previous step.


- Create the `Application Profile` as a container to the Endpoint Groups (EPGs)


- Create the `EPGs` under the Application Profile created, specifically a private and a public EPG (statically assigned in this example).


- Create a `Standard Contract` construct statically named Public-to-Private.


- Create a `Port Filter` that filters all ports as required by the contract. Note: The filter is  defined as `TCP` as `EtherType` and `IPv4` as `IP Protocol` in this workflow example.


- For all `Filters` created, bind the filters to the `Standard Contract` created.


- Assign `Conract` as a `Consumer` to the `Public-EPG`. 


- Assign `Contract` as a `Provider` to the `Private-EPG`.


**Workflow input variables**:

**Note**: All input variables under this workflow are required.
![10_37_31](https://user-images.githubusercontent.com/104349654/165052705-a05eb5a3-4429-4243-9beb-8bb2763aa133.jpg)

- Name: This variable will be mapped to `Tenant`, `VRF`, `BD`, `Application Profile (AP)`.
- PORT: List of ports that should be allowed as defined by your filter.
- Gateway IP: The IP address of the `Bridge Domain` subnet gateway.
- External Target: The external APIC endpoint as assigned under `Intersight Tartgets`

**Static Values**:
The values are statically assigned under `Tasks`.
- Public-EPG: Name of the first created EPG assigned as a `consumer`.
- Private-EPG: Name of the second created EPG aassigend as a `provider`.
- Public-to-Private: `Standard Contract` name in APIC.



##  Workflow - ACI Update Filter:
The workflow contains all the tasks required to delete and/or add addtional filters on top of the previous workflow described in this document. For instance, adding an additional port to the contract created previously or deleting an existing port in the contract:
- For each port in the list of ports added in the previous workflow, delete filter  
- For each deleted filter unassign from the contract.
- For each port create a new filter.
- For each filter created, assign to contract.  

**Workflow input variables**:

**Note**: All input variables are optional except the `Name of Tenant` and `External Target`.
![12_11_25](https://user-images.githubusercontent.com/104349654/165069298-446d1e5a-d565-496c-9f55-03282dd12b94.jpg)


- External Target: As assigned under `Targets`. Must be the same target as in the previous workflow.
- Name of Tenant: Must be the exact same name of the `Tenant` as in the previous workflow.
- Delete Port: List of ports to be deleted. 
- Add Port: List of ports to be created and assigned to the contract.

## Rollbacks:

ICO supports the functionality of rollbacks by reverting entities created or modified when executing a workflow. You can choose one or more `tasks` to revert back as shown in the figure below:

![12_18_43](https://user-images.githubusercontent.com/104349654/165070438-26b65ce3-f162-47a7-af38-2b1a7a9625ff.jpg)



## Documentation reference:
- [Workflow Designer reference](https://www.intersight.com/help/saas/resources/Workflow_Designer#requests).
- [Tasks reference](https://intersight.com/help/saas/resources/Task_Designer#supported_requests).
