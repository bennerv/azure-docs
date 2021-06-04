---
title: Azure Red Hat OpenShift 4 cluster support policy
description: Understand support policy requirements for Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
---

# Azure Red Hat OpenShift support policy

Azure Red Hat OpenShift is not responsible for any unsupported actions taken by You or any of Your Authorized Users relating to Azure Red Hat OpenShift, including but not limited to actions that may affect Infrastructure Services, Service Availability and data loss.

You, and not Azure Red Hat OpenShift, are responsible for ensuring any custom software or third party software, including operators, continue to function after any cluster maintenance, including upgrades, performed by Azure Red Hat OpenShift or software upgrades self-managed by You. You are also responsible for any terms, conditions and/or fees associated with any third party components deployed by the software, not otherwise included in the Azure Red Hat OpenShift service.

Azure Red Hat OpenShift does not provide Support Services for (a) advice on architecture, design, development or prototyping; (b) non-Red Hat or Azure software, including but not limited to devices, operators, applications, infrastructure or tools that you or a third party provide or create; (c) access management or any security related processes that apply only to your cluster; (d) any work performed under a separate professional services engagement; (e) for any changes you make to the Infrastructure Services account which negatively impacts the operation of the Online Services such as the removal of a load balancer, virtual machine instance, or any Infrastructure Services that Azure Red Hat OpenShift are dependent on. You are responsible for testing any Content deployed on Azure Red Hat OpenShift. You are responsible for backing up Your Content on a regular basis and have those backups available if needed for support or other purposes.

## Cluster configuration requirements

Certain configurations for Azure Red Hat OpenShift 4 clusters can affect your cluster's supportability. Azure Red Hat OpenShift 4 allows cluster administrators to make changes to internal cluster components, but not all changes are supported. The support policy below shares what modifications violate the policy and void support from Microsoft and Red Hat.

> [!NOTE]
> Features marked Technology Preview in OpenShift Container Platform are not supported in Azure Red Hat OpenShift.

### Nodes and Machines

* The cluster must have a minimum of three worker nodes and three master nodes.
* Don't scale down the number of workers below the minimum amount of three.
* Don't taint nodes that prevent OpenShift components to be scheduled, or prevent an upgrade from progressing.
* All virtual machines or nodes must remain in a powered on state.
* All nodes must be associated with a machine config pool.  You can view the machine count for a machine config pool by running `oc get machineconfigpool`
* Don't remove or modify the worker or master machine config pools.
* Don't pause any machineconfigpool.

### Cluster Operators

* All OpenShift Cluster operators must remain in a managed state. The list of cluster operators can be returned by running `oc get clusteroperators`.
* Following configuration of cluster operators such as authentication or dns, the associated cluster operator should remain in a available state without any errors.  After applying configuration ensure that `oc get clusteroperators` shows all cluster operators as healthy.

### OpenShift Resources

* You may not remove or replace any native Azure Red Hat OpenShift components or any other component installed and managed by Azure Red Hat OpenShift
* Don't remove or modify the cluster Prometheus and Alertmanager services.
* Don't remove Service Alertmanager rules.
* Don't remove or modify any resources in any namespace beginning with `openshift-azure`
* Don't run workloads in the kube-system namespace or any namespace beginning with "openshift-".
* Don't remove or modify the 'arosvc.azurecr.io' cluster pull secret.  This secret can be seen with `oc -n openshift-config get secret pull-secret`.
* Customer workloads must not be scheduled on the master nodes to prevent any resource contention with the OpenShift master components.
* Don't set any unsupportedConfigOverrides options. Setting these options prevents minor version upgrades.
* A pod disruption budget should not set a `maxUnavailable` of 0% or 0, or a `minAvailable` of 100% or equal to the number of replicas.  This can block nodes from being drained and upgraded.
* Don't create or enable any Feature Gates
* Don't create or enable the cluster-wide proxy
* Don't modify the audit log policy
* Don't modify or remove the OpenShift SDN network or attempt to use another SDN provider.
* Don't modify the cluster network OpenShift SDN configuration.
* Azure Red Hat OpenShift does not support adding an additional network such as a bridge, host-device, ipvlan, or macvlan network.
* The cluster version resource should only use stable release channels for upgrades.  There is no support for non-stable channels.  The channel can be seen by running `oc get clusterversion version -o jsonpath="{.spec.channel}"`.  In new cluster installations, the channel may not be set yet.
* Any workloads that are run on the cluster including operators from OperatorHub must not affect running OpenShift components and cluster operators from being available.

### Azure Resources

* Don't remove the network security groups from their assigned subnets.
* Upon installation, all cluster resources are created in a resource group called the cluster resource group.  Don't modify or remove any resources within the cluster resource group.
* All cluster virtual machines must have direct outbound internet access, at least to the Azure Resource Manager (ARM) and service logging (Geneva) endpoints.  No form of HTTPS proxying is supported.
* Don't modify the DNS configuration of the cluster's virtual network. The default Azure DNS resolver must be used.
* The Azure Red Hat OpenShift service accesses your cluster via Private Link Service.  Don't remove or modify service access.
* Non-RHCOS compute nodes aren't supported. For example, you can't use a RHEL compute node.
* Don't place policies within your subscription or management group that prevent SREs from performing normal maintenance against the ARO cluster, such as requiring tags on the ARO RP-managed cluster resource group.

## Supported virtual machine sizes

Azure Red Hat OpenShift 4 supports worker node instances on the following virtual machine sizes:

### General purpose

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### Memory optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### Compute optimized

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### Master nodes

|Series|Size|vCPU|Memory: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
