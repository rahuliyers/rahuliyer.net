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

Enabling internet access

To enable access to or from the internet for instances in a subnet in a VPC, you must do the following:

Attach an internet gateway to your VPC.
- Add a route to your subnet's route table that directs internet-bound traffic to the internet gateway. If a subnet is associated with a route table that has a route to an internet gateway, it's known as a public subnet. If a subnet is associated with a route table that does not have a route to an internet gateway, it's known as a private subnet.
- Ensure that instances in your subnet have a globally unique IP address (public IPv4 address, Elastic IP address, or IPv6 address).
- Ensure that your network access control lists and security group rules allow the relevant traffic to flow to and from your instance.
- In your subnet route table, you can specify a route for the internet gateway to all destinations not explicitly known to the route table (0.0.0.0/0 for IPv4 or ::/0 for IPv6). Alternatively, you can scope the route to a narrower range of IP addresses; for example, the public IPv4 addresses of your company’s public endpoints outside of AWS, or the Elastic IP addresses of other Amazon EC2 instances outside your VPC.

To enable communication over the internet for IPv4, your instance must have a public IPv4 address or an Elastic IP address that's associated with a private IPv4 address on your instance. Your instance is only aware of the private (internal) IP address space defined within the VPC and subnet. The internet gateway logically provides the one-to-one NAT on behalf of your instance, so that when traffic leaves your VPC subnet and goes to the internet, the reply address field is set to the public IPv4 address or Elastic IP address of your instance, and not its private IP address. Conversely, traffic that's destined for the public IPv4 address or Elastic IP address of your instance has its destination address translated into the instance's private IPv4 address before the traffic is delivered to the VPC.

To enable communication over the internet for IPv6, your VPC and subnet must have an associated IPv6 CIDR block, and your instance must be assigned an IPv6 address from the range of the subnet. IPv6 addresses are globally unique, and therefore public by default.

**A Default VPC does not have a route for IPv6 traffic to the internet gateway by default, but has a route for IPv4 traffic**

**Instances launched in a default subnet have a public IPv4 address by default, but do not have a public IPv6 address**

### Egress-only internet gateways

An egress-only internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows outbound communication over IPv6 from instances in your VPC to the internet, and prevents the internet from initiating an IPv6 connection with your instances. An egress-only internet gateway is for use with IPv6 traffic only. To enable outbound-only internet communication over IPv4, use a NAT gateway instead.

### NAT

You can use a NAT device to enable instances in a private subnet to connect to the internet (for example, for software updates) or other AWS services, but prevent the internet from initiating connections with the instances. A NAT device forwards traffic from the instances in the private subnet to the internet or other AWS services, and then sends the response back to the instances. When traffic goes to the internet, the source IPv4 address is replaced with the NAT device’s address and similarly, when the response traffic goes to those instances, the NAT device translates the address back to those instances’ private IPv4 addresses.

NAT devices are not supported for IPv6 traffic—use an egress-only Internet gateway instead. For more information, see Egress-only internet gateways.

AWS offers two kinds of NAT devices—a NAT gateway or a NAT instance. We recommend NAT gateways, as they provide better availability and bandwidth over NAT instances. The NAT Gateway service is also a managed service that does not require your administration efforts. A NAT instance is launched from a NAT AMI. You can choose to use a NAT instance for special purposes.

- **To create a NAT gateway, you must specify the public subnet in which the NAT gateway should reside.**

- **You must also specify an Elastic IP address to associate with the NAT gateway when you create it. The Elastic IP address cannot be changed after you associate it with the NAT Gateway. After you've created a NAT gateway, you must update the route table associated with one or more of your private subnets to point internet-bound traffic to the NAT gateway. This enables instances in your private subnets to communicate with the internet.**

- Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone. You have a quota on the number of NAT gateways you can create in an Availability Zone

- If you have resources in multiple Availability Zones and they share one NAT gateway, and if the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access. To create an Availability Zone-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone.

