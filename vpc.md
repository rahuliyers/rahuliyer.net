# Virtual Private Clouds

## IPv4 Address

An IPv4 address looks like this: 192.168.0.1

The address is 32 bits in length, with each number separated by a period being an eight bit number.

Hence an IPv4 address can be expressed as a decimal or a binary number:


| Format  | Representation |
| --- | ----------- |
| Decimal: | 192.168.0.1|
| Binary: | 1100000000 . 10101000 . 00000000 . 00000001 |

IPv4 limit = 2<sup>32</sup> bits = 4294967296 addresses, excluding reserved blocks.

### Classful Networking :

| Network Class | Addresses |
| --- | --- |
| A |  2<sup>24</sup> |
| B |  2<sup>16</sup> |
| C |  2<sup>8</sup>  |

But IPv4 has only 4 Billion addresses (ran out of them.) So we have IPv6 now, which has 128Bits!

## CIDR or Classless Inter-Domain Routing

Instead of having networks of fixed sizes (Class A,B,C etc), where many addresses are wasted, CIDR lets us specify the size of our network in a more granular manner.

| Example  | Host | Binary Representation | Network Prefix | Subnet Mask (Binary)| Subnet Mask (IP Notation)| Number of Addresses  (including reserved addresses)|
| --- | --- |  --- | --- | --- | --- | --- |
| 192.168.0.0/24 | 192.168.0.0| 11000000.10101000.00000000.00000000 | 11000000.10101000.00000000 | 11111111.11111111.11111111.00000000 | 255.255.255.0 | 2<sup>8</sup> |
| 192.168.0.0/16 | 192.168.0.0| 11000000.10101000.00000000.00000000 | 11000000.10101000.00000000 | 11111111.11111111.00000000.00000000 | 255.255.0.0 | 2<sup>16</sup> |

The number after the "/", such as /24, /16 represents the number of bits to use to represent the host address. The remaining bits can be used for representing the addresses in the network.

If there are more than two addresses in the network, then some addresses are reserved. Normally the first and last address are used for **identification** and ** broadcast** purposes. In **AWS VPC** the first four, and last address are reserved (five reserved addresses). Hence if you compute 256 addresses, rememeber to subtract five addresses, when computing the number of available ip's for your network in AWS.

## AWS VPC (Amazon Web Services Virtual Private Cloud)

VPCs may be no larger than a /16 block. In other words, they can have 216 – or 65,536 – total addresses. The smallest size can be /26 (16 IP addresses)

AWS reserves the first four IPv4 addresses for their internal services, and the last address is also reserved.

When selecting a block for a VPC, AWS recommends using one of the private IPv4 ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)

| RFC 1918 range |	Example CIDR block |
| --- | --- |
| 10.0.0.0 - 10.255.255.255 (10/8 prefix) |	Your VPC must be /16 or smaller, for example, 10.0.0.0/16. |
| 172.16.0.0 - 172.31.255.255 (172.16/12 prefix) |	Your VPC must be /16 or smaller, for example, 172.31.0.0/16. |
| 192.168.0.0 - 192.168.255.255 (192.168/16 prefix) |	Your VPC can be smaller, for example 192.168.0.0/20. |

**You can create a VPC with a publicly routable CIDR block that falls outside of the private IPv4 ranges specificed in RFC 1918**

### Creating a VPC

- First specify a name, CIDR block and **Tenancy**.

*Tenancy* can be either *Default* or *Dedicated* - Dedicated Tenancy ensures all EC2 instances that are launched in a VPC run on hardware that is dedicated to a single customer. By default, EC2 instances may run on hardware shared with other customers.

A VPC spans all of the availability zones in a region.

### Creating a Subnet

- Then create a subnet, by specifying a name, the VPC, availability zone and CIDR block.

The CIDR block of a subnet can be the same as the CIDR block for the VPC (for a single subnet in the VPC), or a subset of the CIDR block for the VPC (for multiple subnets). The allowed block size is between a /28 netmask and /16 netmask. If you create more than one subnet in a VPC, the CIDR blocks of the subnets cannot overlap.

