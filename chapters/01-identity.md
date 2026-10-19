# 1. Identity and access

[Contents](../README.md) | [Next: governance](02-governance.md) | [Question index](../QUESTION-INDEX.md)

**Goal:** distinguish who someone is, which directory they belong to, and what they are allowed to do.

## Directory, users and licenses

A **tenant** is an identity directory. A subscription is a resource and billing boundary. A user who can manage resources in one subscription does not automatically become an administrator in every directory.

For tenant-creation questions, check the administrator role **inside the new tenant**. A role in the original tenant is insufficient. The PDF's statement that only Global Administrators can create users is too broad: User Administrators can also manage users within their permitted scope.

Assign the appropriate directory role to delegate user administration. Assign a product license to enable licensed features. A license does not grant administrative permissions, and buying licenses does not automatically assign them to the intended users.

For bulk user operations, use the current CSV template supplied by the portal. Identify users by the required user identifier, such as their user principal name, rather than assuming display names are unique.

**Quiz connections:** Q38-40, Q50-51, Q87, Q327.

## Groups and delegated administration

| Requirement | Useful concept | Reason |
| --- | --- | --- |
| Add named people manually | Assigned membership | An administrator or group owner manages membership |
| Automatically include Finance employees | Dynamic user group | Membership follows a rule based on an attribute |
| Group devices by an attribute | Dynamic device group | The rule evaluates device properties |
| Share Microsoft 365 collaboration resources | Microsoft 365 group | It supports collaboration services |
| Delegate user administration per office | Administrative unit plus a scoped role | It limits the administrator's user-management scope |

A dynamic membership rule changes membership; it does not grant permission by itself. A group owner does not manually edit the membership of a dynamic group. Treat access reviews, group expiration and dynamic membership as different features.

Expiration in the PDF concerns **Microsoft 365 groups**. Expiration can involve renewal and activity-based renewal; it is not a promise that every group is deleted exactly 180 days after creation.

**Example:** Set each user's department consistently, use a dynamic Finance group, then target a Conditional Access policy at that group. New Finance employees can enter the policy scope automatically. This explains Q341 without memorizing an option letter.

**Quiz connections:** Q43-44, Q54, Q71, Q90, Q341.

## Custom domains and external users

To use a sign-in suffix such as `@example.com`, first add the custom domain to the tenant, publish the DNS verification record supplied by Microsoft, and verify ownership. A TXT record is commonly used; an MX record is another supported verification method in relevant flows. Creating an Azure DNS zone alone does not prove domain ownership.

Inviting a guest is a **B2B collaboration** task. If an invitation is blocked, check external collaboration restrictions and the inviter's permissions. Do not assume the solution is a new domain or a more powerful unrelated role.

**Quiz connections:** Q57, Q69, Q81.

## Hybrid identity and source of authority

When an account is synchronized from on-premises Active Directory, many profile attributes must be changed at the authoritative source. Cloud-only users are managed in the cloud. Determine the source of each attribute rather than assuming every property of a synchronized user is read-only in Entra ID.

For Microsoft Entra Connect Sync, a delta cycle processes changes; an initial cycle performs a fuller synchronization. For example:

```powershell
Start-ADSyncSyncCycle -PolicyType Delta
```

This example belongs on a server running the applicable Connect Sync tooling. Restarting Netlogon or forcing replication between domain controllers does not perform the cloud synchronization task.

**Quiz connections:** Q9-11, Q72.

## Conditional Access and MFA

Think through a policy in this order: **which users -> which resources -> which conditions -> which grant requirements**.

Grant controls decide what must be satisfied to allow access, such as MFA or an allowed device state. Session controls govern aspects of an already granted session. Changing the old per-user MFA settings does not configure the complete Conditional Access scenario.

If a scenario says **both** MFA and a device requirement are necessary, the policy must require all the selected controls. Selecting an OR combination changes the outcome. Also distinguish an Entra-joined device from an Entra hybrid-joined device and a compliant device; they are not interchangeable conditions. Use the available control matching the requirement. [Microsoft grant-control guidance](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-grant).

**Quiz connections:** Q2-4, Q68, Q341. Q6-8 describe legacy MFA provider billing models; see the corrections page.

## Devices and local administration

**Registered** often describes a personal device associated with a work account. **Entra joined** describes a device joined directly to the cloud directory. **Hybrid joined** combines on-premises domain membership with registration in Entra ID.

For device questions, check who may join devices, any join-time MFA requirement, the user's device quota, and how local administrator access is assigned. Permission to join a device is different from permission to administer every joined device. Do not confuse Cloud Device Administrator with the role used for local administrator access to Entra-joined devices.

**Quiz connections:** Q53-54, Q94, Q188, Q324.

## Password reset and managed identities

For SSPR, evaluate the enabled population, the required number of registered methods, methods allowed for that account type, and password writeback requirements for synchronized accounts. Administrator reset rules can differ from normal-user rules; the PDF's security-question examples should not be generalized.

A **managed identity** lets a supported Azure workload authenticate without storing an application password. Enable or attach the identity, then grant that identity the necessary role on the target resource. Enabling it alone does not grant access. For a VM managing resources in a resource group, enable the identity first, then assign an appropriate role at that resource group.

**Quiz connections:** Q78, Q105, Q323, Q326.

## Check your understanding

- Can a license replace a directory role? **No.**
- Does joining a device to Entra ID install the certificate required for certificate-based VPN access? **No.**
- Does a dynamic group enforce MFA by itself? **No; a policy must target it.**
- Does a managed identity have access to storage immediately? **Only after the required authorization is assigned.**
