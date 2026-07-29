# AWS Network Connectivity Options – SAP-C02 Notes

> **Note:** This is a concise starter version based on the course outline you completed. Expand it as you continue studying.

# AWS Network Connectivity Services

## Amazon VPC
### Definition
Logically isolated virtual network in AWS.

### When to use
- Host AWS resources securely.
- Build public/private subnets.

### Cost
- No charge for VPC.
- Pay for resources (NAT Gateway, endpoints, data transfer).

---

## VPC Peering
### Definition
Private connection between two VPCs.

### When to use
- Small number of VPCs.
- Low-latency communication.

### Limitations
- No transitive routing.
- Mesh becomes complex.

### Cost
- No hourly charge.
- Cross-AZ/Region data transfer charges apply.

---

## AWS Transit Gateway
### Definition
Central hub connecting many VPCs and on-premises networks.

### When to use
- Large multi-account environments.
- Hub-and-spoke architecture.

### Cost
- Hourly per attachment.
- Data processing charges.

---

## AWS Direct Connect
### Definition
Dedicated private network connection from on-premises to AWS.

### When to use
- Consistent bandwidth.
- Lower latency.
- Enterprise hybrid cloud.

### Cost
- Port-hour charges.
- Data transfer.

---

## AWS Site-to-Site VPN
### Definition
Encrypted IPSec tunnel between on-premises and AWS.

### When to use
- Hybrid connectivity.
- Backup for Direct Connect.

### Cost
- Hourly VPN connection.
- Data transfer.

---

## AWS Client VPN
### Definition
Managed VPN for remote users.

### When to use
- Employees working remotely.

### Cost
- Endpoint hourly.
- Client connection hours.

---

## Gateway Endpoint
### Services
- Amazon S3
- DynamoDB

### Cost
No additional charge.

---

## Interface Endpoint (AWS PrivateLink)
### Definition
Private access to AWS services and SaaS using ENIs.

### Cost
- Hourly per endpoint/AZ.
- Data processed.

---

Decision Flowcharts

Example:


Need private access to AWS service?
            │
            ▼
Is it S3/DynamoDB?
      │
 Yes ─────────► Gateway Endpoint

 No
 │
 ▼
Interface Endpoint (PrivateLink)

Cost Summary

| Service            | Pricing                                       |
| ------------------ | --------------------------------------------- |
| VPC Peering        | No hourly charge; data transfer charges apply |
| Transit Gateway    | Hourly attachment + data processing charges   |
| Direct Connect     | Port hours + outbound data transfer           |
| Site-to-Site VPN   | Hourly per VPN connection + data transfer     |
| Client VPN         | Endpoint hourly + client connection hours     |
| Interface Endpoint | Hourly per AZ + data processed                |
| Gateway Endpoint   | No additional charge                          |


# Quick Comparison

| Service | Best Use |
|----------|----------|
| VPC Peering | Few VPCs |
| Transit Gateway | Hundreds of VPCs |
| Direct Connect | Dedicated enterprise connection |
| Site-to-Site VPN | Encrypted hybrid connection |
| Client VPN | Remote users |
| Gateway Endpoint | S3/DynamoDB private access |
| Interface Endpoint | Private AWS/SaaS access |


Cheat Sheet

Private AWS service access → PrivateLink
Connect two VPCs → VPC Peering
Connect hundreds of VPCs → Transit Gateway
Dedicated on-premises connection → Direct Connect
Encrypted backup connection → Site-to-Site VPN
Remote users → Client VPN
S3 privately from VPC → Gateway Endpoint
Private access to partner SaaS → PrivateLink

# SAP-C02 Cheat Sheet

- Two VPCs → VPC Peering
- Many VPCs → Transit Gateway
- Dedicated link → Direct Connect
- Encrypted tunnel → Site-to-Site VPN
- Remote employees → Client VPN
- S3 private access → Gateway Endpoint
- Private AWS service access → Interface Endpoint (PrivateLink)
