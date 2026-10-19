# 8. Monitoring, backup and recovery

[Contents](../README.md) | [Previous](07-network-security.md) | [Question index](../QUESTION-INDEX.md)

**Goal:** collect the right evidence and choose the smallest recovery operation that meets the requirement.

## Pick the right monitoring signal

| Need | Start with |
| --- | --- |
| Numeric CPU, throughput or other time-series values | Metrics |
| Events, structured records and KQL | Logs / Log Analytics |
| Who changed an Azure resource? | Activity Log |
| Application requests, failures and dependencies | Application Insights |
| Platform incident notifications | Service Health alert |
| Right-sizing/cost improvement suggestions | Azure Advisor |

Host-level VM metrics and guest-level data are different. Guest events or memory counters need the appropriate collection configuration. Application code errors are not resolved by a Service Health alert about an Azure incident.

**Quiz connections:** Q36, Q52, Q62, Q67, Q82, Q199, Q321, Q331-333.

## Current collection and log alerts

For new VM guest collection, use **Azure Monitor Agent (AMA)** and data collection rules (DCRs). Associate the rule with the machine, collect the required events/counters, send data to the intended destination and confirm ingestion. The PDF's MMA/Log Analytics agent is retired. [Migration reference](https://learn.microsoft.com/en-us/azure/azure-monitor/agents/azure-monitor-agent-migration-tools).

For more than two Windows System error events in an hour, collect the events, query the Log Analytics table and use a scheduled query alert with the required threshold/window. A storage-account SAS does not configure event ingestion or make a storage account the appropriate log-query alert target.

```kusto
// Assumes the DCR sends Windows events to the Event table.
Event
| where TimeGenerated > ago(1h)
| where EventLog == "System"
| where EventLevelName == "Error"
| summarize ErrorCount = count() by Computer
| where ErrorCount > 2
```

Table and column names depend on the collection path. Inspect the schema; some configurations use other event tables. The PDF's `Event | search "error"` is a broad text search and is not equivalent to a precise event-level filter. KQL uses operators such as `==`; SQL `select *` and PowerShell `-eq` are not interchangeable syntax.

**Quiz connections:** Q62, Q82, Q146-147, Q156, Q167, Q199, Q216, Q331.

## Alert rules, action groups and application logs

An alert rule evaluates a signal and condition. An action group routes notifications or automation. Several rules can reuse the same group when their recipients/actions are the same. Different signals may need separate rules even when they share recipients.

The Email Azure Resource Manager Role action addresses supported directly assigned users; do not assume it emails service principals or expands every group membership. Notification limits, alert state and evaluation cadence affect counts. The PDF's one-per-minute arithmetic should not be treated as a universal delivery promise.

For HTTP 500 troubleshooting, enable the relevant application/server diagnostics and inspect the live log stream or exception telemetry. Request logs and exception details provide different evidence. Minimal-code Application Insights instrumentation depends on runtime and supported hosting mode.

**Quiz connections:** Q52, Q95, Q191, Q321, Q333, Q337-338.

## Network Watcher tool selection

| Question | Tool |
| --- | --- |
| Which NSG rule allows or denies a test flow? | IP flow verify |
| Does an endpoint connect right now? | Connection troubleshoot |
| How do reachability and latency change over time? | Connection Monitor |
| What packets were actually sent? | Packet capture |
| What traffic flows are recorded for analysis? | Supported flow logs / Traffic Analytics |

