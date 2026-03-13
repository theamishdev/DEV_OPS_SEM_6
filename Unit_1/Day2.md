# What is Virtual Machine

A Virtual Machine (VM) is a software-based computer that runs on top of physical hardware using a hypervisor.

Each VM contains:
- A full operating system
- Virtual hardware
- Applications
- Libraries and dependencies

 It is like a real physical pc.

 ## Architecture of VM

Physical Hardware
       ↓
Hypervisor (Virtualization Layer)
       ↓
---------------------------------
| VM1 | VM2 | VM3 |
---------------------------------

## HyperVisor

The Hypervisor is software that creates and manages VMs.
Example: 
+ VMware
+ VirtualBox
+ Hyper-V
+ KVM

| Advantages of Virtual Machines | Disadvantages of Virtual Machines |
|--------------------------------|-----------------------------------|
| Strong Isolation: Each VM is completely isolated from others. | Heavy Resource Usage: Each VM requires a full OS. |
| Run Multiple OSs: You can run Windows, Linux and macOS on the same physical machine. | Slow Startup: Booting a VM takes minutes. |
| Security: If one VM crashes, others remain unaffected. | Large Storage Requirement: VM images can be several GB. |

Popular Container platforms:
* Docker
* Podman
* containerd
