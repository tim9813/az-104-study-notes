# 3. Storage

[Contents](../README.md) | [Previous](02-governance.md) | [Next: virtual machines](04-virtual-machines.md)

**Goal:** choose the right data service, redundancy and access mechanism.

## Account and service selection

Blob Storage holds objects in containers. Azure Files provides file shares. Queues hold messages; Tables store structured NoSQL entities. A storage account is the management resource that exposes supported services.

Standard general-purpose v2 (`StorageV2`) is a common choice for mixed workloads. Premium block-blob and premium file accounts serve different needs. Check performance, account kind, region, redundancy and feature compatibility together. Changing a resource group's location does not change the storage account's location.

For container persistence questions, the PDF uses an Azure Files volume: the data must survive a container restart. A blob container is not automatically an SMB-mounted filesystem.

**Quiz connections:** Q101-104, Q112, Q124, Q127, Q133, Q140, Q143-144.

## Redundancy

| Option | Primary placement | Second region | Read secondary without failover? |
| --- | --- | --- | --- |
| LRS | Local replicas | No | No |
| ZRS | Synchronous across zones | No | No |
| GRS | Local replicas | Asynchronous copy | No |
| RA-GRS | Local replicas | Asynchronous copy | Yes, for supported services |
| GZRS | Zone replicas | Asynchronous copy | No |
| RA-GZRS | Zone replicas | Asynchronous copy | Yes, for supported services |

Match the failure: local hardware, whole zone, or whole region. Secondary-region replication can lag. The `RA` prefix adds read access; it does not permit writing to the secondary endpoint. Azure Files does not provide RA-GRS/RA-GZRS secondary reads. Archive-tier compatibility also constrains redundancy selection. [Redundancy reference](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy).

**Quiz connections:** Q12, Q106, Q116, Q119, Q124, Q136.

## Tiers and lifecycle rules

Hot, cool, cold and archive address different access-frequency needs. Archive is offline: data must be rehydrated before normal reading. A low storage price can come with retrieval and early-deletion charges.

To solve a lifecycle timeline, list the blob's creation/modification dates, match the rule's filters, then apply its age conditions. Read which timestamp the rule actually uses. A container's name or a default tier is not a substitute for the blob's own state. Lifecycle work is asynchronous, so do not assume an exact execution minute. [Lifecycle guidance](https://learn.microsoft.com/en-us/azure/storage/blobs/lifecycle-management-overview).

The PDF has GPv1 and old feature-limit examples. Treat account-upgrade and redundancy-conversion questions as compatibility decisions; current supported conversion routes need checking.

**Quiz connections:** Q98, Q103, Q106, Q116, Q136-137, Q144.

## Protecting data and replicating objects

An **immutability policy** protects content against modification or deletion under its retention/legal-hold rules. An access tier controls cost and availability, not immutability. A normal access level setting does not satisfy a one-year write-once requirement.

Object replication copies supported block blobs asynchronously between accounts. The source needs change feed, and both participating accounts need blob versioning. Account kind and other feature combinations matter. Object replication is not the same thing as storage-account geo-redundancy. [Object replication requirements](https://learn.microsoft.com/en-us/azure/storage/blobs/object-replication-overview).

Soft delete and versioning address accidental deletion and overwrites. Redundant copies alone do not give you an independent history of a file that was changed incorrectly.

**Quiz connections:** Q97, Q99, Q145.

## Authentication versus network access

Two independent questions must be answered: **Can this client reach the endpoint? Is the request authorized?** A valid credential does not bypass a storage firewall.

| Method | Main idea | Typical trap |
| --- | --- | --- |
| Managed identity + data role | Workload authenticates without a stored account secret | Enabling identity alone grants nothing |
| Account key | Broad secret-based access | It is not least privilege for a read-only app |
| SAS | Delegated permissions bounded by token restrictions | Check expiry, protocol, IP and operation permissions |
| Stored access policy | Server-side constraints for a supported service SAS | Not supported by every SAS type |

For a blob-reading app with managed identity, assign a suitable data role such as Storage Blob Data Reader. Management-plane Contributor does not inherently grant blob data permissions. [Entra authorization](https://learn.microsoft.com/en-us/azure/storage/blobs/authorize-access-azure-active-directory).

A 30-day SAS can express a time limit, but it is itself a credential. Evaluate the exact SAS type and validity limits; do not assume a role assignment expires automatically. Q105's App1 account-key answer conflicts with minimizing secrets.

For firewall questions, distinguish allowed public source IPs from allowed VNets/subnets. A service endpoint authorizes a subnet path to a public service endpoint; a private endpoint exposes a private IP in your VNet. Trusted-service exceptions are specific, not blanket permission for every Azure-hosted VM.

**Quiz connections:** Q84, Q105, Q114, Q117, Q126, Q134.

## Copying data

AzCopy is useful for blobs and files. Copying a directory recursively is different from synchronizing changes. Use current tool documentation to select authorization for the destination service; do not turn the PDF's old authentication matrix into a permanent rule.

```powershell
# Syntax example only: provide your own destination and authorization.
azcopy copy 'D:\StudyData\*' 'https://ACCOUNT.blob.core.windows.net/CONTAINER' --recursive=true
```

Storage Explorer provides a graphical data-management interface. The PDF's Import/Export questions describe shipping prepared disks: understand preparation, job metadata and destination service. Historical import supports blobs/files, whereas its export scenario concerns blobs. Verify present service availability before planning a physical-transfer project.

**Quiz connections:** Q100, Q102, Q104, Q109, Q120, Q123, Q125-126, Q129, Q139, Q142.

## Azure Files and File Sync

An SMB share path has this shape:

```text
\\ACCOUNT.file.core.windows.net\SHARE
```

Direct SMB uses TCP **445**. HTTPS REST access uses a different path and protocol. A SAS should not be treated as a password for normal SMB `net use` drive mapping. Identity-based SMB access also involves the supported identity configuration and permissions.

For File Sync, remember the objects:

```text
Storage Sync Service
  Sync group
    One cloud endpoint: an Azure file share
    Server endpoint(s): folders on registered Windows Servers
```

Create the service and sync group/cloud endpoint in Azure. Install the agent on a supported server, register that server, then create its server endpoint. Service-side and server-side preparation can overlap, but the dependencies must exist before adding endpoints. Cloud tiering retains the namespace while caching selected content locally; a placeholder does not mean the file is absent. [File Sync planning](https://learn.microsoft.com/en-us/azure/storage/file-sync/file-sync-planning).

**Quiz connections:** Q107, Q110-111, Q113, Q115, Q118, Q130-131, Q138.

## Check your understanding

For synchronized zone protection, think **ZRS**. For regional replication plus secondary reads, think **RA**. For a drive-mapping requirement, think **Files + SMB**, not a blob URL. For secretless blob reads, think **managed identity + data role**.