- **If you no longer need a NAT gateway, you can delete it. Deleting a NAT gateway disassociates its Elastic IP address, but does not release the address from your account.**

### NAT Limitations

- A NAT gateway **supports 5 Gbps of bandwidth and automatically scales up to 45 Gbps. If you require more, you can distribute the workload by splitting your resources into multiple subnets, and creating a NAT gateway in each subnet.**
- You can associate exactly one Elastic IP address with a NAT gateway. You cannot disassociate an Elastic IP address from a NAT gateway after it's created. To use a different Elastic IP address for your NAT gateway, you must create a new NAT gateway with the required address, update your route tables, and then delete the existing NAT gateway if it's no longer required.
- A NAT gateway supports the following protocols: TCP, UDP, and ICMP.
You cannot associate a security group with a NAT gateway. You can use security groups for your instances in the private subnets to control the traffic to and from those instances.
- You can use a network ACL to control the traffic to and from the subnet in which the NAT gateway is located. The network ACL applies to the NAT gateway's traffic. A NAT gateway uses ports 1024–65535. For more information, see Network ACLs.
-When a NAT gateway is created, it receives a network interface that's automatically assigned a private IP address from the IP address range of your subnet. You can view the NAT gateway's network interface in the Amazon EC2 console. For more information, see Viewing details about a network interface. You cannot modify the attributes of this network interface.
-A NAT gateway cannot be accessed by a ClassicLink connection that is associated with your VPC.
-You cannot route traffic to a NAT gateway through a VPC peering connection, a Site-to-Site VPN connection, or AWS Direct Connect. A NAT gateway cannot be used by resources on the other side of these connections.
-A NAT gateway can support up to 55,000 simultaneous connections to each unique destination. This limit also applies if you create approximately 900 connections per second to a single destination (about 55,000 connections per minute). If the destination IP address, the destination port, or the protocol (TCP/UDP/ICMP) changes, you can create an additional 55,000 connections. For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors. These errors can be monitored by viewing the ErrorPortAllocation CloudWatch metric for your NAT gateway. For more information, see Monitoring NAT gateways using Amazon CloudWatch.

### Migrating from a NAT Instance

- If you're already using a NAT instance, you can replace it with a NAT gateway. To do this, you can create a NAT gateway in the same subnet as your NAT instance, and then replace the existing route in your route table that points to the NAT instance with a route that points to the NAT gateway. To use the same Elastic IP address for the NAT gateway that you currently use for your NAT instance, you must first also disassociate the Elastic IP address from your NAT instance and then associate it with your NAT gateway when you create the gateway.

- If you change your routing from a NAT instance to a NAT gateway, or if you disassociate the Elastic IP address from your NAT instance, any current connections are dropped and have to be re-established. Ensure that you do not have any critical tasks (or any other tasks that operate through the NAT instance) running.

**To avoid data processing charges for NAT gateways when accessing Amazon S3 and DynamoDB that are in the same Region, set up a gateway endpoint and route the traffic through the gateway endpoint instead of the NAT gateway. There are no charges for using a gateway endpoint.**

