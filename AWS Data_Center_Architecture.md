# Data Center Architecture

This file describes the architecture of a data center, from the physical building down to the virtual machines running applications. It also shows how AWS organizes the same ideas into Regions and Availability Zones.

---

## 1. Physical Layer: Data Center and Racks

A data center is a facility with power, cooling, networking, and physical security. Its servers are arranged in racks for organization, airflow, and cabling.

```
+---------------------------------------------------------------+
|                         Data center                           |
|                                                               |
|   Rack-1              Rack-2              Rack-3              |
|  +-----------+       +-----------+       +-----------+        |
|  | [Physical |       | [Physical |       | [Physical |        |
|  |  server]  |       |  server]  |       |  server]  |        |
|  |           |       |           |       |           |        |
|  | [Physical |       | [Physical |       | [Physical |        |
|  |  server]  |       |  server]  |       |  server]  |        |
|  +-----------+       +-----------+       +-----------+        |
+---------------------------------------------------------------+
```

**Design principle:** Spreading servers across racks limits the impact of a single rack failure, such as a power or top-of-rack switch problem.

---

## 2. Compute Layer: Hypervisor and Virtual Machines

Each physical server runs a hypervisor, which divides the server's CPU, memory, and storage into isolated virtual machines (VMs).

```
+-----------------------------------------------------+
|                  Physical server                    |
|                                                     |
|  +-----------------------------------------------+  |
|  |                  Hypervisor                   |  |
|  |                                               |  |
|  |   [ web ]          [ App ]                    |  |
|  |                                               |  |
|  |             [ DB ]                            |  |
|  +-----------------------------------------------+  |
+-----------------------------------------------------+
```

| Tier | Role                                  | Typical software            |
|------|---------------------------------------|-----------------------------|
| web  | Accepts user requests, serves static content | Nginx, Apache      |
| App  | Runs business logic                   | Node.js, Java, Python       |
| DB   | Stores and retrieves data             | MySQL, PostgreSQL           |

Separating the tiers into different VMs makes each one easier to scale, patch, and secure independently.

---

## 3. Cloud Layer: Region and Availability Zones (AWS)

AWS organizes its data centers into Regions and Availability Zones.

- **Region:** a geographic area, here `ap-south-1`.
- **Availability Zone (AZ):** an isolated data center, or group of data centers, with independent power and cooling, linked to other AZs in the same Region by low-latency networking.

```
+-----------------------------------------------------------------------+
|                         Region: ap-south-1                            |
|                                                                       |
|  +-----------------+   +-----------------+   +-----------------+      |
|  | AZ ap-south-1a  |   | AZ ap-south-1b  |   | AZ ap-south-1c  |      |
|  | (Data center)   |   | (Data center)   |   | (Data center)   |      |
|  |                 |   |                 |   |                 |      |
|  |  10.1.1.100     |   |  10.1.1.200     |   |  10.1.1.300     |      |
|  +--------+--------+   +--------+--------+   +--------+--------+      |
|           |                     |                     |               |
|           +---------------------+---------------------+               |
|                     Low-latency private network                       |
+-----------------------------------------------------------------------+
```

The IP addresses shown (`10.1.1.100`, `10.1.1.200`, `10.1.1.300`) are example private addresses for resources placed in each AZ.

**Design principle:** Deploy each tier across at least two AZs. If one AZ fails, the application keeps running in the others.

---

## 4. Full Stack, Top to Bottom

| Layer              | Component                    | Example                        | Who manages it (AWS) |
|--------------------|------------------------------|--------------------------------|----------------------|
| Region             | Geographic area              | `ap-south-1`                   | AWS                  |
| Availability Zone  | Isolated data center         | `ap-south-1a`                  | AWS                  |
| Rack               | Cabinet of servers           | Rack-1                         | AWS                  |
| Physical server    | Hardware host                | One server in Rack-1           | AWS                  |
| Hypervisor         | Virtualization software      | Nitro hypervisor on EC2        | AWS                  |
| Virtual machine    | EC2 instance                 | web, App, DB instances         | You                  |
| Application        | Your software                | Web app, API, database         | You                  |

---

## 5. Architecture Checklist

- [ ] Each tier (web, App, DB) runs in its own VM or managed service.
- [ ] Resources are spread across at least two Availability Zones.
- [ ] Each VM has a private IP address in a known subnet.
- [ ] Failure of one rack, server, or AZ does not stop the whole application.
- [ ] Backups of the DB tier are stored outside the AZ where the database runs.

---

## 6. Relation to Cloud Service Models

| Model | You manage                    | AWS manages                                    |
|-------|-------------------------------|------------------------------------------------|
| IaaS  | VMs, OS, apps, data           | Regions, AZs, racks, servers, hypervisors      |
| PaaS  | App code and data             | Everything below the runtime                   |
| SaaS  | Users and data entered        | The entire stack                               |
