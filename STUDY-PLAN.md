# Study plan and active recall

[Contents](README.md) | [Question index](QUESTION-INDEX.md)

## A practical two-week pass

Adjust the duration to your schedule. The aim is to explain decisions accurately, not memorize a letter sequence.

| Day | Read | Do |
| --- | --- | --- |
| 1 | Identity: users/groups/domains | Explain directory versus resource roles; revisit 10 matching PDF questions |
| 2 | Identity: devices/MFA/SSPR | Sketch the scope, conditions and grant controls for Finance MFA |
| 3 | Governance: RBAC/scopes | Pick roles for read, create, delegate and network-management tasks |
| 4 | Governance: policy/tags/locks/cost | Explain why a tag, policy and lock cannot replace each other |
| 5 | Storage: types/redundancy | Choose a storage design for local, zone and regional failures |
| 6 | Storage: permissions/tiering/sync | Draw the File Sync objects and a credential/network troubleshooting path |
| 7 | VM availability/disks | Trace what survives restart, resize, redeploy and disk restore |
| 8 | Templates/configuration/scale sets | Follow a location expression and manually calculate an autoscale timeline |
| 9 | App Service and containers | Explain slots, plan sharing, scale up/out and image-to-runtime steps |
| 10 | VNet/peering/VPN | Draw a hub/spoke diagram with the required routes and gateway settings |
| 11 | DNS | Explain public delegation and private DNS links without looking |
| 12 | NSGs/load balancing | Work 10 traffic paths by writing source, destination, protocol and matching rules |
| 13 | Monitoring/recovery | Choose a tool for five symptoms; count recovery points on a calendar |
| 14 | Mixed review and corrections | Reattempt missed concepts and fill the current-syllabus gaps listed in README |

## Recall questions

Try answering first, then open the answer. These are newly written prompts based on the PDF's concepts.

### 1. An operator can edit VMs but cannot assign Reader to another person. What is missing?

<details><summary>Show answer</summary>

Role-assignment authority at the intended scope. A resource-management role such as Contributor does not ordinarily include that authority. Use an appropriate scoped access-management role rather than granting broad subscription ownership by default.
</details>

### 2. The RG has a Department tag but its VM does not. Is Azure malfunctioning?

<details><summary>Show answer</summary>

No. Resource tags do not automatically inherit from the RG. Apply tags directly or use an appropriate policy/remediation approach.
</details>

### 3. Storage must stay writable during a zone outage. Is LRS enough?

<details><summary>Show answer</summary>

No. Evaluate a zone-resilient option such as ZRS in a supported configuration. Regional replication is a different requirement from synchronous protection within the region.
</details>

### 4. An app has a managed identity but blob reads fail. What two layers should you check?

<details><summary>Show answer</summary>

Authorization to the data, including the required role at the correct scope, and network reachability/firewall/private-DNS configuration. Identity existence alone satisfies neither.
</details>

### 5. A VM's CPU is too small, but the app cannot run two active copies. Scale in which direction?

<details><summary>Show answer</summary>

Scale up by selecting a suitable VM size. Plan for the resize's availability impact and check quota/capacity.
</details>

### 6. Can moving a VM into an East US resource group physically relocate it there?

<details><summary>Show answer</summary>

No. Resource grouping and resource location are different. A regional migration needs a supported migration/redeployment process.
</details>

### 7. A user cannot create a staging slot. Should you add more instances?

<details><summary>Show answer</summary>

First check the plan tier. More instances of an unsupported tier do not unlock slots; a supported tier is needed.
</details>

### 8. VNets A-B and B-C are peered. Does A-C connectivity automatically work?

<details><summary>Show answer</summary>

No. Peering is not automatically transitive. Design direct or supported transit connectivity with the required routes.
</details>

### 9. A certificate-based VPN client has the profile but no private key. Is it ready?

<details><summary>Show answer</summary>

No. It needs a valid client certificate with the private key and the appropriate trust/profile configuration.
</details>

### 10. NIC NSG allows HTTPS, subnet NSG denies it. Does the NIC allow win?

<details><summary>Show answer</summary>

No. Both applicable NSGs must allow the flow. Priorities are evaluated within each NSG, not as a global merged override.
</details>

### 11. Which tool tells you which NSG rule blocks a specified packet?

<details><summary>Show answer</summary>

IP flow verify. Packet capture, Connection Monitor and IP flow verify provide different evidence.
</details>

### 12. A budget alert emails you at 100%. Will it stop a VM automatically?

<details><summary>Show answer</summary>

No. An email-only action does not stop resources. A deliberately configured automation action is needed for that behavior.
</details>

### 13. A weekly backup also meets the monthly rule. Should you count it twice?

<details><summary>Show answer</summary>

No. Count distinct surviving recovery points. The same point may carry more than one retention label.
</details>

### 14. Does a backup restore of VM disks reverse a later VM resize?

<details><summary>Show answer</summary>

Do not assume so. VM resource configuration and restored disk contents are different layers. Check the selected restore operation.
</details>

### 15. Should packet payload inspection and recurring latency testing use the same tool?

<details><summary>Show answer</summary>

Not necessarily. Packet capture records traffic; Connection Monitor tests ongoing connectivity and latency.
</details>

## Optional practical exercises

Use a disposable lab with costs you understand. Reading and planning these exercises does not require deploying anything.

- Inspect effective access at a resource group and explain each inherited assignment.
- Write an NSG flow table on paper before validating it with IP flow verify.
- Read a small Bicep file, predict resource names/locations, then inspect what-if output.
- Build a lifecycle-rule timeline for three hypothetical blobs.
- Build a VM restore calendar and mark which data/configuration changes each operation recovers.
- Inspect a real log table's schema before writing a KQL event-level filter.

## Track weak areas

| Date | PDF question | Topic | My incorrect assumption | Correct reasoning | Recheck date |
| --- | --- | --- | --- | --- | --- |
| | | | | | |

A topic is ready when you can explain why the plausible alternatives fail and solve a changed version of the scenario.