### NAT Instances

 The NAT instance must have internet access. It must be in a public subnet (a subnet that has a route table with a route to the internet gateway), and it must have a public IP address or an Elastic IP address. NAT is not supported for IPv6 traffic—use an egress-only internet gateway instead.

 ### NAT Gateways vs NAT Instances

 | Attribute	| NAT gateway	| NAT instance |
 | --- | --- | --- |
 | Availability | 	Highly available. NAT gateways in each Availability Zone are implemented with redundancy. Create a NAT gateway in each Availability Zone to ensure zone-independent architecture. | 	Use a script to manage failover between instances.|
 | Bandwidth	| Can scale up to 45 Gbps. | 	Depends on the bandwidth of the instance type.
 | Maintenance | Managed by AWS. You do not need to perform any maintenance. |	Managed by you, for example, by installing software updates or operating system patches on the instance.|
 | Performance| 	Software is optimized for handling NAT traffic. | 	A generic Amazon Linux AMI that's configured to perform NAT.|
 | Cost	| Charged depending on the number of NAT gateways you use, duration of usage, and amount of data that you send through the NAT gateways. |	Charged depending on the number of NAT instances that you use, duration of usage, and instance type and size.|
 | Type and size |	Uniform offering; you don’t need to decide on the type or size.	| Choose a suitable instance type and size, according to your predicted workload.|
 | Public IP addresses| 	Choose the Elastic IP address to associate with a NAT gateway at creation.	| Use an Elastic IP address or a public IP address with a NAT instance. You can change the public IP address at any time by associating a new Elastic IP address with the instance.|
 | Private IP addresses| 	Automatically selected from the subnet's IP address range when you create the gateway.| 	Assign a specific private IP address from the subnet's IP address range when you launch the instance.|
 | Security groups	| Cannot be associated with a NAT gateway. You can associate security groups with your resources behind the NAT gateway to control inbound and outbound traffic.| Associate with your NAT instance and the resources behind your NAT instance to control inbound and outbound traffic.|
 | Network ACLs| 	Use a network ACL to control the traffic to and from the subnet in which your NAT gateway resides.| 	Use a network ACL to control the traffic to and from the subnet in which your NAT instance resides.|
 | Flow logs	| Use flow logs to capture the traffic.| 	Use flow logs to capture the traffic.|
 | Port forwarding| 	Not supported.	|Manually customize the configuration to support port forwarding.|
 | Bastion servers| 	Not supported. |	Use as a bastion server.|
 | Traffic metrics| 	View CloudWatch metrics for the NAT gateway.|	View CloudWatch metrics for the instance.|
 | Timeout behavior	| When a connection times out, a NAT gateway returns an RST packet to any resources behind the NAT gateway that attempt to continue the connection (it does not send a FIN packet).| 	When a connection times out, a NAT instance sends a FIN packet to resources behind the NAT instance to close the connection.|
 | IP fragmentation	| Supports forwarding of IP fragmented packets for the UDP protocol.Does not support fragmentation for the TCP and ICMP protocols. Fragmented packets for these protocols will get dropped.| Supports reassembly of IP fragmented packets for the UDP, TCP, and ICMP protocols. |

 ### Elastic IP Addresses

 An Elastic IP address is a static, public IPv4 address designed for dynamic cloud computing. You can associate an Elastic IP address with any instance or network interface in any VPC in your account. With an Elastic IP address, you can mask the failure of an instance by rapidly remapping the address to another instance in your VPC.

 To use an Elastic IP address, you first allocate it for use in your account. Then, you can associate it with an instance or network interface in your VPC. Your Elastic IP address remains allocated to your AWS account until you explicitly release it.

An Elastic IP address is a property of a network interface. You can associate an Elastic IP address with an instance by updating the network interface attached to the instance. The advantage of associating the Elastic IP address with the network interface instead of directly with the instance is that you can move all the attributes of the network interface from one instance to another in a single step.

### VPC endpoints

- Interface Endpoints and Gateway endpoints.
- An interface VPC endpoint (interface endpoint) enables you to connect to services powered by AWS PrivateLink. These services include some AWS services, services hosted by other AWS customers and Partners in their own VPCs (referred to as endpoint services), and supported AWS Marketplace Partner services. The owner of the service is the service provider, and you, as the principal creating the interface endpoint, are the service consumer. Services cannot initiate requests to resources in your VPC through the endpoint. An endpoint only returns responses to traffic that is initiated from resources in your VPC.

