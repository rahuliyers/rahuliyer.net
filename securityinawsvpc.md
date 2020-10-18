# Security in AWS

## Security Groups

- A security group acts as a virtual firewall for your instance to control inbound and outbound traffic.

- When you launch an instance in a VPC, you can assign up to five security groups to the instance. Security groups act at the instance level, not the subnet level. Therefore, each instance in a subnet in your VPC can be assigned to a different set of security groups.

- If you launch an instance using the Amazon EC2 API or a command line tool and you don't specify a security group, the instance is automatically assigned to the default security group for the VPC. If you launch an instance using the Amazon EC2 console, you have an option to create a new security group for the instance.

- For each security group, you add rules that control the inbound traffic to instances, and a separate set of rules that control the outbound traffic.

- You can specify allow rules, but not deny rules.
- You can specify separate rules for inbound and outbound traffic.
- Security group rules enable you to filter traffic based on protocols and port numbers.
- Security groups are stateful — if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules. Responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.

- **When you create a new security group, it has no inbound rules. Therefore, no inbound traffic originating from another host to your instance is allowed until you add inbound rules to the security group.**

- **By default, a security group includes an outbound rule that allows all outbound traffic. You can remove the rule and add outbound rules that allow specific outbound traffic only. If your security group has no outbound rules, no outbound traffic originating from your instance is allowed.**

- There are quotas on the number of security groups that you can create per VPC, the number of rules that you can add to each security group, and the number of security groups that you can associate with a network interface. For more information, see Amazon VPC quotas.
- **Instances associated with a security group can't talk to each other unless you add rules allowing the traffic (exception: the default security group has these rules by default).**

- Security groups are associated with network interfaces. After you launch an instance, you can change the security groups that are associated with the instance, which changes the security groups associated with the primary network interface (eth0). You can also specify or change the security groups associated with any other network interface. By default, when you create a network interface, it's associated with the default security group for the VPC, unless you specify a different security group.

- A security group name cannot start with sg- as these indicate a default security group.

- **A security group can only be used in the VPC that you specify when you create the security group.**

Default rules of default security group:

**Inbound**

| Source | Protocol | Port Range | Description |
| --- | --- | --- | --- |
| The security group ID (sg-xxxxxxxx) | All  | All | Allow inbound traffic from network interfaces (and their associated instances that are assigned to the same security group) |

**Outbound**

| Destination | Protocol | Port Range | Description |
| --- | --- | --- | --- |
| 0.0.0.0/0 | All  | All | Allow all outbound IPv4 traffic. |
| ::/0 | All  | All | Allow all outbound IPv^ traffic. This rule is added by default if you create a VPC with an IPv6 CIDR block or if you associate an IPv6 CIDR block with your existing VPC.|


- **You can change the rules for the default security group.**

- **You can't delete a default security group. If you try to delete the default security group, you an error**

### An example security group rules:

Inbound


|Source|	Protocol|	Port range|	Description|
| --- | --- | --- | --- |
| 0.0.0.0/0 | TCP | 80 | Allow inbound HTTP access from all IPv4 addresses|
|::/0 |	TCP |	80 |	Allow inbound HTTP access from all IPv6 addresses|
|0.0.0.0/0 | TCP | 443 | Allow inbound HTTPS access from all IPv4 addresses|
|::/0|	TCP	|443	|Allow inbound HTTPS access from all IPv6 addresses|
|Your network's public IPv4 address range| TCP| 22|Allow inbound SSH access to Linux instances from IPv4 IP addresses in your network (over the internet gateway)|
|Your network's public IPv4 address range|TCP|3389|Allow inbound RDP access to Windows instances from IPv4 IP addresses in your network (over the internet gateway)|

Outbound

| Destination |	Protocol |Port range	| Description|
| --- | --- | --- | --- |
|The ID of the security group for your Microsoft SQL Server database servers|TCP|1433|Allow outbound Microsoft SQL Server access to instances in the specified security group|
|The ID of the security group for your MySQL database servers|TCP|3306|Allow outbound MySQL access to instances in the specified security group|

### VPC Peers and Security groups

If your VPC has a VPC peering connection with another VPC, a security group rule can reference another security group in the peer VPC. This allows instances that are associated with the referenced security group and those that are associated with the referencing security group to communicate with each other.

If the owner of the peer VPC deletes the referenced security group, or if you or the owner of the peer VPC deletes the VPC peering connection, the security group rule is marked as stale. You can delete stale security group rules as you would any other security group rule.

## Network Access Control Lists (NACLs)

Network ACL basics

The following are the basic things that you need to know about network ACLs:

