# Enterprise Active Directory & Virtualized Network Lab

Built an enterprise AD lab on Proxmox VE with a pfSense firewall, Windows Server 2022 DC, and Windows 11 client. Separated IT, Finance, and Workstations into custom OUs instead of default containers to enforce targeted Group Policies, delegate control, and isolate departmental security.

---

## 🏗️ Lab Architecture & Hardware

This environment runs on a bare-metal hypervisor using isolated virtual bridges (`vmbr`) to simulate a segmented corporate network.

```text
                       [ Internet / WAN ]
                               |
                        ( Proxmox vmbr0 )
                               |
                    +---------------------+

                    |  pfSense Firewall   |
                    |  (Perimeter / NAT)  |
                    +---------------------+
                               |
                        ( Proxmox vmbr1 )
                       Internal LAN Subnet
                        /               \
                       /                 \
        +-----------------------+   +-----------------------+

        | Windows Server 2022   |   | Windows 11 Enterprise |
        |       (DC01)          |   |   (Client Endpoint)   |
        |  AD DS | DNS | GPO    |   |   Domain-Joined PC    |
        +-----------------------+   +-----------------------+
```

* **Proxmox VE (Hypervisor):** Type-1 hypervisor hosting all lab virtual machines and managing internal virtual network switches (`vmbr0` for WAN, `vmbr1` for isolated LAN).
* **pfSense (Perimeter Firewall & Router):** Manages WAN/LAN routing, NAT, DHCP relay, DNS forwarding, and perimeter firewall rules to inspect and control traffic.
* **Windows Server 2022 (`DC01`):** Primary Domain Controller hosting Active Directory Domain Services (AD DS), internal DNS resolution, and Group Policy Management.
* **Windows 11 Enterprise (Client):** Standard corporate workstation joined to the domain to test authentication, security baselines, and Group Policy deployments.

---

## 📂 Active Directory OU Structure

Instead of leaving accounts in the default `Users` and `Computers` containers, objects were organized into custom Organizational Units (OUs):

```text
domain.local
├── IT_Department        # System admins and helpdesk accounts
├── Finance_Department   # Finance staff accounts with restricted data access
└── Workstations         # Domain-joined Windows 11 client machines
```

### Why Custom OUs Were Implemented
1. **Group Policy Object (GPO) Enforcement:** Default AD containers (`Users` and `Computers`) cannot have GPOs linked directly to them. Custom OUs allow targeted policies per department or device type.
2. **Delegation of Control:** Administrative tasks (such as password resets or user creation) can be delegated to specific IT roles over a single OU without granting full Domain Admin rights.
3. **Security & Role Isolation:** Separating privileged IT accounts from standard business units (`Finance_Department`) reduces the attack surface and simplifies auditing.

---

## 🛡️ Configured Group Policies (GPOs)

---

## 📸 Screenshots & Verification

