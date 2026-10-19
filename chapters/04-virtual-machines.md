# 4. Virtual machines and deployment

[Contents](../README.md) | [Previous](03-storage.md) | [Next: apps and containers](05-apps-and-containers.md)

**Goal:** predict what changes a VM's availability, placement, data and configuration.

## Availability sets and zones

A fault domain groups infrastructure sharing certain failure risks. An update domain groups VMs that can be restarted together during planned maintenance. Availability sets reduce correlated failures; they do not provide the same datacenter-failure protection as distributing replicas across availability zones.

Availability sets support up to **20 update domains** and up to **3 fault domains**, with the managed fault-domain maximum depending on the region. Settings are chosen when creating the set. [Availability-set reference](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview).

For an evenly distributed question, compute the largest affected update domain. With 14 VMs over 10 update domains, at most 2 VMs occupy a domain. That leaves 12 during one domain's maintenance, before considering unrelated failures. For a whole-datacenter outage, place application replicas in separate zones and use a suitable traffic-distribution design.

**Quiz connections:** Q18-19, Q63, Q128, Q166, Q176, Q201, Q215, Q259.

## VM state, sizing and quota

Distinguish **stopped** from **deallocated**. A guest shutdown can leave compute allocated; deallocation releases compute placement. Disks and other resources can still incur charges.

Resizing changes vCPU/RAM capacity. A resize can restart the VM; changing to a size unavailable on the current hardware cluster can require deallocation. Availability-set allocation failures can require deallocating all VMs in the set so Azure can place them together on another suitable cluster. It is not a rule that every resize always requires stopping every VM.

Check both total regional vCPU quota and the relevant VM-family quota. A deallocated VM can still count toward quota. Also confirm the requested size is available in that region or zone.

For a single-instance app that cannot run several active copies, scale **up** by changing VM size. A scale set with extra instances does not automatically make that application compatible with horizontal scaling.

**Quiz connections:** Q16, Q175, Q180, Q203.

## Disks, images and redeployment

OS disks hold the operating system. Data disks hold persistent application data. A temporary/resource disk is local scratch space and must not be treated as durable storage. The PDF's Windows D: example is temporary storage under its stated default layout; drive letters vary by image and configuration.

Redeploy moves a VM to a new host. It can lose temporary-disk data, whereas moving the resource into another RG does not force a host change. A normal restart is also different from redeploy.

To move a conventional data disk to another VM, quiesce writes, detach it, then attach it to the intended target. Supported data disks can be detached while the VM is running; stopping the source is not universally necessary. [Disk-detach procedure](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/detach-disk).

For uploaded VM disks, distinguish a supported **VHD** format from VHDX. Prepare the operating system and generalization as required by the image workflow. The PDF uses `Add-AzVhd`; do not memorize its old claim that Azure supports only Generation 1 VMs.

**Quiz connections:** Q17, Q22, Q168-170, Q207-208, Q214.

## Region, NIC and VNet constraints

A NIC must match the VM/VNet location requirements. Its resource group does not have to share the region label of the containing RG. All NICs on a VM must follow the platform's same-VNet constraint.

Changing the RG or adding a second NIC does not move a VM between VNets. In the PDF's cross-VNet scenario, the conceptual solution is to retain required disks and recreate the VM with a NIC in the target VNet. Preserve dependencies and configuration deliberately. Changing a subnet inside the same VNet is a different operation.

**Quiz connections:** Q150-152, Q157, Q172-174, Q187, Q274.

## ARM templates and Bicep

An ARM template declares resources, parameters, variables and outputs. Bicep is another way to express Azure resource deployments. To predict a deployment, follow the actual expression assigned to each resource property.

For location questions, a parameter only matters if the resource references it. A hard-coded location in `resources` wins over an unused variable. Inspect dependencies, loops, disk arrays and scope rather than assuming every field is configurable at deployment time.

Use the resource group's **Deployments** history to inspect an earlier template deployment and its operations. Exporting a current resource configuration is different from recovering the original deployment. Neither is a backup of guest files.

```powershell
# Illustrative resource-group deployment syntax.
New-AzResourceGroupDeployment -ResourceGroupName 'StudyRG' -TemplateFile './main.bicep'
```

[Deployment command reference](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-powershell).

**Quiz connections:** Q13-15, Q49, Q140, Q149, Q164, Q186, Q196-198, Q202, Q210, Q220.

## Secrets and initial configuration

For a deployment password, use a suitable Key Vault reference and secure parameters. A secure parameter protects handling, but putting its value in a committed plain-text parameter file still exposes the secret. The deployment identity needs the applicable access.

Use **cloud-init** for supported Linux images at first boot. A CLI deployment passes a configuration file with `az vm create --custom-data ...`; Q5's printed `Create-AzVM` answer is inconsistent with its own explanation. [Cloud-init tutorial](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-automate-vm-deployment).

A Custom Script Extension runs deployment/configuration scripts through the VM agent. DSC describes a desired state; its historical Automation State Configuration workflow imports configuration, compiles it and assigns a node configuration. Distinguish one-time installation from ongoing state enforcement. Current lifecycle/support should be checked before implementing older DSC services.

**Quiz connections:** Q5, Q20-21, Q158, Q161, Q163, Q204, Q219, Q229, Q244.

## Scale sets and placement

Scale sets manage multiple VM instances. Autoscale evaluates a rule's aggregation, time window, threshold, action, cooldown and min/max boundaries. A rule adding 2 to 4 instances results in 6 only if the maximum permits it. A low CPU value at one instant does not necessarily satisfy a full evaluation window.

Uniform and Flexible orchestration modes use different instance models and management capabilities. Understand the mode named in the exhibit; avoid treating old `VM` and `ScaleSetVM` names as the whole modern feature comparison.

Model changes and instance updates are separate. In a manual upgrade policy, updating the model does not necessarily apply changes to existing instances immediately. Proximity placement groups target low latency; they do not replace a high-availability design and must satisfy resource-location constraints.

**Quiz connections:** Q160-161, Q175-176, Q183, Q195, Q209, Q217, Q260.

## Check your understanding

What solves a host problem? **Redeploy.** What solves a single-instance CPU bottleneck? **A suitable resize.** What preserves a password outside a template? **An authorized Key Vault reference.** What protects against a datacenter failure? **Replicas across zones.**