-You can have multiple endpoint routes to different services in a route table, and you can have multiple endpoint routes to the same service in different route tables. But you cannot have multiple endpoint routes to the same service in a single route table. For example, if you create two endpoints to Amazon S3 in your VPC, you cannot create endpoint routes for both endpoints in the same route table.
-You cannot explicitly add, modify, or delete an endpoint route in your route table by using the route table APIs, or by using the Route Tables page in the Amazon VPC console. You can only add an endpoint route by associating a route table with an endpoint. To change the route tables that are associated with your endpoint, you can modify the endpoint.
-An endpoint route is automatically deleted when you remove the route table association from the endpoint (by modifying the endpoint), or when you delete your endpoint

We use the most specific route that matches the traffic to determine how to route the traffic (longest prefix match). If you have an existing route in your route table for all internet traffic (0.0.0.0/0) that points to an internet gateway, the endpoint route takes precedence for all traffic destined for the service, because the IP address range for the service is more specific than 0.0.0.0/0. All other internet traffic goes to your internet gateway, including traffic that's destined for the service in other Regions.

However, if you have existing, more specific routes to IP address ranges that point to an internet gateway or a NAT device, those routes take precedence. If you have existing routes destined for an IP address range that is identical to the IP address range used by the service, then your routes take precedence.

### VPC peering

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IPv4 addresses or IPv6 addresses. Instances in either VPC can communicate with each other as if they are within the same network. You can create a VPC peering connection between your own VPCs, or with a VPC in another AWS account. The VPCs can be in different regions (also known as an inter-region VPC peering connection).

AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor a VPN connection, and does not rely on a separate piece of physical hardware. There is no single point of failure for communication or a bandwidth bottleneck.

To establish a VPC peering connection, you do the following:

- The owner of the requester VPC sends a request to the owner of the accepter VPC to create the VPC peering connection. The accepter VPC can be owned by you, or another AWS account, and cannot have a CIDR block that overlaps with the requester VPC's CIDR block.
- The owner of the accepter VPC accepts the VPC peering connection request to activate the VPC peering connection.
- To enable the flow of traffic between the VPCs using private IP addresses, the owner of each VPC in the VPC peering connection must manually add a route to one or more of their VPC route tables that points to the IP address range of the other VPC (the peer VPC).
- If required, update the security group rules that are associated with your instance to ensure that traffic to and from the peer VPC is not restricted. If both VPCs are in the same region, you can reference a security group from the peer VPC as a source or destination for ingress or egress rules in your security group rules.
- By default, if instances on either side of a VPC peering connection address each other using a public DNS hostname, the hostname resolves to the instance's public IP address.
- To change this behavior, enable DNS hostname resolution for your VPC connection. After enabling DNS hostname resolution, if instances on either side of the VPC peering connection address each other using a public DNS hostname, the hostname resolves to the private IP address of the instance.

### VPC Peering Limitations:

- You cannot create a VPC peering connection between VPCs that have matching or overlapping IPv4 or IPv6 CIDR blocks. Amazon always assigns your VPC a unique IPv6 CIDR block. If your IPv6 CIDR blocks are unique but your IPv4 blocks are not, you cannot create the peering connection.
- You have a quota on the number of active and pending VPC peering connections that you can have per VPC. For more information, see Amazon VPC Quotas in the Amazon VPC User Guide
- VPC peering does not support transitive peering relationships. In a VPC peering connection, your VPC does not have access to any other VPCs with which the peer VPC may be peered. This includes VPC peering connections that are established entirely within your own AWS account. For more information about unsupported peering relationships, see - Unsupported VPC peering configurations. For examples of supported peering relationships, see VPC peering scenarios.
- You cannot have more than one VPC peering connection between the same two VPCs at the same time.
- Unicast reverse path forwarding in VPC peering connections is not supported. For more information, see Routing for response traffic.
- You can enable resources on either side of a VPC peering connection to communicate with each other over IPv6; however, IPv6 communication is not automatic. You must associate an IPv6 CIDR block with each VPC, enable the instances in the VPCs for IPv6 communication, and add routes to your route tables that route IPv6 traffic intended for the peer VPC to the VPC peering connection. For more information, see Your VPC and Subnets in the Amazon VPC User Guide.
- Any tags that you create for your VPC peering connection are only applied in the account or region in which you create them.
- If the IPv4 CIDR block of a VPC in a VPC peering connection falls outside of the private IPv4 address ranges specified by RFC 1918, private DNS hostnames for that VPC cannot be resolved to private IP addresses. To resolve private DNS hostnames to private - IP addresses, you can enable DNS resolution support for the VPC peering connection. For more information, see Enabling DNS resolution support for a VPC peering connection.
- You cannot connect to or query the Amazon DNS server in a peer VPC.


