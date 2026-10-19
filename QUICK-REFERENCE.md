# Quick reference

[Contents](README.md) | [Corrections](CORRECTIONS.md) | [Question index](QUESTION-INDEX.md)

Use these as recall cues after reading the chapters. Each cue assumes the scenario's permissions, regional support and service prerequisites are met.

## Identity and governance

| If the requirement says... | Think... |
| --- | --- |
| Organize resources by department | Tags |
| Prevent an unapproved configuration | Policy |
| Grant someone an operation | RBAC role at an appropriate scope |
| Prevent accidental resource deletion | CanNotDelete lock |
| Let someone delegate Reader | Access-management role; Contributor is insufficient |
| Automatically target Finance staff | Dynamic user group plus the required policy |
| Delegate local office user administration | Administrative unit plus a scoped directory role |
| Workload needs credentials without stored passwords | Managed identity plus target authorization |
| New user does not receive paid features | Check assigned licenses |
| New sign-in domain | Add domain, publish verification record, verify |
| Budget reaches a threshold | Alert/action; no inherent VM shutdown |

## Storage

| Requirement | Recall cue |
| --- | --- |
| Synchronous protection against a zone failure | ZRS |
| Regional replication | GRS or GZRS as supported |
| Read the geo-secondary before failover | RA variant for a supported service |
| Prevent content changes for retention | Immutability policy |
| Rarely read object, offline retrieval acceptable | Evaluate archive and its constraints |
| Automatically age objects into cheaper tiers | Lifecycle management |
| Secretless application reads | Identity plus blob data role |
| Time/operation-limited delegated access | SAS; read all restrictions |
| Traditional mapped network share | Azure Files / SMB |
| Synchronize a Windows folder with Azure Files | File Sync |
| Copy directories to blob storage | AzCopy recursive copy |

## Compute and apps

| Requirement | Recall cue |
| --- | --- |
| Planned maintenance isolation | Update domains |
| Shared power/switch failure isolation | Fault domains |
| Datacenter outage isolation | Availability zones |
| Move to another physical VM host | Redeploy |
| Single active app needs more CPU | Resize / scale up |
| More app instances | Scale out |
| Retrieve the original template deployment | RG Deployments history |
| Keep a deployment secret outside the template | Key Vault reference |
| First-boot Linux configuration | cloud-init |
| Run installation scripts on Azure VMs | Custom Script Extension |
| Test a new web release before production | Deployment slot |
| Fast rollback after slot swap | Swap back, after checking configuration |
| Kubernetes YAML | kubectl |
| AKS node scaling vs pod replicas | Cluster autoscaler vs HPA |

## Networking and monitoring

| Requirement | Recall cue |
| --- | --- |
| One remote client | P2S VPN |
| An entire office | S2S VPN |
| Private provider connectivity | ExpressRoute |
| Peer two VNets | Non-overlapping ranges and valid peering state |
| Delegate a DNS child zone | NS record in the parent |
| Make Azure authoritative for a public domain | Registrar name-server delegation |
| Private app-initiated connections from App Service | VNet integration |
| Private endpoint access | Private IP plus the correct DNS path |
| NSG evaluates conflicting rules | Lowest matching priority number first |
| HTTPS web-attack protection | WAF-capable application gateway |
| Forward one endpoint to one backend | Inbound NAT rule |
| Repeated requests need client affinity | Session persistence |
| Identify a blocking NSG rule | IP flow verify |
| Packet contents | Packet capture |
| RTT/reachability over time | Connection Monitor |
| Guest event alert | Agent/DCR -> log table -> query alert |
| Few lost files | File Recovery with a compatible recovery host |
| Regional failover of a workload | Site Recovery |

## Ports and paths

| Item | Value |
| --- | --- |
| SSH | TCP 22 |
| DNS | UDP/TCP 53 |
| HTTP | TCP 80 |
| HTTPS | TCP 443 |
| SMB | TCP 445 |
| SQL Server default instance | TCP 1433; actual configuration can differ |
| RDP | TCP 3389; UDP can also be used |
| Azure Files UNC | `\\ACCOUNT.file.core.windows.net\SHARE` |

## Five things to inspect in every exhibit

1. **Actual resource region**, not only RG location.
2. **Role scope**, not just role name.
3. **All applicable rules**, not only one NSG.
4. **Exact dates/window**, not just the latest sample.
5. **What is already configured**, not an imagined prerequisite.