**The first four IP addresses and the last IP address in each subnet CIDR block are not available for you to use, and cannot be assigned to an instance.**

For example, suppose our VPC is 10.200.0.0/16

Let us create a subnet which is /20 within this subnet.
The first address will be 10.200.0.0
The last address will be computed, by doing the following:

- The first 16 bits are used for the host address.
- Since the subnet is /20, it means that is will use 4096, or 2<sup>12</sup> addresses (since 32-20 = 12). Since /20 uses half of the third octet, we can compute the last IP address by converting the last four bits to all 'ones' and converting the decimal number --> 1111 = 15. The final octet will also be all 'ones', (11111111 = 255) Therefore the last IP address in the subnet will be 10.200.15.255.

### Creating additional Subnets

Subnets cannot overlap their addresses without other subnets within the same VPC. Hence it makes sense to start the next subnet at the address following where the previous subnet ends. You can use any address, but it makes sense to allocate contiguous blocks.

For example, the next subnet (/20) can be 10.200.16.0/20 -> This starts at the very next address after which the previous subnet ended.

- The next subnet can be in a different availability zone. A VPC can span only a single region, but can contain subnets from multiple availability zones.

## Default VPC and subnet

**NOTE:** A default VPC already exists in each region, so you can immediately start launching EC2, RDS, ELB or EMR  in the default VPC.

The default VPC is  (172.31.0.0/16) (65536 addresses), and contains a /20 default subnet in each availability zone (4096 addresses per subnet)

The default VPC has an internet gateway, a default security group, default network access control list and feault DHCP options.

You can delete your default VPC and subnet, or create a default VPC and subnet. You cannot restore a deleted default VPC.

 If you choose to create a default VPC, the subnet CIDR blocks may not map to the same availability zones as your previous default VPC. Likewise, you can create a default subnet in an availability zone if it does not have one - it will be of size /20, and you cannot specify the CIDR block yourself, or restore a previous default subnet that was deleted. There can be only one default subnet per availability zone, and you cannot create a default subnet in a nondefault VPC.

 If there is not enough address space in your default VPC to create a /20 CIDR block the request fails. To add more address space you can add an IPv$ CIDR block to your VPC.

 If you've associated an IPv6 CIDR block with your default VPC, the new default subnet does not automatically receive an IPv6 CIDR block. Instead, you can associate an IPv6 CIDR block with the default subnet after you create it. For more information, see Associating an IPv6 CIDR block with your subnet.

Currently, you can create a default subnet using the AWS CLI, an AWS SDK, or the Amazon EC2 API only.

## Route Tables

A route table contains a set of rules, called routes, that are used to determine where network traffic from your subnet or gateway is directed.

The following are the key concepts for route tables.

- **Main route table** —The route table that automatically comes with your VPC. It controls the routing for all subnets that are not explicitly associated with any other route table.
- **Custom route table** —A route table that you create for your VPC.
- **Edge association** —A route table that you use to route inbound VPC traffic to an appliance. You associate a route table with the internet gateway or virtual private gateway, and specify the network interface of your appliance as the target for VPC traffic.
- **Route table association** —The association between a route table and a subnet, internet gateway, or virtual private gateway.
- **Subnet route table** —A route table that's associated with a subnet.
- **Gateway route table** —A route table that's associated with an internet gateway or virtual private gateway.
- **Local gateway route table** —A route table that's associated with an Outposts local gateway. For information about local gateways, see Local Gateways in the AWS Outposts User Guide.
- **Destination** —The range of IP addresses where you want traffic to go (destination CIDR). For example, an external corporate network with a 172.16.0.0/12 CIDR.
- **Propagation** —Route propagation allows a virtual private gateway to automatically propagate routes to the route tables. This means that you don't need to manually enter VPN routes to your route tables. For more information about VPN routing options, see Site-to-Site VPN routing options in the Site-to-Site VPN User Guide.
- **Target** —The gateway, network interface, or connection through which to send the destination traffic; for example, an internet gateway.
- **Local route** —A default route for communication within the VPC.

