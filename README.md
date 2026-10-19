# AZ-104 Study Notes

Easy-to-read topic notes built from **AZ-104_Cert King 1.pdf**, version 15.0: **341 questions across 334 PDF pages**.

Learn the reason behind an answer, then use the question index to revisit the original scenario. Repeated questions are combined into one explanation. The PDF itself and its full question bank are not included.

## Start here

1. Read a chapter and explain each decision rule in your own words.
2. Open the matching questions in your own PDF using the [question index](QUESTION-INDEX.md).
3. Check [corrections and legacy material](CORRECTIONS.md) before memorizing an answer.
4. Practise the [short recall questions and study plan](STUDY-PLAN.md).
5. Review the [quick reference](QUICK-REFERENCE.md) before doing another quiz.

## Chapters

| Chapter | What you will learn |
| --- | --- |
| [1. Identity and access](chapters/01-identity.md) | Entra users, groups, domains, devices, MFA, SSPR and managed identities |
| [2. Governance and permissions](chapters/02-governance.md) | RBAC, scopes, policies, tags, locks, resource moves and costs |
| [3. Storage](chapters/03-storage.md) | Redundancy, tiers, access, SAS, replication, AzCopy and File Sync |
| [4. Virtual machines and deployment](chapters/04-virtual-machines.md) | Availability, disks, resizing, ARM/Bicep, extensions and scale sets |
| [5. Web apps and containers](chapters/05-apps-and-containers.md) | App Service plans, slots, scaling, container storage and AKS concepts |
| [6. Networks and DNS](chapters/06-networks-and-dns.md) | Addresses, NICs, peering, VPNs, routing and name resolution |
| [7. Network security and load balancing](chapters/07-network-security.md) | NSG evaluation, ASGs, Bastion, probes, NAT and session persistence |
| [8. Monitoring, backup and recovery](chapters/08-monitoring-and-recovery.md) | Logs, metrics, alerts, troubleshooting, recovery points and Site Recovery |

## How current is this?

Prepared **19 October 2026**. The structure follows the five domains in the [Microsoft AZ-104 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-104), whose current objectives are dated 17 April 2026. Eight smaller chapters make those domains easier to study.

These are source-based notes with selected checks against Microsoft documentation, **not a claim that every PDF answer is correct or independently verified**. Some answers are images, and many questions need tables or case-study requirements. The index marks those as **exhibit-dependent**; it provides the concept to study rather than inventing a diagram-specific answer.

Azure AD is now **Microsoft Entra ID**. Legacy commands and retired services appear in the PDF. Keep them separate from current implementation guidance. Conditional Access and AKS topics are retained because they occur in this PDF; use the official objectives to prioritize your actual exam preparation.

## Topics to add beyond this PDF

The current objectives also deserve dedicated practice in Bicep, Azure Container Apps, encryption at host, storage encryption, blob and container soft delete, versioning, private endpoints, Azure Backup vaults, and alert processing rules. These notes introduce some of them, but do not replace a complete current syllabus.

## Source and review method

- Source: the user's local PDF; SHA-256 and extraction details are in [source metadata](SOURCE.md).
- All 341 numbered questions have one primary topic mapping in the index. Related chapters may discuss the same concept.
- Original explanations are rewritten into learning rules, comparisons, examples and recall prompts.
- Source errors and uncertain scenarios are explicitly separated from study guidance.
- Official references appear beside the relevant explanations and in [the sources list](SOURCES.md).

No Azure resources need to be deployed to read these notes. Hands-on exercises in the study plan are optional.
