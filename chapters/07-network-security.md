# 7. Network security and load balancing

[Contents](../README.md) | [Previous](06-networks-and-dns.md) | [Next: monitoring and recovery](08-monitoring-and-recovery.md)

**Goal:** identify the rule or component that determines whether traffic reaches a healthy application.

## Read an NSG rule correctly

Write a flow as **source IP/port -> destination IP/port, protocol, direction**. Evaluate matching rules from the lowest priority number upward; the first matching rule decides the result. Custom priorities are 100-4096.

NSGs can apply at both subnet and NIC. Traffic must pass every applicable NSG. An allow on a NIC does not cancel a subnet deny. Default rules allow some VNet and load-balancer traffic and deny other unsolicited inbound traffic. NSGs are stateful. [NSG reference](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview).

**Example:** An inbound allow for TCP 443 at priority 200 is evaluated before a matching deny at 500 in that same NSG. It still cannot override a separate applicable NSG that denies the flow.

For the PDF's two-NSG RDP questions, evaluate each change against the whole path. Changing only one blocking NSG can leave the other blocking the connection. Do not confuse source port, destination port and inbound/outbound direction.

**Quiz connections:** Q33, Q200, Q224, Q226, Q232, Q246-248, Q250, Q257, Q265, Q267-268, Q283, Q286-288, Q297, Q307, Q312, Q320.

## Application security groups and rule scope

An ASG groups relevant NIC IP configurations for use in security rules. The ASG is not a firewall by itself: an NSG rule must refer to it. To add the VM in Q269, associate its NIC/IP configuration with the intended ASG.

Use the narrowest applicable protocol, source and destination. For ping, think ICMP plus guest firewall configuration; opening every TCP port does not specifically solve an ICMP requirement. To expose a multi-tier app, allow the public web port to the web tier, then restrict internal tier-to-tier connections as required.

One shared NSG can cover multiple resources with identical requirements. The minimum number depends on placement, available rule selectors and the actual requirement, rather than a universal one-NSG-per-VM rule.

**Quiz connections:** Q33, Q232, Q250, Q269, Q312, Q320.

## Bastion, service endpoints and private endpoints

Bastion provides managed administrative access to VMs without requiring a public IP on every target VM. For a dedicated deployment using an `AzureBastionSubnet`, follow the required name and current size. New such deployments need **/26 or larger**, so Q301's /27 is legacy guidance. Check SKU-specific architecture rather than applying that subnet rule to every Bastion offering. [Configuration reference](https://learn.microsoft.com/en-us/azure/bastion/configuration-settings).

A service endpoint and a private endpoint solve different network-access problems. Neither creates an RBAC assignment. Private endpoint troubleshooting also requires correct private DNS resolution.

**Quiz connections:** Q114, Q117, Q268, Q301. Private endpoints also deserve additional study beyond the PDF.

## Pick the traffic-distribution service

| Service | Layer / job | Typical question |
| --- | --- | --- |
| Azure Load Balancer | TCP/UDP distribution | Spread VM traffic at transport level |
| Internal Load Balancer | Private frontend | Balance an internal application tier |
| Application Gateway | HTTP(S) application routing | Route web traffic; use WAF for supported web-attack protection |
| Traffic Manager | DNS-based endpoint selection | Choose among reachable application endpoints |

An Application Gateway answer assumes an HTTP(S) workload. A CDN is not a general substitute for balancing a private application over VPN. For SQL injection protection, the source is testing a WAF-capable web gateway, not a transport-layer load balancer.

**Quiz connections:** Q66, Q230, Q237, Q319.

## Load Balancer building blocks

Follow the request:

```text
Client -> frontend IP -> load-balancing rule -> healthy member of backend pool
                                      health probe determines eligibility
```

The rule maps frontend traffic to backend traffic. The backend pool identifies target instances. The probe checks whether a backend is eligible. An **inbound NAT rule** forwards a particular frontend endpoint to a specific backend, as in the RDP-to-one-VM question.

If two VMs must each be reached using the same external port, the frontend endpoint combinations must remain distinguishable, for example by using different frontend IP addresses when required by the design. Merely adding a load-balancing rule does not select an individual administrative target.

A Standard public load balancer requires compatible Standard public IP configuration. Basic Load Balancer was retired on **30 September 2025**; source Basic-SKU constraints are historical. [SKU reference](https://learn.microsoft.com/en-us/azure/load-balancer/skus).

**Quiz connections:** Q225, Q233, Q253-254, Q272-273, Q279-281, Q291, Q294, Q305, Q315.

## Probes, persistence and Floating IP

For sticky-session questions, examine session persistence. Client-IP-based modes change flow distribution. Floating IP is not the general sticky-session setting.

For SQL Always On listener questions, a supported internal-LB design can require Floating IP and its specified TCP probe/cluster configuration. An HTTP probe against the SQL listener port is not an equivalent substitute. Choose a probe appropriate for the service and design.

A healthy probe alone does not prove user traffic is allowed; check frontend/rule mapping, NSGs, guest firewall, listening service and return route. Allow necessary probe traffic using the correct service configuration.

**Quiz connections:** Q28-30, Q223, Q237, Q245, Q272, Q314.

## Troubleshooting order

1. Confirm the destination IP/name and intended port.
2. Confirm the backend process is listening.
3. Check frontend, pool, rule and health probe.
4. Evaluate subnet and NIC NSGs for the exact flow.
5. Inspect guest firewall and route symmetry.
6. Use Network Watcher tools to test the remaining hypothesis.

This separates a traffic path problem from a permission or application-health problem. Source questions with screenshots need the actual priorities and associations; the chapter teaches the method rather than assuming the hidden values.
