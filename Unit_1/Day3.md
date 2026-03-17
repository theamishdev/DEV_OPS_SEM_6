## Namespaces and cgroups in Containers

---

## 1. Namespaces

### Definition
Namespaces are a feature of the Linux kernel that provide **isolation** for containers.  
They ensure that each container has its **own separate view of system resources**.

In simple terms, namespaces make a container feel like it is running on its **own independent system**, even though it shares the same OS.

---

### Types of Namespaces

| Namespace Type | Description |
|---------------|------------|
| PID Namespace | Isolates process IDs. Each container has its own process tree. |
| Network Namespace | Provides separate network interfaces, IP addresses, ports, and routing tables. |
| Mount Namespace | Isolates filesystem mount points. Each container has its own file system view. |
| IPC Namespace | Isolates inter-process communication mechanisms like message queues and shared memory. |
| UTS Namespace | Allows containers to have their own hostname and domain name. |
| User Namespace | Maps user and group IDs, allowing containers to run as root internally but not on host. |

---

### Key Points

- Provides **isolation between containers**
- Ensures **security and independence**
- Makes containers behave like **separate machines**
- Lightweight compared to virtual machines

---

## 2. cgroups (Control Groups)

### Definition
cgroups (control groups) are a Linux kernel feature used to **limit, control, and monitor resource usage** of processes or containers.

They ensure that a container does not consume more resources than allocated.

---

### Purpose of cgroups

- Limit resource usage
- Monitor resource consumption
- Ensure fair distribution of system resources
- Prevent one container from affecting others

---

### Types of Resources Controlled by cgroups

| Resource | Description |
|----------|------------|
| CPU | Limits CPU usage and scheduling priority |
| Memory | Restricts RAM usage and prevents memory overflow |
| Disk I/O | Controls read/write speed of storage devices |
| Network Bandwidth | Limits network usage of containers |
| Processes (PIDs) | Restr number of processes a container can create |

---

### Key Features

- Resource limiting (e.g., CPU %, memory limit)
- Resource accounting (monitor usage)
- Resource isolation
- Process grouping

---

## 3. Difference Between Namespaces and cgroups

| Feature | Namespaces | cgroups |
|--------|-----------|---------|
| Purpose | Isolation | Resource Management |
| Focus | Visibility of resources | Usage of resources |
| Example | Separate PID, network | Limit CPU, memory |
| Role | Makes containers independent | Prevents resource overuse |

---

## 4. Summary

- **Namespaces → Isolation (what a container can see)**
- **cgroups → Resource control (how much a container can use)**

Together, they form the **core foundation of containerization** in Linux.