## How Route Tables work

- Your VPC has an implicit router, and you use route tables to control where network traffic is directed.
- Each subnet in your VPC must be associated with a route table, which controls the routing for the subnet(subnet route table).
- You can explicitly associate a subnet with a particular route table. Otherwise the subnet is implicitly associated with the main route table.
- A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same subnet route table.
- You can optionally associate a route table with an internet gateway or a virtual private gateway (gateway route table).  This enables you to specify routing rules for inbound traffic that enters your VPC through the gateway.
- There is a quota on the number of route tables you can create per VPC, as well as a quota on the number of routes that you can add per route table.
- You can disassociate a subnet from a route table. Until you associate the subnet with another route table, it's implicitly associated with the main route table.

Each route in a table specifies a destination and a target. For example, to enable your subnet to access the internet through an internet gateway, add the following route to your subnet route table.

| Destination |	Target |
| --- | --- |
| 0.0.0.0/0 |	igw-12345678901234567 |

**The destination for the route is 0.0.0.0/0, which represents all IPv4 addresses. The target is the internet gateway that's attached to your VPC.**

**CIDR blocks for IPv4 and IPv6 are treated separately. For example, a route with a destination CIDR of 0.0.0.0/0 does not automatically include all IPv6 addresses. You must create a route with a destination CIDR of ::/0 for all IPv6 addresses.**

**Every route table contains a local route for communication within the VPC. This route is added by default to all route tables. If your VPC has more than one IPv4 CIDR block, your route tables contain a local route for each IPv4 CIDR block. If you've associated an IPv6 CIDR block with your VPC, your route tables contain a local route for the IPv6 CIDR block. You cannot modify or delete these routes in a subnet route table or in the main route table.**

**If your route table has multiple routes, we use the most specific route that matches the traffic (longest prefix match) to determine how to route the traffic.**

## Example route table:

| Destination | Target |
| --- | --- |
| 10.0.0.0/16 | Local |
| 2001:db8:1234:1a00::/56	| Local |
| 172.31.0.0/16	| pcx-11223344556677889 |
|0.0.0.0/0	| igw-12345678901234567 |
| ::/0	| eigw-aabbccddee1122334 |

In the above example, an IPv6 CIDR block is associated with your VPC. In your route table:

- IPv6 traffic destined to remain within the VPC (2001:db8:1234:1a00::/56) is covered by the Local route, and is routed within the VPC.
- IPv4 and IPv6 traffic are treated separately; therefore, all IPv6 traffic (except for traffic within the VPC) is routed to the egress-only internet gateway.
- There is a route for 172.31.0.0/16 IPv4 traffic that points to a peering connection.
- There is a route for all IPv4 traffic (0.0.0.0/0) that points to an internet gateway.
- There is a route for all IPv6 traffic (::/0) that points to an egress-only internet gateway.

**If you frequently reference the same set of CIDR blocks across your AWS resources, you can create a customer-managed prefix list to group them together. You can then specify the prefix list as the destination in your route table entry.**

## Main Route table

| Destination | target |
| :------------- | :------------- |
| 10.200.0.0/16 | local |

In our previous example, after creating the subnets, the subnets are added to the main route table by default. The destination 10.200.0.0/16 / local target combination ensures that all subnets can communicate with each other using their private addresses.

## Internet Gateways

A VPC can have a single Internet Gateway. The default VPC has an internet gateway attached. But if you create a custom VPC then you would have to create your own Internet Gateway if you would like the VPC to directly connect to the internet.

- If you create your own Internet Gateway, it will be created in the detached state, and you would need to attach it to your VPC.
- Even after attaching it to your VPC, it does not mean that your instances can use it right away. You must add a route in your route table. The destination must be 0.0.0.0/0 and Target will be the internet gateway. Note that this simply allows IPv4 access to the internet. For IPv6, you would have to add a route for IPv6 as well. In the case of IPv6, it would be ::/0, and the target would be the egress only internet gateway.

At this point, you have your VPC setup, and can proceed to [creating EC2 instances](ec2.md).