Connection Monitor is designed for ongoing reachability/latency tests. Packet capture records packet data for inspection. Q302-306 blur these two requirements: select by whether you need actual traffic contents or synthetic connectivity monitoring. [Packet capture](https://learn.microsoft.com/en-us/azure/network-watcher/packet-capture-overview), [Connection Monitor](https://learn.microsoft.com/en-us/azure/network-watcher/connection-monitor-overview).

NSG flow logs are on a retirement path; use current VNet flow-log guidance for new deployments. Validate required permissions rather than accepting the PDF's claim that Reader can enable configuration changes. Network Performance Monitor is legacy material.

**Quiz connections:** Q73-75, Q89, Q182, Q221, Q235, Q267, Q276, Q293, Q302-304, Q306, Q328.

## Vaults and backup policies

For the source's Azure VM backup questions, use a **Recovery Services vault**, a backup policy and enabled protection. Policies define schedule and retention. Read the time zone, daily/weekly/monthly/yearly rules and any instant-restore snapshot retention separately.

Vault placement must meet the workload's region/subscription support rules. The VM and its resource group can be in different regions; check the actual VM region. A VM not in the vault's supported location cannot be fixed by assigning a different backup time.

A healthy VM agent/backup extension matters for guest-aware backups. A stopped VM is not automatically excluded, but backup consistency and support depend on the configuration. The separate **MARS** agent backs up selected Windows files/folders; it is not the same as the VM backup extension. [Azure VM backup workflow](https://learn.microsoft.com/en-us/azure/backup/backup-azure-vms-first-look-arm).

**Quiz connections:** Q37, Q121-122, Q135, Q141, Q148, Q192, Q213, Q330, Q334-336.

## Restore choices

| Goal | Recovery choice to evaluate |
| --- | --- |
| Recover a few files | File Recovery / compatible mount-and-copy process |
| Rebuild a VM separately | Create a new VM from a recovery point |
| Roll disks back on a suitable existing VM | Replace existing disks |
| Use restored disks in a controlled recovery | Restore disks, then attach/rebuild as needed |
| Recover MARS-protected files elsewhere | Register/use the compatible recovery agent workflow |

Disk restore does not mean every resource setting is rolled back. For Q322, distinguish VM size and resource metadata from contents inside the restored disks and from disks added after the recovery point. [Restore behavior](https://learn.microsoft.com/en-us/azure/backup/about-azure-vm-restore).

For ransomware, select a suitable clean recovery point and a safe recovery target. Q34's restriction to the infected VM is wrong. A file-recovery mount needs a compatible OS/filesystem; the Linux-to-Windows shortcut in Q329 is not generally valid. Use a compatible recovery machine, then transfer recovered files to the final destination. [File-recovery compatibility](https://learn.microsoft.com/en-us/azure/backup/backup-azure-restore-files-from-vm).

**Quiz connections:** Q34-35, Q148, Q212, Q322, Q329.

## Count recovery points carefully

Draw a calendar, place successful backups at the scheduled times, assign retention labels, remove expired points and count **unique** surviving points. One backup can qualify for multiple retention labels, so daily plus weekly plus monthly counts cannot simply be added.

Q339's printed 6/8 result depends on its exact dates and retention exhibit. It is not a reusable formula. Q335 likewise depends on which backup qualifies as monthly/yearly. Do not confuse snapshot-tier retention with the vault-tier history.

**Quiz connections:** Q330, Q335, Q339.

## Site Recovery and vault dependencies

Azure Backup supplies recoverable historical data. **Azure Site Recovery** replicates workloads for failover; recovery plans can sequence application recovery. A recovery plan is not the vault you must create first for a backup configuration.

For an on-premises Hyper-V replication question, identify the vault, replication infrastructure, target storage/network configuration and supported source setup. The exact required objects depend on the replication scenario in its diagram. For Azure-to-Azure disaster recovery, practise enabling replication, test failover, cleanup and reprotection separately from restoring a backup.

Before deleting a vault, resolve all protected items, retained/soft-deleted data and registered dependencies according to the applicable service configuration. Stopping a VM or deleting an unrelated storage account does not make a vault empty.

**Quiz connections:** Q23, Q55, Q79, Q121, Q135, Q141.

## Check your understanding

Errors in guest logs? **Collect them, query them, then alert.** Latency over time? **Connection Monitor.** Real packet inspection? **Packet capture.** A few lost files? **Evaluate file-level recovery first.** Full application failover? **Consider Site Recovery rather than confusing it with backup retention.**
