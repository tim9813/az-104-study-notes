# Corrections, legacy material and open cautions

[Contents](README.md) | [Question index](QUESTION-INDEX.md)

**Read this before memorizing the PDF key.** This is a targeted review, not a certification that every remaining answer is correct. Page numbers in the index are physical PDF pages. The source's answer letters are mentioned only where they explain a concrete conflict.

## Clear conflicts or incorrect generalizations

| Question(s) | Problem in the source | What to study instead |
| --- | --- | --- |
| Q5 | Key C says `Create-AzVM`; its explanation describes the CLI | For the stated cloud-init choice, **D: `az vm create`**, with `--custom-data`. [Reference](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-automate-vm-deployment) |
| Q19 | Key D chooses 40 update domains, while the explanation itself says 20 | **B: 20** is the supported maximum in these choices. [Reference](https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview) |
| Q70 | Key B relies on subscription Owner for a root-scope task | The offered Global Administrator elevation route is **C**. After elevation, grant the appropriate resource role at the required scope. [Reference](https://learn.microsoft.com/en-us/azure/role-based-access-control/elevate-access-global-admin) |
| Q76 | Key B cannot meet the explicit requirement to manage VNets | **C: Contributor** is sufficient among the offered roles. VM Contributor is not general VNet management. [Reference](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/compute#virtual-machine-contributor) |
| Q105 | App1's answer uses an account key despite managed identity and a minimize-secrets requirement | Use identity-based blob data authorization for App1. Assess App2's time-bounded SAS separately; a normal RBAC assignment has no automatic 30-day expiry. [Reference](https://learn.microsoft.com/en-us/azure/storage/blobs/authorize-access-azure-active-directory) |
| Q185 | The marked answer shuts off VM1 even though the action group described is email-only | Both VMs continue to run unless an explicit shutdown action exists. Budget alerts are not spending caps. [Reference](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets) |
| Q193 | The explanation treats pod HPA as node autoscaling | Cluster autoscaler changes nodes; `kubectl autoscale` changes workload replicas. Do not accept A as evidence of cluster-autoscaler setup. [Reference](https://learn.microsoft.com/en-us/azure/aks/cluster-autoscaler) |
| Q302-306 | The source rejects packet capture for inspecting all traffic and favors Connection Monitor | For actual packet inspection, choose packet capture. For continuing reachability/latency tests, choose Connection Monitor. The wording and marked key are inconsistent. [Reference](https://learn.microsoft.com/en-us/azure/network-watcher/packet-capture-overview) |
| Q329 | It sends a Linux recovery mount directly to Windows File Explorer | Use a compatible Linux recovery environment for the Linux file-recovery script/filesystem, then copy to Windows if needed. [Reference](https://learn.microsoft.com/en-us/azure/backup/backup-azure-restore-files-from-vm) |

## Legacy instructions that should not become current runbooks

| Question(s) | Legacy or incomplete idea | Current learning rule / review action |
| --- | --- | --- |
| Q6-8 | Per-authentication versus per-enabled-user MFA provider billing | Historical scenario. Study modern MFA/Conditional Access rather than reproducing that provider workflow. |
| Q31 | `Set-AzureStaticVNetIP` classic command | Use the current Azure NIC IP-allocation configuration. |
| Q38-40, Q327 | Explanation says only Global Administrators add users | Evaluate the account's actual role in the target tenant; other appropriate directory roles can manage users. |
| Q61 | Explanation states a maximum of 15 tags | Current general ARM limit is 50 tag pairs, with resource-specific limitations. [Reference](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources) |
| Q73-75, Q89 | Explanation says Reader can enable Traffic Analytics | Read permissions are not configuration write permissions. Check current permissions for each resource and action. |
| Q95 | Classic Service Administrator and outage mail | Separate present resource administration from configured Service Health alert recipients. |
| Q123 | Explanation treats 5 TB as a universal file-share maximum | Do not reuse this as a current Azure Files capacity limit. Limits depend on current share/account configuration. |
| Q126 | A fixed historical AzCopy authorization matrix | Check the current AzCopy release and destination service. [Reference](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) |
| Q146-147, Q156, Q167, Q199, Q216 | MMA/Log Analytics agent or older diagnostics-extension setup | Use AMA and DCRs for new guest collection. The Log Analytics agent retired in 2024. [Reference](https://learn.microsoft.com/en-us/azure/azure-monitor/agents/azure-monitor-agent-migration-tools) |
| Q182, Q235 | New NSG flow-log setup | Use current VNet flow-log guidance; the NSG flow-log service has a retirement plan. [Reference](https://learn.microsoft.com/en-us/azure/network-watcher/nsg-flow-logs-overview) |
| Q208 | Explanation says only Generation 1 VMs are supported | Its VHDX-versus-VHD format issue is useful; its Generation 1-only statement is obsolete. |
| Q241 | Remove/recreate peering to add an address range | Current guidance supports editing address space and syncing peers. [Reference](https://learn.microsoft.com/en-us/azure/virtual-network/update-virtual-network-peering-address-space) |
| Q254, Q272, Q294 and related Basic-SKU cases | Basic Load Balancer restrictions | Preserve them as historical quiz context. Basic Load Balancer retired on 30 September 2025. [Reference](https://learn.microsoft.com/en-us/azure/load-balancer/skus) |
| Q299 | Only one registration VNet per private zone | Check the current auto-registration/link model and limits; a zone can serve multiple linked VNets. [Reference](https://learn.microsoft.com/en-us/azure/dns/private-dns-autoregistration) |
| Q301 | `AzureBastionSubnet` /27 in a preview-era template | A new dedicated deployment using that subnet requires /26 or larger; check the chosen SKU. [Reference](https://learn.microsoft.com/en-us/azure/bastion/configuration-settings) |
| Q328 | Network Performance Monitor | Study current Connection Monitor for ongoing hybrid connection monitoring. [Reference](https://learn.microsoft.com/en-us/azure/network-watcher/connection-monitor-overview) |

## Do not accept these explanations without the scenario details

- **Q16 / Q180:** deallocating all VMs can solve a cluster-allocation constraint. It is not mandatory for every resize. A restart/downtime can still result from resizing.
- **Q17:** stopping a source VM is not a universal prerequisite to detach a supported data disk. Quiesce writes and follow supported detach steps. [Reference](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/detach-disk).
- **Q21:** the selected batch-file answer and its Custom Script Extension explanation discuss different mechanisms. Treat it as ambiguous rather than translating the explanation into a nonexistent option.
- **Q24-26 / Q238:** the P2S/profile-refresh lesson assumes a supported gateway and routes. The stated static/policy-based gateway is inconsistent with the P2S setup.
- **Q34-35:** file recovery is not limited to the infected VM, and full VM recovery is not an arbitrary overwrite of any chosen VM. Select a supported restore operation and compatible target.
- **Q42:** creating an OAuth endpoint is not the whole AKS access solution. Evaluate supported managed Entra integration and cluster authorization; the source choices are historical/unclear.
- **Q56:** its selected access-management role is relevant, but the explanation's description of full resource-management access overstates that role.
- **Q79 / Q121 / Q135:** distinguish stopping protection, retaining data, deleting data and changing vaults. An unprotected/empty vault state depends on all dependencies, including soft-delete and registration rules. [Reference](https://learn.microsoft.com/en-us/azure/backup/backup-azure-delete-vault).
- **Q113 / Q115:** File Sync direction, cloud change detection, endpoint-server constraints and conflict handling matter. Do not memorize an endpoint count without inspecting the diagram.
- **Q130:** it asks for preparation **in the subscription**, but its second answer is installing an agent on a server. Learn the Azure-side service/sync-group objects separately from server preparation. [Reference](https://learn.microsoft.com/en-us/azure/storage/file-sync/file-sync-deployment-guide).
- **Q132:** the explanation mixes location rules for diagnostic storage accounts and Log Analytics workspaces. Recheck the actual destination restrictions instead of reversing the two by memory.
- **Q134:** SAS REST access and SMB drive mapping are different. Do not treat a SAS token as a normal SMB password.
- **Q194:** `docker push` fits an already built local image; `az acr build` builds from source and pushes. The question and explanation mix starting points.
- **Q195:** determine the actual VMSS region from the resource, not the region label of its resource group.
- **Q202:** configurable deployment values depend on the parameters and expressions actually defined in the template.
- **Q242 / Q278:** move support is resource-specific and dependency-sensitive. The explanation's blanket claim that virtual networks cannot move is unreliable. [Reference](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/move-support-resources).
- **Q252:** its private-zone migration-script explanation is not a general public-zone migration procedure.
- **Q273 / Q279-281 / Q291:** check SKU compatibility and backend configuration; do not infer every backend VM needs its own public IP or static private IP.
- **Q290 / Q316:** traditional CNI, CNI Overlay, kubenet and policy-provider capabilities differ. Read the actual network mode.
- **Q323 / Q326:** administrator SSPR methods and hybrid writeback restrictions need separate evaluation; security questions are not a universal administrator reset method.
- **Q335 / Q339:** preserve the diagram's schedule and retention rules. Count distinct points, not retention labels.

## What the review does not claim

No replacement answer has been fabricated for an unreadable image. An exhibit-dependent index entry means the topic is mapped, not that its individual Yes/No selections have all been independently validated. Use the original exhibit, the learning method in the chapter and current documentation together.
