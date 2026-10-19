# 6. Networks and DNS

[Contents](../README.md) | [Previous](05-apps-and-containers.md) | [Next: network security](07-network-security.md)

**Goal:** trace addressing, connectivity, routing and name resolution as separate layers.

## Addresses and network interfaces

A VNet owns address ranges; a subnet is a range inside the VNet. Check overlap before connecting networks. Azure reserves five addresses in an IPv4 subnet, so a /24 has 251 normally usable addresses rather than 256.

A VM normally needs at least one NIC. A single NIC can have a private address and an associated public IP resource; five VMs do not need ten NICs merely because each needs both address types.

Configure private-IP allocation on the Azure NIC/IP configuration. Keep the guest configuration aligned with Azure; changing the Windows IP manually is not the right substitute for reserving the address at the Azure level. Q31 uses an old classic-networking cmdlet.

A NIC must meet regional, subscription and VNet constraints. The containing resource group's metadata region does not determine NIC compatibility. Likewise, a public IP resource is regional; its RG does not make it usable across regions.

**Quiz connections:** Q31-32, Q150-152, Q232, Q240, Q242, Q249, Q274.

## VNet peering

Peering connects compatible, non-overlapping VNets over Microsoft's network. It can span subscriptions and tenants with the required authorization. Check both peering directions and their states.

Peering is **not transitive**. If A peers with B and B peers with C, do not assume A can reach C directly. Add direct connectivity or implement a supported transit architecture with routing and appliances/gateways as appropriate.

Gateway transit is a separate setting. The gateway-hosting VNet allows gateway transit; a remote VNet uses the remote gateway where supported. Merely allowing forwarded traffic does not make all possible routes appear.

The PDF's address-space change workflow removes and recreates peering. Current Azure supports updating a peered VNet's address space and synchronizing peers; avoid unnecessary disconnection. [Address-space update reference](https://learn.microsoft.com/en-us/azure/virtual-network/update-virtual-network-peering-address-space).

**Quiz connections:** Q236, Q238, Q241, Q258, Q261, Q271, Q285, Q310, Q317.

## VPN, ExpressRoute and Virtual WAN

| Requirement | Start with |
| --- | --- |
| One remote computer needs private access | Point-to-site VPN |
| An office network needs an encrypted tunnel | Site-to-site VPN |
| Private provider connectivity to Azure | ExpressRoute |
| Many branches and managed hub connectivity | Azure Virtual WAN |

A site-to-site design includes a dedicated `GatewaySubnet`, an Azure virtual network gateway, an on-premises representation called a **local network gateway**, and a connection. The local network gateway describes the remote site's gateway/address prefixes; it is not the Azure VPN appliance.

For certificate-based P2S, a client needs its valid client certificate **including its private key**, the trusted root configuration, and the appropriate VPN profile. Installing the profile alone is insufficient. Joining the computer to Entra ID does not install that certificate. Prefer separate issued client certificates per device in a real deployment.

After topology changes, the PDF's Windows-client case requires a refreshed downloaded VPN configuration so the client learns the needed routes. This assumes the gateway/transit setup is otherwise valid. Its simultaneous description of a static/policy-based gateway and P2S is inconsistent: P2S needs a supported route-based gateway.

For resilience, distinguish redundant Azure gateway instances from redundant on-premises devices. Active-active designs use additional public IPs and tunnels. A VPN backup to ExpressRoute still needs correct gateway configuration and route preference; resource existence alone does not ensure automatic failover.

**Quiz connections:** Q24-27, Q231, Q238, Q256, Q262-263, Q270, Q282, Q295, Q298, Q308, Q313, Q318.

## User-defined routes and virtual appliances

A route table tells Azure how to forward matching traffic. Use the destination prefix and next-hop type/IP to trace the actual path. More-specific prefixes normally win; for equally specific routes, route source priorities apply.

For an NVA, enabling IP forwarding on its NIC is only one step. The guest must also forward traffic and its software/firewall must allow it. Routes must steer traffic through the NVA and provide a viable return path. An asymmetric route can break a stateful appliance flow.

In a gateway-to-appliance question, first identify which subnet's traffic must be redirected, then choose the destination route and appliance next hop. Do not associate a route table to the wrong subnet merely because it contains the NVA.

**Quiz connections:** Q222, Q227, Q237, Q270.

## Public DNS and delegation

| Record | Meaning |
| --- | --- |
| A / AAAA | Name to IPv4 / IPv6 address |
| CNAME | Alias to another DNS name |
| NS | Authoritative name servers / delegation |
| PTR | Reverse lookup |
| TXT | Text, often including ownership verification |
| MX | Mail routing; also used in some verification flows |

Creating a public Azure DNS zone does not make the registrar delegate the domain to Azure. Update the authoritative name servers at the registrar to the Azure-assigned values. For a child zone, publish the appropriate NS delegation in its parent.

An A record for `research.example.com` points to an address; it does not delegate authority for the entire `research.example.com` zone. A wildcard record is not a substitute for delegation.

For migrating a DNS zone, export/import the actual records using a supported zone-file workflow, then verify resolution and delegation. Q252's explanation about a private-zone migration script is not a general procedure for migrating a public DNS zone.

**Quiz connections:** Q57, Q80-81, Q83, Q171, Q252.

## Private DNS and custom servers

A private DNS zone is resolved through its configured VNet links and the client's DNS path. Link permissions for resolution and auto-registration are different. Auto-registration creates relevant VM records only for enabled registration links; it is not an automatic import of every DNS name in a tenant.

A VNet can have resolution links to multiple private zones, but its auto-registration relationship is constrained. A private zone can have multiple registration-enabled VNet links under current limits; do not generalize the PDF's older single-registration-network statement.

NIC-level DNS settings can override VNet DNS settings. After changing VNet DNS configuration, clients may need a lease refresh or restart to pick it up. A custom DNS IP without network reachability will not work.

Use FQDNs when testing. A guest's DNS suffix does not automatically create public authoritative records. A CNAME must ultimately resolve to a usable address, and successful resolution still does not guarantee ICMP or an application port is allowed.

**Quiz connections:** Q234, Q239, Q255, Q266, Q275, Q277, Q296, Q299-300, Q309, Q311.

## Check your understanding

If a name fails, test DNS. If the IP fails, test routing/security/service availability. If peering fails, test overlap and both peering states. If a P2S client fails authentication, check its certificate or the actual configured authentication method before changing NSG rules.
