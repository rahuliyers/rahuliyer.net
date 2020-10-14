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

VPCs may be no larger than a /16 block. In other words, they can have 216 – or 65,536 – total addresses.

AWS reserves the first four IPv4 addresses for their internal services, and the last address is also reserved.

When selecting a block for a VPC, AWS recommends using one of the private IPv4 ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)

| RFC 1918 range |	Example CIDR block |
| --- | --- |
| 10.0.0.0 - 10.255.255.255 (10/8 prefix) |	Your VPC must be /16 or smaller, for example, 10.0.0.0/16. |
| 172.16.0.0 - 172.31.255.255 (172.16/12 prefix) |	Your VPC must be /16 or smaller, for example, 172.31.0.0/16. |
| 192.168.0.0 - 192.168.255.255 (192.168/16 prefix) |	Your VPC can be smaller, for example 192.168.0.0/20. |
