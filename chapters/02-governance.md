# 2. Governance and permissions

[Contents](../README.md) | [Previous](01-identity.md) | [Next: storage](03-storage.md)

**Goal:** choose the right scope, permission and governance tool.

## Scopes and inherited access

Azure resource permissions can be assigned at management group, subscription, resource group or resource scope. An assignment at a parent can affect its descendants. An assignment on a resource group does not grant authority over its parent subscription.

```text
Management group
  Subscription
    Resource group
      Resource
```

A resource group's location stores its metadata. A resource inside it can be deployed in a different supported region. Moving an app to another resource group does not move the app's physical hosting region.

**Quiz connections:** Q45, Q48, Q64, Q70, Q189, Q242, Q260, Q278.

## Choose an Azure RBAC role

A role assignment combines **principal + role + scope**. Start with the exact operation and choose the narrowest sufficient assignment.

| Role | Study rule |
| --- | --- |
| Reader | View resource configuration |
| Contributor | Manage resources; does not ordinarily grant role-assignment authority |
| Owner | Manage resources and assign access |
| User Access Administrator / Role Based Access Control Administrator | Manage access within the role's capabilities and scope |
| Network Contributor | Manage network resources |
| Virtual Machine Contributor | Manage VMs, with limited related permissions; not general VNet management |
| Virtual Machine Administrator Login | VM sign-in capability, subject to the required configuration |
| Logic App Contributor | Manage logic apps |

Permission to use a VM, permission to change its configuration and permission to delegate access are separate. Roles accumulate, subject to deny assignments and other applicable constraints; having Reader alongside a stronger role does not reduce that stronger role.

**Example:** A user only needs to assign Reader on one VNet. A suitably scoped access-management role fits better than Owner on the whole subscription. A Contributor assignment does not solve the missing role-assignment permission.

Q76 asks for VM deployment **and VNet management**. Its VM Contributor key conflicts with the stated requirement; among the offered choices, Contributor is the sufficient choice. [VM Contributor definition](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/compute#virtual-machine-contributor).

**Quiz connections:** Q41, Q56, Q58-60, Q65, Q73-76, Q85, Q89, Q91-93, Q96, Q340.

## Directory roles and root access

Global Administrator is a directory role. Subscription Owner is an Azure resource role. Neither automatically means unrestricted authority in the other system.

For the root-management scenario, a Global Administrator can elevate to User Access Administrator at root scope, then grant the required Azure role. Subscription ownership alone does not give permission at a parent management group. Elevation is per user. [Microsoft's elevation procedure](https://learn.microsoft.com/en-us/azure/role-based-access-control/elevate-access-global-admin).

**Quiz connections:** Q70, Q77, Q95. Historical Service Administrator questions need to be separated from current RBAC and Service Health notification configuration.

## Custom roles and initiatives

A custom role describes allowed operations, exclusions, optional data operations and assignable scopes. `Actions` govern management operations; `DataActions` govern supported data operations. `NotActions` subtracts from that role's allowed operations; it is not an explicit deny against permissions from another role.

Assignable scopes describe **where the role can be assigned**. They do not assign it to anyone. For Q340's Reader-based role, retrieve and inspect the Reader role definition before creating a modified custom definition. Do not overwrite the built-in role.

An **initiative** groups policy definitions. A role that can create initiative definitions and one that can assign initiatives may need different permissions and scopes. Resource Policy Contributor is relevant; remedial deployments can also need identity permissions.

**Quiz connections:** Q65, Q96, Q340.

## Policy, tags and locks

| Tool | Question it answers | Example |
| --- | --- | --- |
| RBAC | Who can perform this operation? | Allow a group to create VMs |
| Policy | What resource configurations are permitted or required? | Deny an unapproved resource type |
| Tags | How do we label and report resources? | `Department=Finance` |
| Locks | Which management changes should be prevented? | Prevent accidental deletion |

Policy assignments can be inherited from parent scopes. An allow-like condition at a child does not cancel a parent's applicable deny. Read exclusions, effects, parameters and enforcement mode before deciding whether a request succeeds.

Assigning policy does not imply all existing resources are instantly fixed. `append`, `modify` and `deployIfNotExists` have different behavior; remediation may be necessary. An NSG policy must actually deploy or require the desired rule. A lock does not create a firewall rule, and unregistering a provider does not implement that network requirement.

Tags do **not automatically flow** from a resource group to its resources. Use a relevant policy when inheritance is required. Existing tags, policy-added tags and the containing resource group's tags must be evaluated separately.

**Quiz connections:** Q1, Q45-47, Q61, Q86, Q88, Q108, Q190, Q251, Q264, Q284, Q289, Q292.

## Deleting and moving resources

A **CanNotDelete** lock blocks deletion; a **ReadOnly** lock blocks management changes as well. Inherited locks matter. They apply to management operations and should not be treated as universal protection for resource data.

If resource-group deletion fails, inspect locks, backup-vault dependencies and the reported error. Turning off a VM does not remove a lock. A vault can require backup items and registrations to be resolved before deletion; stopping protection and retaining backup data may still leave dependencies.

For moves, distinguish changing resource group, changing subscription and changing region. A move can require related resources to move together. Check the resource-type move matrix; the PDF contains overly broad claims about VNets and attached NICs.

**Quiz connections:** Q48, Q55, Q64, Q79, Q86, Q121, Q189, Q242, Q278.

## Cost management

Tags can support department-based cost analysis. A budget evaluates spending and sends configured alerts. It does not stop VMs simply because a threshold is reached; shutdown needs an explicitly configured action. Read the budget scope and filters before adding costs. [Budget behavior](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets).

Azure Advisor can identify opportunities such as underused VMs. Unattached disks can continue costing money after a VM is deleted. Verify ownership and the need for data before taking any cleanup action.

**Quiz connections:** Q61, Q67, Q185, Q332.

## Check your understanding

Can a policy grant a user access? **No.** Can a Contributor assign Owner? **Ordinarily no.** Do RG tags automatically appear on a VM? **No.** Does changing an RG move a VM to another datacenter? **No.**