- Your VPC automatically comes with a modifiable default network ACL. By default, it allows all inbound and outbound IPv4 traffic and, if applicable, IPv6 traffic.
- You can create a custom network ACL and associate it with a subnet. By default, each custom network ACL denies all inbound and outbound traffic until you add rules.
- Each subnet in your VPC must be associated with a network ACL. If you don't explicitly associate a subnet with a network ACL, the subnet is automatically associated with the default network ACL.
- You can associate a network ACL with multiple subnets. However, a subnet can be associated with only one network ACL at a time. When you associate a network ACL with a subnet, the previous association is removed.
- A network ACL contains a numbered list of rules. We evaluate the rules in order, starting with the lowest numbered rule, to determine whether traffic is allowed in or out of any subnet associated with the network ACL. The highest number that you can use for a rule is 32766. We recommend that you start by creating rules in increments (for example, increments of 10 or 100) so that you can insert new rules where you need to later on.
- A network ACL has separate inbound and outbound rules, and each rule can either allow or deny traffic.
- Network ACLs are stateless, which means that responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa).
- There are quotas (limits) for the number of network ACLs per VPC, and the number of rules per network ACL.


You can add or remove rules from the default network ACL, or create additional network ACLs for your VPC. When you add or remove rules from a network ACL, the changes are automatically applied to the subnets that it's associated with.

The following are the parts of a network ACL rule:

- **Rule number**. Rules are evaluated starting with the lowest numbered rule. As soon as a rule matches traffic, it's applied regardless of any higher-numbered rule that might contradict it.
- **Type.** The type of traffic; for example, SSH. You can also specify all traffic or a custom range.
- **Protocol.** You can specify any protocol that has a standard protocol number. For more information, see Protocol Numbers. If you specify ICMP as the protocol, you can specify any or all of the ICMP types and codes.
- **Port range.** The listening port or port range for the traffic. For example, 80 for HTTP traffic.
- **Source.** [Inbound rules only] The source of the traffic (CIDR range).
- **Destination.** [Outbound rules only] The destination for the traffic (CIDR range).
- **Allow/Deny.** Whether to allow or deny the specified traffic.

### Default network ACL

**The default network ACL is configured to allow all traffic to flow in and out of the subnets with which it is associated. Each network ACL also includes a rule whose rule number is an asterisk. This rule ensures that if a packet doesn't match any of the other numbered rules, it's denied. You can't modify or remove this rule.**

The following is an example default network ACL for a VPC that supports IPv4 only.

Inbound

| Rule #	| Type	| Protocol	| Port range	| Source	| Allow/Deny |
| --- | --- | --- | --- | --- | --- |
| 100 |All IPv4 traffic|All|All|0.0.0.0/0|ALLOW|
|*|All IPv4 traffic|All|All|0.0.0.0/0|DENY|

Outbound

| Rule #	| Type	| Protocol	| Port range	| Source	| Allow/Deny |
| --- | --- | --- | --- | --- | --- |
|100|All IPv4 traffic|All|All|0.0.0.0/0|ALLOW|
|*|All IPv4 traffic|All|All|0.0.0.0/0|DENY|

- **If you create a VPC with an IPv6 CIDR block or if you associate an IPv6 CIDR block with your existing VPC, we automatically add rules that allow all IPv6 traffic to flow in and out of your subnet. We also add rules whose rule numbers are an asterisk that ensures that a packet is denied if it doesn't match any of the other numbered rules. You can't modify or remove these rules. **
- **If you've modified your default network ACL's inbound rules, we do not automatically add an allow rule for inbound IPv6 traffic when you associate an IPv6 block with your VPC. Similarly, if you've modified the outbound rules, we do not automatically add an allow rule for outbound IPv6 traffic.**

**In practice, to cover the different types of clients that might initiate traffic to public-facing instances in your VPC, you can open ephemeral ports 1024-65535. However, you can also add rules to the ACL to deny traffic on any malicious ports within that range. Ensure that you place the deny rules earlier in the table than the allow rules that open the wide range of ephemeral ports.**


**If the maximum transmission unit (MTU) between hosts in your subnets is different, you must add the following network ACL rule, both inbound and outbound. This ensures that Path MTU Discovery can function correctly and prevent packet loss. Select Custom ICMP Rule for the type and Destination Unreachable, fragmentation required, and DF flag set for the port range (type 3, code 4). If you use traceroute, also add the following rule: select Custom ICMP Rule for the type and Time Exceeded, TTL expired transit for the port range (type 11, code 0). For more information, see Network maximum transmission unit (MTU) for your EC2 instance in the Amazon EC2 User Guide for Linux Instances.**
