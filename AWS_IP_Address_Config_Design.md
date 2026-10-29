# IP Addresses, Classes, VPC and Subnets

## 1. What is an IP address?

An IP address is a unique number that identifies a device on a network. IPv4 addresses are 32 bits long, written as four numbers (octets) from 0 to 255, separated by dots:

```
192.168.1.10
```

Each octet is 8 bits, so there are 2^32 (about 4.3 billion) possible IPv4 addresses.

An IP address has two parts:
- **Network part**: identifies which network the device belongs to.
- **Host part**: identifies the specific device inside that network.

A **subnet mask** or **CIDR prefix** (such as `/24`) tells you where the split is.

---

## 2. IP address classes (classful addressing)

The original (classful) system divided IPv4 space by the first octet.

| Class | First octet range | Leading bits | Default mask | Default prefix | Networks | Hosts per network | Use |
|-------|-------------------|--------------|--------------|----------------|----------|-------------------|-----|
| A | 1 – 126 | `0` | 255.0.0.0 | /8 | 126 | ~16.7 million | Very large networks |
| B | 128 – 191 | `10` | 255.255.0.0 | /16 | ~16,384 | ~65,534 | Medium networks |
| C | 192 – 223 | `110` | 255.255.255.0 | /24 | ~2 million | 254 | Small networks |
| D | 224 – 239 | `1110` | n/a | n/a | n/a | n/a | Multicast |
| E | 240 – 255 | `1111` | n/a | n/a | n/a | n/a | Reserved / experimental |

Special cases:
- `127.0.0.0/8` is loopback (`127.0.0.1` means "this machine").
- `0.0.0.0` means "any/unknown" or "default route".
- `255.255.255.255` is the limited broadcast address.

Classes are obsolete on the modern internet. They have been replaced by **CIDR** (Classless Inter-Domain Routing), where any prefix length from /0 to /32 can be used. You still see classes referenced in exams, documentation, and when people talk about "private ranges."

### Private IP ranges (RFC 1918)

These ranges are not routed on the public internet. They are used inside homes, offices, and cloud networks like VPCs.

| Range | CIDR | Size | Former class |
|-------|------|------|--------------|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | 16.7 million addresses | A |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | 1 million addresses | B |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | 65,536 addresses | C |

---

## 3. CIDR and calculating addresses

The number after the slash is the prefix length: how many bits are the network part.

Total addresses in a block = 2^(32 − prefix)

| CIDR | Subnet mask | Total addresses |
|------|-------------|-----------------|
| /8 | 255.0.0.0 | 16,777,216 |
| /16 | 255.255.0.0 | 65,536 |
| /20 | 255.255.240.0 | 4,096 |
| /24 | 255.255.255.0 | 256 |
| /26 | 255.255.255.192 | 64 |
| /28 | 255.255.255.240 | 16 |

Example: `192.168.1.0/24` covers `192.168.1.0` through `192.168.1.255`.

---

## 4. What is a VPC?

A **VPC (Virtual Private Cloud)** is your own isolated, private network inside a cloud provider such as AWS, Azure, or Google Cloud. You choose its IP range, create subnets in it, and control how traffic flows in and out.

Key parts of a VPC:

- **CIDR block**: the overall IP range for the VPC, for example `10.0.0.0/16`.
- **Subnets**: smaller slices of the VPC range, each placed in one availability zone.
- **Route tables**: rules that decide where traffic goes (for example, `0.0.0.0/0` to an internet gateway).
- **Internet gateway**: lets public subnets reach the internet.
- **NAT gateway**: lets private subnets reach the internet outbound without being reachable inbound.
- **Security groups and network ACLs**: firewall rules that allow or block traffic.

Public subnet: its route table sends internet traffic (`0.0.0.0/0`) to an internet gateway.
Private subnet: no direct route to the internet gateway. Outbound traffic goes through a NAT gateway.

---

## 5. How subnets divide up IP addresses

A subnet is a smaller block carved out of the VPC's CIDR block. The subnets must not overlap.

### Example

VPC CIDR: `10.0.0.0/16` (65,536 addresses, `10.0.0.0` to `10.0.255.255`)

Split into `/24` subnets (256 addresses each):

| Subnet | Address range | Usable hosts (AWS) |
|--------|---------------|--------------------|
| 10.0.1.0/24 | 10.0.1.0 – 10.0.1.255 | 251 |
| 10.0.2.0/24 | 10.0.2.0 – 10.0.2.255 | 251 |
| 10.0.3.0/24 | 10.0.3.0 – 10.0.3.255 | 251 |

Split into `/18` subnets (16,384 addresses each):

| Subnet | Address range |
|--------|---------------|
| 10.0.0.0/18 | 10.0.0.0 – 10.0.63.255 |
| 10.0.64.0/18 | 10.0.64.0 – 10.0.127.255 |
| 10.0.128.0/18 | 10.0.128.0 – 10.0.191.255 |
| 10.0.192.0/18 | 10.0.192.0 – 10.0.255.255 |

### Reserved addresses in AWS

AWS reserves 5 IP addresses in every subnet. For `10.0.1.0/24`:

| Address | Reserved for |
|---------|--------------|
| 10.0.1.0 | Network address |
| 10.0.1.1 | VPC router |
| 10.0.1.2 | DNS server (Amazon-provided) |
| 10.0.1.3 | Reserved by AWS for future use |
| 10.0.1.255 | Broadcast address (not supported, but reserved) |

So a `/24` gives 256 − 5 = **251 usable IPs**.

Usable hosts formula (AWS): 2^(32 − prefix) − 5

Other networks (on-premises, Azure, GCP) reserve different numbers. Azure reserves 5, Google Cloud reserves 4 in most cases, and plain networking typically reserves 2 (network and broadcast).

---

### VPC diagram

```
Internet
   |
   v
Internet gateway (IGW)
   |
   v
VPC 10.0.0.0/16
   |
   +-- Public subnet 10.0.1.0/24 (AZ-a)
   |     route table: 0.0.0.0/0 -> IGW
   |     EC2 instance, security group: web
   |     NAT gateway lives here
   |
   +-- Public subnet 10.0.2.0/24 (AZ-b)
   |     route table: 0.0.0.0/0 -> IGW
   |     EC2 instance, security group: web
   |
   +-- Private subnet 10.0.11.0/24 (AZ-a)
   |     route table: 0.0.0.0/0 -> NAT gateway
   |     EC2 instance, security group: db
   |
   +-- Private subnet 10.0.12.0/24 (AZ-b)
         route table: 0.0.0.0/0 -> NAT gateway
         EC2 instance, security group: db
```


## 6. Planning tips

1. Pick a VPC block big enough for growth, but within the private ranges. `10.0.0.0/16` is common.
2. Do not overlap ranges between VPCs you might connect (peering) or with your office network.
3. Spread subnets across multiple availability zones for high availability.
4. Leave room. Once a subnet is created, its size cannot be changed. You would need to create a new one.
5. Use a `/28` as the smallest practical subnet in AWS (11 usable IPs).

---

## Quick reference

- IPv4 = 32 bits, 4 octets
- Class A = /8, Class B = /16, Class C = /24 (classful, obsolete but still taught)
- Private: 10/8, 172.16/12, 192.168/16
- Addresses in a block = 2^(32 − prefix)
- AWS usable per subnet = 2^(32 − prefix) − 5