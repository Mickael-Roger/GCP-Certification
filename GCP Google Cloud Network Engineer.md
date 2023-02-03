# VPC and subnets

## VPC networks

It's a virtual version of a physical network
It's gobal

Maximum default of 5 VPC networks per project

VPC networks contain subnets

Subnet belongs to only one region

IPv6 is supported

Best practicise: Delete the default VPC Network


### Type of VPC Network

- default: Created when compute engine is enabled. Each project has a default VPC (except if policy block that)
- auto: Fixed subnet range per region. Can expand from /20 to /16
- custom: No subnet automatically created. Custom IP range allocation


### MTU

MTU is configured at the VPC level. 

Can be from 1300 to 8896. MTU > 1600 can only be used if source and destination interfaces are in the same subnet


### Dynamic routing mode

Configured at the VPC Network level

Can be regional (Learn route only in the region) or global





### Subnets

It's possible ton configure apolicy at the organization level to prevent the creation of the default VPC Network

In each subnets, some IP addresses are reserved and cannot be used: The network and broadcast address and the the first and last IP address in the subnet


#### VPC Flow logs

Activated at the subnet level.

Logs all network flows


### VPC Peering

Can connect VPC Networks from the same project or different project/organization

Must be configured the same way on both side


### Shared VPC

Max project Host per Organization: 100. Max 100 Service Project per project

Shared VPC is only available for projects within an organization

A VM instance in one project can have a network interface into the Shared VPC Network. Otherwise, routes between shared VPC Networks and VPC Networks are propagated


# Firewall

Control incoming and outgoing traffic to an instance

Default: Allow all egress and block all ingress

Rules has priority number

A firewall rule is attached to a VPC Network and a subnet

Can use tags, ip addresses, or service account as selector

Firewall can trace logs event


