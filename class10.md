# Docker Networking, Subnetting & Core Networking Concepts

---

## 1. IP Address

An IP address is a **unique identifier** given to a device that communicates using the IP protocol.

**IPv4 details:**

* 32-bit number
* Written in dotted decimal format
* Each octet ranges from 0 to 255
* Example: `192.168.10.24`

In simple words, an IP address uniquely identifies a device on a network.

---

## 2. Network Interface Card (NIC)

A **NIC** is the hardware that connects a machine to a network.

**Functions:**

* Connects the system to the network
* Converts data into electrical or wireless signals
* Adds MAC addresses to Ethernet frames
* Sends and receives frames

**Types:**

* Wired (Ethernet)
* Wireless (Wi-Fi)

All network traffic from apps, containers, and VMs passes through the NIC.

---

## 3. Ethernet Cable

A physical cable used to connect networking devices.

**Common types:**

* Cat5e → Up to 1 Gbps
* Cat6 → Up to 10 Gbps (around 55 meters)
* Cat6a → 10 Gbps (100 meters)
* Cat7 / Cat8 → Datacenter use

These cables carry data as electrical signals.

---

## 4. Network Switch

A **Layer 2 device** that connects devices within a LAN.

**Features:**

* Forwards frames using MAC addresses
* Maintains a MAC address table
* Reduces unnecessary broadcast traffic

**How it works:**

1. A device sends a frame
2. Switch checks destination MAC
3. Looks up MAC table
4. Sends frame to correct port

---

## 5. ARP (Address Resolution Protocol)

ARP converts **IP addresses into MAC addresses** inside a local network.

**Why needed:**

* IP is logical
* Ethernet needs MAC addresses

**Process:**

1. Host asks: Who has `192.168.1.20`?
2. Target replies with its MAC
3. Mapping is stored in ARP cache

---

## 6. Default Gateway

The gateway is the router that sends traffic **outside the local subnet**.

Example:

* Host: `192.168.1.10/24`
* Destination: `8.8.8.8`
* Since destination is outside `/24`, traffic goes to gateway such as `192.168.1.1`.

---

## 7. NAT (Network Address Translation)

NAT allows private networks to access external networks.

### SNAT

* Used for outgoing traffic
* Replaces private source IP with public IP

### DNAT

* Used for incoming traffic
* Forwards public IP/port to private IP/port

---

## 8. Subnetting Basics

Subnetting splits a large network into smaller networks.

Example:

* Network: `192.168.10.0/24`
* Host bits: `32 - 24 = 8`
* Total IPs: `2^8 = 256`

`/24` represents the subnet mask.

---

## 9. Common Subnet Sizes

| Subnet | Total IPs |
| ------ | --------- |
| /24    | 256       |
| /25    | 128       |
| /26    | 64        |
| /27    | 32        |
| /28    | 16        |
| /29    | 8         |
| /30    | 4         |
| /31    | 2         |
| /32    | 1         |

---

## 10. Creating Multiple Subnets

If 3 networks are needed, the nearest power of two is **4**, so `/24` can be split into **four `/26` networks**.

Each `/26`:

* Host bits = 6
* Total IPs = 64

---

### Engineering Network

* Range: `192.168.10.0 – 192.168.10.63`
* Network ID: `192.168.10.0`
* Broadcast: `192.168.10.63`
* CIDR: `192.168.10.0/26`

---

### HR Network

* Range: `192.168.10.64 – 192.168.10.127`
* Network ID: `192.168.10.64`
* Broadcast: `192.168.10.127`
* CIDR: `192.168.10.64/26`

---

### Reception Network

* Range: `192.168.10.128 – 192.168.10.191`
* CIDR: `/26`

---

## 11. IPv4 Notes

* IPv4 has 4 octets
* Each octet ranges from 0–255

Examples:

* `/32` → 1 IP
* `/31` → 2 IPs
* `/30` → 4 IPs
* `/23` → 512 IPs

Example:

```
192.168.10.0/23
Range: 192.168.10.0 – 192.168.11.255
```

Subnets must always start on valid block boundaries.

---

## 12. Docker Networking Overview

Docker networking is based on:

* Linux network namespaces
* veth pairs
* Linux bridges
* iptables
* NAT

---

## 12.1 Network Namespaces

Each container has its own:

* Network interfaces
* Routing table
* ARP table
* Firewall rules

Containers behave like separate machines.

---

## 12.2 veth Pairs

A veth pair acts like a virtual cable:

* One end inside container (`eth0`)
* One end connected to a Linux bridge

---

## 12.3 docker0 Bridge

Default Docker bridge:

```
docker0: 172.17.0.1/16
```

Functions:

* Container-to-container communication
* Assigns private IPs
* Provides internet access using NAT

---

## 13. Docker Network Types

| Type    | Purpose               |
| ------- | --------------------- |
| bridge  | Default networking    |
| host    | Uses host network     |
| none    | No network            |
| overlay | Multi-host networking |
| macvlan | Appears on LAN        |

---

## 14. User-Defined Bridge Network

Create network:

```
docker network create mynet
```

Run containers:

```
docker run -d --network mynet --name app1 nginx
docker run -d --network mynet --name app2 busybox
```

Containers can reach each other using names.

---

## 15. Host Network Mode

```
docker run --network host nginx
```

Container uses host’s network directly.

---

## 16. None Network Mode

```
docker run --network none alpine
```

Container has no networking.

---

## 17. Overlay Network

Used for multi-node Docker setups.

```
docker network create -d overlay myoverlay
```

---

## 18. Internet Access from Containers

Docker uses iptables with a POSTROUTING MASQUERADE rule.

Flow:

```
container → docker0 → host interface → internet
```

---

## 19. Port Publishing

```
docker run -p 8080:80 nginx
```

Maps host port 8080 to container port 80 using DNAT.

---

## 20. Inspecting Docker Networking

```
docker network ls
docker network inspect bridge
docker inspect <container>
```

---

## 21. App and Database Example

```
docker network create backend
docker run -d --network backend --name db mysql
docker run -d --network backend --name app myapp
```

The app connects to the database using hostname `db`.