An inter-region VPC peering connection has additional limitations:

- You cannot create a security group rule that references a peer VPC security group.
- You cannot enable support for an EC2-Classic instance that's linked to a VPC via ClassicLink to communicate with the peer VPC.
- The Maximum Transmission Unit (MTU) across the VPC peering connection is 1500 bytes (jumbo frames are not supported).
- You must enable DNS resolution support for the VPC peering connection to resolve private DNS hostnames of the peered VPC to private IP addresses, even if the IPv4 CIDR for the VPC falls into the private IPv4 address ranges specified by RFC 1918.
- Inter-region peering in China is only allowed between the China (Beijing) Region, operated by SINNET and the China (Ningxia) Region, operated by NWCD.

### Unsupported VPC peering configurations

Overlapping CIDR blocks

You cannot create a VPC peering connection between VPCs with matching or overlapping IPv4 CIDR blocks.


**VPCs with matching IPv4 CIDR blocks**

If the VPCs have multiple IPv4 CIDR blocks, you cannot create a VPC peering connection if any of the CIDR blocks overlap (regardless of whether you intend to use the VPC peering connection for communication between the non-overlapping CIDR blocks only).


**VPCs with overlapping IPv4 CIDR blocks**

This limitation also applies to VPCs that have non-overlapping IPv6 CIDR blocks. Even if you intend to use the VPC peering connection for IPv6 communication only, you cannot create a VPC peering connection if the VPCs have matching or overlapping IPv4 CIDR blocks.

**Transitive peering**

Instead of using VPC peering, you can use an AWS Transit Gateway that acts as a network transit hub, to interconnect your VPCs and on-premises networks. For more information about transit gateways, see What is a Transit Gateway in Amazon VPC Transit Gateways.

You have a VPC peering connection between VPC A and VPC B (pcx-aaaabbbb), and between VPC A and VPC C (pcx-aaaacccc). There is no VPC peering connection between VPC B and VPC C. You cannot route packets directly from VPC B to VPC C through VPC A.

**Edge to edge routing through a gateway or private connection**

If either VPC in a peering relationship has one of the following connections, you cannot extend the peering relationship to that connection:

A VPN connection or an AWS Direct Connect connection to a corporate network
An internet connection through an internet gateway
An internet connection in a private subnet through a NAT device
A gateway VPC endpoint to an AWS service; for example, an endpoint to Amazon S3.
(IPv6) A ClassicLink connection. You can enable IPv4 communication between a linked EC2-Classic instance and instances in a VPC on the other side of a VPC peering connection. However, IPv6 is not supported in EC2-Classic, so you cannot extend this connection for IPv6 communication.

### AWS Transit gateways

A transit gateway is a network transit hub that you can use to interconnect your virtual private clouds (VPC) and on-premises networks. A transit gateway acts as a Regional virtual router for traffic flowing between your virtual private clouds (VPC) and VPN connections. A transit gateway scales elastically based on the volume of network traffic. Routing through a transit gateway operates at layer 3, where the packets are sent to a specific next-hop attachment, based on their destination IP addresses.

A transit gateway attachment is both a source and a destination of packets. You can attach the following resources to your transit gateway:

- One or more VPCs
- One or more VPN connections
- One or more AWS Direct Connect gateways
- One or more transit gateway peering connections
- If you attach a transit gateway peering connection, the transit gateway must be in a different Region.
