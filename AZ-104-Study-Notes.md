# AZ-104 Azure Administrator – Study Notes

These notes teach the AZ-104 topics from scratch. Each topic follows the same pattern:

1. **The idea**: what the thing is and why it exists, in plain words.
2. **How it works**: the facts and limits you need to know.
3. **🧭 Scenario guide**: "If you're asked about *this*, the answer is *that*", including the usual answer, the less common one, and the trap.
4. **🔎 Research**: where to read, watch and practise, before or after studying the section.

The exam has five domains. These notes follow them in order:

| Part | Domain | Exam weight (approx.) |
|---|---|---|
| 1 | Manage Azure identities and governance | 20–25% |
| 2 | Implement and manage storage | 15–20% |
| 3 | Deploy and manage Azure compute resources | 20–25% |
| 4 | Implement and manage virtual networking | 15–20% |
| 5 | Monitor and maintain Azure resources | 10–15% |

> **General research (use throughout)**
> - Official exam study guide (lists every skill measured): https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/az-104
> - Exam page and free Microsoft Learn training paths: https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator/
> - Official hands-on labs: https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/
> - John Savill's Technical Training (YouTube) – search **"AZ-104 Study Cram"** for a full-course overview video.
> - Tutorials Dojo cheat sheets index: https://tutorialsdojo.com/microsoft-azure-cheat-sheets/
> - Practise for real: a free Azure account lets you click through almost everything in these notes.

---

## Contents

- [Part 1 – Identities and Governance](#part-1--identities-and-governance)
- [Part 2 – Storage](#part-2--storage)
- [Part 3 – Compute](#part-3--compute)
- [Part 4 – Virtual Networking](#part-4--virtual-networking)
- [Part 5 – Monitor and Maintain](#part-5--monitor-and-maintain)
- [How to read AZ-104 questions](#how-to-read-az-104-questions)

---

# Part 1 – Identities and Governance

## 1.1 Microsoft Entra ID, tenants and custom domains

**The idea.** Microsoft Entra ID (formerly Azure AD) is Microsoft's cloud identity service: users, groups, devices, sign-in. A **tenant** is your organisation's own copy of Entra ID. Every tenant starts with a free domain like `contoso.onmicrosoft.com`. You can't delete or rename this initial domain, but you can add your real domain (`contoso.com`) so users sign in as `name@contoso.com`.

**How it works.**
- Adding a custom domain is a two-step process: **add** it in Entra ID, then **prove you own it** by creating a DNS record that Entra gives you.
- The proof record is a **TXT** record (most common) or an **MX** record.
- Once verified, you can make it the primary domain for new users.

**🧭 Scenario guide**
- If you're asked **which DNS record verifies a domain in Entra ID** ➜ **TXT** or **MX**. If only one is offered, pick that one.
- If the options are A, SOA, RRSIG or CNAME ➜ none of these prove ownership:
  - **A** maps a name to an IP.
  - **SOA** describes the zone itself.
  - **RRSIG** is a DNSSEC signature.
  - **CNAME** is an alias.
- Rarely, you'll see domain verification for **App Service** custom domains. That uses a **TXT** record named `asuid.<name>` plus a CNAME or A record, which is a different service with the same idea.

## 1.2 Licenses (Free, P1, P2)

**The idea.** Entra ID has editions. Free covers the basics. **P1** and **P2** unlock premium features. Buying licenses does nothing on its own: a user only gets premium features once a license is **assigned** to them.

**How it works.**

| Edition | Key features to remember |
|---|---|
| Free | Users, groups, basic SSO, security defaults |
| **P1** | **Conditional Access**, dynamic groups, group-based licensing, self-service password reset with on-prem writeback, hybrid features, custom device local admins |
| **P2** | Everything in P1 + **Privileged Identity Management (PIM)**, **Identity Protection** (risk-based policies), access reviews |

- Assign licenses in **Entra ID ➜ Billing ➜ Licenses ➜ All products ➜ Assign** (or from the user's Licenses page).
- **Group-based licensing**: assign the license to a group, and every member gets it automatically. This is the low-effort answer when many users are involved.
- A user needs a **usage location** set before a license can be assigned.
- Licenses belong to a tenant and can't be moved to another tenant.

**🧭 Scenario guide**
- If you're asked **"users must get P1/P2 features"** ➜ **assign the license** (Licenses blade, per user or per group).
  - For **many users with minimal effort** ➜ group-based licensing.
- If an option offers **directory roles**, **external collaboration settings** or **bulk create users** ➜ these don't give features:
  - Roles = admin permissions.
  - External collaboration = guest (B2B) access.
  - Bulk create = makes accounts only.
- If you're asked **which edition is needed for PIM or risk-based sign-in policies** ➜ **P2**. For Conditional Access alone ➜ **P1**.

## 1.3 Devices: join types and device settings

**The idea.** Entra ID tracks devices as well as users, so you can trust "a known company laptop" more than "an unknown phone".

**How it works – three ways a device relates to Entra ID:**

| Type | Typical device | Signs in with |
|---|---|---|
| **Entra registered** | Personal / BYOD phone or laptop | Personal account, plus a work account added |
| **Entra joined** | Company-owned Windows 10/11, cloud-only | Work (Entra) account |
| **Hybrid Entra joined** | Company PC joined to on-prem AD **and** registered in Entra | On-prem AD account (synced) |

**Device settings** live in **Entra ID ➜ Devices ➜ Device settings**:
- **Users may join devices to Microsoft Entra**: All / Selected (a group) / None.
- **Users may register their devices**.
- **Require MFA to register or join devices**. Microsoft now recommends doing this with a Conditional Access policy ("Register or join devices" user action) instead.
- **Maximum number of devices per user**.
- **Additional local administrators on all Microsoft Entra joined devices**. This needs Premium. By default, the Global Administrator role and the user who joined the device are local admins.

**🧭 Scenario guide**
- If you're asked **"add a local administrator for all joined devices"** ➜ **Devices ➜ Device settings ➜ Manage additional local administrators** (those users get the *Microsoft Entra Joined Device Local Administrator* role).
- If you're asked **"only members of group X can join devices"** ➜ Device settings ➜ *Users may join devices* = **Selected** ➜ that group.
- If you're asked **"users must verify with a phone/MFA when joining a device"** ➜ the Device settings MFA toggle, or a Conditional Access policy targeting *Register or join devices*.
- If an option mentions **OAuth 2.0 endpoints, app registrations or group naming policy** ➜ unrelated to devices (apps, apps and Microsoft 365 group names respectively).

## 1.4 Conditional Access

**The idea.** Conditional Access is an **if/then** engine that runs **after** the password step:

> **IF** (who + what app + where + what device + what risk) **THEN** (block, or allow only if extra requirements are met, or allow but limit the session).

It needs **P1** (risk-based conditions need **P2**).

**How it works.**
- **Assignments (the IF):** users/groups, target apps or user actions, conditions (locations / named or trusted IPs, device platforms, client apps, sign-in risk, user risk).
- **Access controls (the THEN)** come in two kinds, and the exam loves to test the difference:

| **Grant controls** – what you must satisfy to get in | **Session controls** – limits once you're in |
|---|---|
| Block access | App enforced restrictions |
| Require **MFA** / authentication strength | Conditional Access App Control (Defender for Cloud Apps) |
| Require **compliant device** (Intune) | **Sign-in frequency** |
| Require **Microsoft Entra hybrid joined device** | Persistent browser session |
| Require approved client app / app protection policy | Customize continuous access evaluation |
| Require password change / terms of use | |

- With several grant requirements you choose **"Require all"** or **"Require one"**.
- **Report-only mode** lets you test a policy without enforcing it.

**🧭 Scenario guide**
- If you're asked to **require MFA, a compliant device or a hybrid-joined device** ➜ **Grant control**.
- If you're asked to **force re-sign-in every X hours, or stop the browser staying signed in** ➜ **Session control**.
- In a Yes/No series where a solution says *"enforce session control"* for an MFA or device requirement ➜ **No**. If it says *"enforce grant control"* ➜ **Yes**.
- **"From untrusted locations"** ➜ condition: **Locations** (include Any, exclude trusted/named locations).
- If you're asked to **block legacy authentication** ➜ condition: client apps = legacy, grant = **Block**.

## 1.5 Azure RBAC – who can do what to Azure resources

**The idea.** Entra ID tells Azure **who you are**. **Azure role-based access control (RBAC)** decides **what you can do** to Azure resources (VMs, storage, networks). A **role assignment** = **who** (security principal) + **what** (role definition) + **where** (scope).

**How it works.**
- **Scopes** form a ladder, and permissions **inherit downward**: Management group ➜ Subscription ➜ Resource group ➜ Resource.
- You manage assignments in **Access control (IAM)** on the scope you want, for example *Subscriptions ➜ pick one ➜ Access control (IAM) ➜ Add role assignment*.
- **Core built-in roles:**

| Role | Can manage resources? | Can grant access to others? |
|---|---|---|
| **Owner** | ✅ everything | ✅ |
| **Contributor** | ✅ everything | ❌ |
| **Reader** | ❌ view only | ❌ |
| **User Access Administrator** | ❌ | ✅ (only manages access) |
| Role Based Access Control Administrator | ❌ | ✅ (assignments only, can be constrained) |

- **Service-specific roles** follow the same pattern: *Virtual Machine Contributor*, *Network Contributor*, *Storage Account Contributor*, and so on.
- **Control plane vs data plane:**
  - *Control plane* = managing the resource (create or delete a storage account, create a file share).
  - *Data plane* = touching the data inside (read a blob, open a file).
  - Roles with **"Data"** in the name (*Storage Blob Data Reader*, *Storage File Data SMB Share Reader/Contributor/Elevated Contributor*) are data-plane only. They **can't create or delete the resource itself**.
- **Entra roles ≠ Azure roles:**
  - *Entra roles* (Global Administrator, User Administrator, …) manage the **directory**: users, groups, licenses.
  - *Azure roles* (Owner, Contributor, …) manage **resources**.
  - A Global Admin has no resource access by default. They can temporarily "elevate access" to User Access Administrator at root scope.

**Custom roles.** When no built-in role fits, copy one and edit it:
1. `Get-AzRoleDefinition -Name "Contributor" | ConvertTo-Json | Out-File role.json` (`Get-AzRoleDefinition` = what a role *can do*; **`ConvertTo-Json`** turns it into a file you can edit).
2. Edit the file: new `Name`, `IsCustom: true`, remove `Id`, adjust `Actions` / `NotActions`, set `AssignableScopes`.
3. `New-AzRoleDefinition -InputFile role.json` (CLI: `az role definition create --role-definition role.json`).

Don't confuse it with:
- `Get-AzRoleAssignment` = *who has* roles (lists assignments), not the role's contents.
- `ConvertFrom-Json` = the opposite direction (JSON text ➜ PowerShell object).

**🧭 Scenario guide**
- If you're asked **"give user X rights to manage everything in the subscription, including access"** ➜ **Owner**, assigned in **Subscription ➜ Access control (IAM)**.
  - Manage everything but **not** access ➜ **Contributor**.
  - Only view ➜ **Reader**.
  - Only manage access ➜ **User Access Administrator**.
- If you're asked **"user must create network objects only"** ➜ **Network Contributor** at subscription or RG scope.
- If you're asked to **read a role-assignment JSON file**:
  - Owner/Contributor can deploy VMs and delete resources.
  - Reader can't change anything.
  - A *Data* role can't provision the resource (e.g. *SMB Share Reader* can't create file shares).
- If you're asked **which command gets a role's JSON to customise** ➜ `Get-AzRoleDefinition … | ConvertTo-Json`.
- If you're asked **who can enable Traffic Analytics** ➜ **Owner, Contributor or Network Contributor** (subscription scope). So a Contributor assignment ➜ **Yes**.
- If the option says to do it in **Entra group settings, OAuth endpoints or Exchange distribution groups** ➜ wrong place. Resource permissions are always **IAM**.

## 1.6 Hybrid identity (Entra Connect) – the bit case studies ask about

**The idea.** Many companies already have on-premises Active Directory. **Microsoft Entra Connect** (or Cloud Sync) copies those users into Entra ID so they have one identity everywhere.

**How it works – sign-in methods:**

| Method | Password hash stored in cloud? | Notes |
|---|---|---|
| **Password hash sync (PHS)** | Yes (a hash of the hash) | Simplest; works if on-prem is down |
| **Pass-through authentication (PTA)** | **No** | A lightweight agent checks the password against on-prem AD |
| **Federation (AD FS)** | **No** | Most complex; for special needs (smart cards, third-party MFA) |

**🧭 Scenario guide**
- If you're asked **"prevent storing passwords or hashes in Azure" + "minimize admin effort"** ➜ **Pass-through authentication**. Use **federation** only if a special need is mentioned.
- If you're asked **"simplest, works even if on-prem is down"** ➜ **Password hash sync**.

### 🔎 Research – Part 1
- **Read (Microsoft Learn):**
  - Add a custom domain: https://learn.microsoft.com/en-us/entra/fundamentals/add-custom-domain
  - Assign licenses: https://learn.microsoft.com/en-us/entra/fundamentals/license-users-groups
  - Device local admins: https://learn.microsoft.com/en-us/entra/identity/devices/assign-local-admin
  - Conditional Access grant controls: https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-grant
  - Conditional Access session controls: https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-session
  - Azure RBAC overview: https://learn.microsoft.com/en-us/azure/role-based-access-control/overview
  - Built-in roles list: https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles
  - Custom roles with PowerShell: https://learn.microsoft.com/en-us/azure/role-based-access-control/custom-roles-powershell
- **Microsoft Learn path:** search "AZ-104: Manage identities and governance in Azure".
- **Watch:** John Savill – search "Azure RBAC deep dive" and "Conditional Access deep dive".
- **Cheat sheets:** https://tutorialsdojo.com/microsoft-entra-id/ · https://tutorialsdojo.com/azure-role-based-access-control-rbac/ · https://tutorialsdojo.com/microsoft-entra-id-vs-role-based-access-control-rbac/
- **Hands-on:**
  - Add a custom domain to a test tenant and look at the TXT value it asks for.
  - Assign Reader to a test user and try to delete something.
  - Run `Get-AzRoleDefinition Contributor | ConvertTo-Json` in Cloud Shell.

---

# Part 2 – Storage

## 2.1 Storage accounts – kinds and performance

**The idea.** A storage account is a container (with a globally unique name) for four data services: **Blobs** (objects/files), **Files** (SMB/NFS shares), **Queues** (messages) and **Tables** (NoSQL key-value). The account's **kind** and **performance** decide which features and redundancy options you get.

**How it works.**

| Kind | Performance | Use | Redundancy options |
|---|---|---|---|
| **General-purpose v2 (GPv2)** | Standard | Default for almost everything | LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS |
| GPv2 | Premium (page blobs) | VM unmanaged disks (legacy) | LRS |
| **BlockBlobStorage** | Premium | Low-latency block blobs | LRS, ZRS |
| **FileStorage** | Premium | High-performance file shares | LRS, ZRS |
| *GPv1* (legacy) | Standard/Premium | Old accounts | LRS, GRS, RA-GRS – **no ZRS** |
| *BlobStorage* (legacy) | Standard | Old blob-only accounts | LRS, GRS, RA-GRS – **no ZRS** |

- Legacy accounts can be **upgraded in place to GPv2** (one-way, no downtime).

## 2.2 Redundancy and changing it

**The idea.** Azure always keeps **3 copies** of your data in the primary region. Redundancy choices decide **where** those copies live and whether there's a second region.

**How it works.**

| Option | Copies in primary region | Second region? | Survives |
|---|---|---|---|
| **LRS** | 3 in **one datacenter** | No | Disk/rack failure |
| **ZRS** | 3 across **3 availability zones** | No | Datacenter (zone) failure |
| **GRS** | LRS in primary | Yes, async to the **paired region** | Region failure (after failover) |
| **GZRS** | ZRS in primary | Yes, to the paired region | Zone **and** region failure |
| **RA-GRS / RA-GZRS** | as above | Yes + **read access** to the secondary at any time | Same, plus readable secondary |

- **The secondary region is always the region's fixed pair** (e.g. Southeast Asia ↔ East Asia, East US ↔ West US). **You can't choose it.**
- Changing redundancy:
  - **Adding or removing the geo part, or read access** (LRS ↔ GRS, GRS ↔ RA-GRS) is a simple **setting change**.
  - **Changing the primary-region part** (LRS ↔ ZRS, GRS ↔ GZRS) is a **conversion** (live migration / in-place conversion) or a manual copy to a new account.
- **In-place conversion to ZRS rules (exam version):**
  - The account must be a kind that supports ZRS (GPv2, BlockBlobStorage, FileStorage). GPv1 and BlobStorage don't.
  - Convert **from LRS or GRS**. If it's **RA-GRS**, first switch to GRS or LRS to drop read access, then convert.
  - To reach GZRS from LRS: switch to GRS first, then convert.
- **Archive tier** only works with **LRS, GRS, RA-GRS** (not ZRS/GZRS).

**🧭 Scenario guide**
- If you're asked **which account can be converted to ZRS in place** ➜ the **GPv2 (or premium block/file) account on LRS or GRS**.
  - GPv1 and BlobStorage ➜ never (upgrade to GPv2 first).
  - RA-GRS ➜ not directly (remove read access first).
- If you're asked to **survive a datacenter failure with the data staying in one region** ➜ **ZRS**.
  - Survive a **region** failure ➜ **GRS/GZRS**.
  - Also **read** from the secondary any time ➜ **RA-** versions.
- If you're asked to **copy data to a specific region that isn't the pair** ➜ GRS can't do it ➜ **object replication** (2.4).

## 2.3 Access tiers and lifecycle management

**The idea.** You pay less to *store* cold data but more to *read* it. Tiers let you match cost to how often data is used. Lifecycle management moves data between tiers **automatically**.

**How it works.**

| Tier | Online? | Minimum stay | Best for |
|---|---|---|---|
| **Hot** | Yes | – | Frequently accessed |
| **Cool** | Yes | 30 days | Infrequent access, still instant |
| **Cold** | Yes | 90 days | Rare access, still instant |
| **Archive** | **No (offline)** | 180 days | Long-term retention; **rehydration takes hours** (up to ~15 h standard; high priority is faster) |

- **The account's default access tier** (applied to new blobs) is **Hot or Cool** on the exam. It's **never Archive**. Archive is set **per blob**.
- **Lifecycle management policy** (storage account ➜ *Lifecycle management*): JSON rules with filters (prefix, blob index tags) and actions:
  - `tierToCool`, `tierToCold`, `tierToArchive`, `delete`.
  - Conditions: *days after creation / last modification / last access* (last access needs **access tracking** enabled).
  - Rules run about once a day.

**🧭 Scenario guide**
- If you're asked to **"move data to Archive after N days" with minimal effort** ➜ **lifecycle management rule**. An Azure Function, Logic App, script or manual Copy Blob also works but is more effort, so it's wrong when the question says "minimize administrative effort".
- If the data is **"infrequently accessed" but must be read instantly** ➜ set the **default tier to Cool**, not Hot (Hot costs more to store) and not Archive (offline).
- Typical two-answer combo ➜ **Cool default tier + lifecycle rule to Archive**.
- If an option says **"set the default tier to Archive"** or **"archive on upload"** when instant access is required ➜ wrong.
- If you're asked to **delete blobs after N days** ➜ lifecycle `delete` action.

## 2.4 Data protection – soft delete, versioning, object replication

**The idea.** Redundancy protects against *hardware* failure. It doesn't protect against **someone deleting or overwriting data**. These features do.

**How it works.**

| Feature | Protects against | Notes |
|---|---|---|
| **Blob soft delete** | Deleted blobs | Retention **1–365 days** |
| **Container soft delete** | Deleted containers | Retention 1–365 days |
| **Blob versioning** | Overwrites & deletes | Keeps previous versions automatically |
| **Point-in-time restore** | Bulk mistakes | Needs versioning + change feed + soft delete |
| **File share soft delete** | Deleted Azure file shares | |
| **Object replication** | Need copies elsewhere | **Async copy of block blobs** to another account, **any region you choose** |

- Object replication requires **versioning on both** accounts and **change feed on the source**. It's **block blobs only** (no append/page blobs) and works on GPv2 / premium block blob accounts.
- In the portal, soft delete and versioning live under **Data management ➜ Data protection**.

**🧭 Scenario guide**
- If you're asked to **recover deleted data for N days** ➜ **Data protection ➜ blob (and container) soft delete = N days**.
- If you're asked to **recover a previous version after an overwrite** ➜ **versioning**.
- If you're asked to **duplicate uploads into a specific region** (e.g. Southeast Asia ➜ Australia Central) ➜ **object replication**.
  - Versioning is a *prerequisite*, not the answer.
  - GRS can't pick the region.
- Rarely: **"copy to another account in the same region for analytics"** ➜ also object replication.

## 2.5 Securing access – keys, SAS, stored access policies, firewall

**The idea.** There are several ways to let someone into storage, from "master key" to "narrow, time-limited pass".

**How it works.**
- **Account access keys** (two, so you can rotate) = full access to everything. Avoid handing them out.
- **Shared Access Signature (SAS)** = a URL token with specific permissions, services, resources, IP range and expiry:
  - **User delegation SAS**: signed with Entra credentials, blob only, **most secure**.
  - **Service SAS**: one service (blob/file/queue/table).
  - **Account SAS**: one or more services.
- **Stored access policy** = a named policy on a container, share, queue or table that a service SAS can point to:
  - **Maximum 5 per container/share/queue/table.**
  - **Revoke** a SAS by deleting or changing its policy. An ad-hoc SAS (no policy) can only be killed by **rotating the key** that signed it.
- **Storage firewall (Networking blade):** "Enabled from selected virtual networks and IP addresses":
  - Add **VNet subnets** (via service endpoints) and **public IP addresses/ranges** (no private ranges).
  - Exceptions such as "Allow trusted Microsoft services".
  - **Private endpoints** give the account a private IP inside your VNet.
- **Entra ID + RBAC** for data access (Storage Blob Data … roles) is the modern preferred method.

**🧭 Scenario guide**
- If you're asked **"only allow access from one public IP"** ➜ **Networking (firewall)** ➜ selected networks ➜ add that IP.
  - **Only from a VNet** ➜ firewall + service endpoint, or a **private endpoint**.
- If you're asked **"temporary, secure access for partners"** ➜ **SAS** (with a **stored access policy** if you need to revoke it later).
- If you're asked **the max number of stored access policies** ➜ **5**.
- If you're asked to **revoke a leaked ad-hoc SAS** ➜ **regenerate the account key** that signed it.

## 2.6 Azure Files and identity-based access

**The idea.** Azure Files gives you real **SMB (and NFS) file shares** in the cloud. Windows, Linux and macOS can **map them as a drive**, like an on-prem file server. Blob storage can't be mapped as a drive. Only Files can.

**How it works.**
- SMB uses **port 445**, which must be open outbound (many ISPs block it).
- Authentication options:
  1. **Storage account key** (full access, like a superuser).
  2. **Identity-based:** on-prem **AD DS**, **Microsoft Entra Domain Services**, or **Microsoft Entra Kerberos** (for hybrid identities).
- Two layers of permissions:
  - **Share-level** = Azure RBAC roles: *Storage File Data SMB Share Reader / Contributor / Elevated Contributor*. This decides whether you can get into the share at all.
  - **Directory/file-level** = normal **Windows ACLs (NTFS permissions)**, set with File Explorer or `icacls`.

**Enabling on-prem AD DS authentication – the order matters:**
1. **Sync on-prem AD to Entra ID with Microsoft Entra Connect.** Users must be **hybrid identities** that exist in both.
2. **Enable AD DS authentication on the storage account.** This registers the account in AD DS (like a computer account) with the `AzFilesHybrid` PowerShell module.
3. **Assign share-level permissions** (RBAC) **and directory/file-level permissions** (ACLs).
4. **Mount the share** using AD credentials.

**🧭 Scenario guide**
- If you're asked **"domain-joined machines must mount the share using AD DS credentials"** ➜ the 4 steps above, **in that order**. Identity has to exist before auth can be enabled, auth before permissions, and permissions before mounting.
- If you're asked to **map a drive letter to Blob storage** ➜ not possible. Use Azure Files, or Storage Explorer / AzCopy for blobs.
- If there's **no on-prem AD and you want domain services in the cloud** ➜ **Entra Domain Services**.

## 2.7 Azure File Sync

**The idea.** Keep your on-prem Windows file server, but make an **Azure file share the central copy**. The server becomes a fast local **cache**, and many servers in many offices can sync the same share. **Cloud tiering** keeps hot files local and leaves cold files in Azure as pointers.

**How it works – the parts:**

| Part | What it is | Rule |
|---|---|---|
| **Storage Sync Service** | Top-level Azure resource | A server can register with **only one** |
| **Sync group** | Defines what syncs with what | Exactly **one cloud endpoint** |
| **Cloud endpoint** | The Azure file share | One per sync group |
| **Server endpoint** | A folder/volume path on a registered server | Many per group, **but only one per server in the same sync group** |
| **Azure File Sync agent** | Software installed on the Windows Server | Needed before registration |

**Setup order (memorise: Agent ➜ Register ➜ Cloud ➜ Server):**
0. Create the Storage Sync Service and the Azure file share (and, for older guidance, disable IE Enhanced Security Configuration on the server for registration).
1. **Install the Azure File Sync agent** on the server.
2. **Register the server** with the Storage Sync Service.
3. **Create a sync group and its cloud endpoint** (the file share).
4. **Add a server endpoint** (a folder on the registered server).

**🧭 Scenario guide**
- If you're asked for **the order of steps** ➜ **Agent, Register, Sync group + cloud endpoint, Server endpoint**. Each step needs the previous one to exist.
- If you're asked **"can I add a second Azure file share to the same sync group?"** ➜ **No**. One cloud endpoint per group; create another sync group.
- If you're asked **"can I add another folder from the *same* server to the same sync group?"** ➜ **No** (one server endpoint per server per group). A **different server** ➜ **Yes**.
- If you're asked **"can one server sync with two Storage Sync Services?"** ➜ **No**.
- If you're asked to **save local disk space but keep everything available** ➜ **cloud tiering**.

## 2.8 Moving data into Azure Storage

| Tool | When |
|---|---|
| **Azure Storage Explorer** | GUI app (Windows/macOS/Linux); upload/download/manage blobs, files, queues, tables **over the Internet** |
| **AzCopy** | Command line; fast, scriptable, `azcopy sync`; over the Internet |
| Azure portal upload | Small, one-off files |
| **Azure Import/Export** | **Ship your own disks** to an Azure datacenter (Blob & Files) |
| **Azure Data Box** family | Microsoft ships you a device: Data Box Disk (small, tens of TB), Data Box (~100 TB class), Data Box Heavy (~1 PB). Offline, huge volumes, slow links |
| **Azure File Sync** | Ongoing sync of file servers |
| Data Box Gateway / Azure Stack Edge | Online appliance for continuous transfer |

**🧭 Scenario guide**
- If you're asked **"transfer files to Blob over the Internet, minimal effort"** ➜ **Storage Explorer** (GUI) or **AzCopy** (scriptable).
- If you're asked about **lots of TB with limited bandwidth / no Internet transfer** ➜ **Data Box** (Microsoft's device) or **Import/Export** (your own disks).
- If an option says to **map a drive to a Blob container** ➜ wrong (drive mapping = Azure Files only).

### 🔎 Research – Part 2
- **Read (Microsoft Learn):**
  - Redundancy: https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy
  - Changing redundancy: https://learn.microsoft.com/en-us/azure/storage/common/redundancy-migration
  - Access tiers: https://learn.microsoft.com/en-us/azure/storage/blobs/access-tiers-overview
  - Lifecycle management: https://learn.microsoft.com/en-us/azure/storage/blobs/lifecycle-management-overview
  - Soft delete for blobs: https://learn.microsoft.com/en-us/azure/storage/blobs/soft-delete-blob-overview
  - Object replication: https://learn.microsoft.com/en-us/azure/storage/blobs/object-replication-overview
  - Stored access policies: https://learn.microsoft.com/en-us/rest/api/storageservices/define-stored-access-policy
  - Storage firewall: https://learn.microsoft.com/en-us/azure/storage/common/storage-network-security
  - Azure Files AD DS auth: https://learn.microsoft.com/en-us/azure/storage/files/storage-files-identity-auth-active-directory-enable
  - File Sync deployment: https://learn.microsoft.com/en-us/azure/storage/file-sync/file-sync-deployment-guide
  - AzCopy: https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10
- **Microsoft Learn path:** search "AZ-104: Implement and manage storage in Azure".
- **Watch:** John Savill – search "Azure Storage deep dive", "Azure Files deep dive", "Azure File Sync".
- **Cheat sheets:** https://tutorialsdojo.com/azure-storage-overview/ · https://tutorialsdojo.com/azure-file-storage/ · https://tutorialsdojo.com/azure-blob-storage/ · https://tutorialsdojo.com/locally-redundant-storage-lrs-vs-zone-redundant-storage-zrs/ · https://tutorialsdojo.com/azure-blob-vs-disk-vs-file-storage/
- **Hands-on:**
  - Create a GPv2 account, open *Redundancy* and see which options are offered.
  - Write a lifecycle rule in the portal and view its JSON.
  - Enable soft delete, delete a blob, then undelete it.
  - Generate a SAS and open it in a browser.
  - Install Storage Explorer.

---

# Part 3 – Compute

## 3.1 Keeping VMs available: availability sets, zones, regions

**The idea.** Things fail at different sizes: a server, a rack, a whole datacenter, a whole region. Each Azure feature protects against a specific size of failure. Learn the ladder:

| Failure / event | Protection | SLA (VMs) |
|---|---|---|
| Planned maintenance reboots | **Update domains** (availability set) | |
| Rack / power / network-switch failure | **Fault domains** (availability set) | 99.95% |
| **Whole datacenter** fails | **Availability zones** (physically separate datacenters in one region, own power/cooling/network, at least 3 per enabled region) | 99.99% |
| **Whole region** fails | Second region: **Azure Site Recovery**, paired regions, GRS | |

**How availability sets work.**
- **Fault domain (FD)** = a group of hardware sharing power and network (think "rack"). Max **3** (2 in some regions).
- **Update domain (UD)** = a group rebooted **together** during **planned** platform maintenance. Up to **20** (default 5). **Only one UD is updated at a time**, with 30 minutes to recover before the next.
- VMs are spread round-robin across FDs and UDs. A VM can only join a set **when it's created**.
- An availability set is **inside one datacenter**, so it can't survive that datacenter going down.
- A VM uses **either** an availability set **or** a zone, never both.

**🧭 Scenario guide**
- If you're asked **"keep VMs running if a datacenter becomes unavailable"** ➜ **one VM in each Availability Zone**. Never "all in one zone" and never "an availability set" (sets live in one datacenter).
- If you're asked **"keep ≥2 VMs available during planned maintenance"** ➜ more **update domains** (e.g. **3 UDs**, with **2+ FDs** for hardware safety). Pick **UD 3 / FD 2** over UD 3 / FD 1 (no hardware protection) and over UD 2 / FD 3 (fewer UDs, and FDs don't help planned maintenance).
- If you're asked **"protect against a rack/hardware failure, cheapest, single datacenter OK"** ➜ **availability set** (FDs).
- If you're asked **"protect against a region outage"** ➜ **Azure Site Recovery** to another region (Part 5.5).
- Memory hook: **Update = Updates (planned). Fault = Failures (hardware).**

## 3.2 Proximity placement groups (PPG)

**The idea.** Sometimes you want VMs **physically close** together for very low network latency (trading apps, HPC, SAP). A PPG asks Azure to put them in the same datacenter.

**How it works.**
- A PPG is created in a **region**. VMs, scale sets and availability sets that use it **must be in the same region**.
- The **resource group's location doesn't matter**. Only the resources' own regions do.
- With zones, a PPG effectively pins you to one zone. There's a trade-off between low latency and resilience.

**🧭 Scenario guide**
- If you're asked **which PPG a VM/VMSS can use** ➜ only PPGs in the **same region as the VM/VMSS**. Ignore which resource group anything is in.
- If you're asked about **lowest latency between VMs** ➜ PPG (and accelerated networking).

## 3.3 VM states, IP addresses, redeploy and reapply

**The idea.** "Stopped" means two different things in Azure, and it changes what you pay for and whether you keep your IP.

**How it works.**

| State | How you get there | Billed for compute? | Dynamic public IP |
|---|---|---|---|
| Running | Start | Yes | Assigned |
| **Stopped** (allocated) | Shut down from **inside the OS** | **Yes** | Kept |
| **Stopped (deallocated)** | **Stop** in the portal / CLI | **No** | **Released** (a new one on next start) |

- **Static public IPs** are kept until you delete them. **Standard SKU public IPs are always static.** Basic SKU is retired.
- **Redeploy** = move the VM to a **new host** (fixes host or connectivity problems). The **temporary disk is lost**.
- **Reapply** = re-run the VM's provisioning to fix a **"Failed" provisioning state**.
- **Resize** may need the VM deallocated if the new size isn't available on the current hardware cluster.

**🧭 Scenario guide**
- If you're asked **"can't RDP; NSG allows 3389; VM has no public IP shown"** ➜ the VM is **deallocated** ➜ **Start it**. Redeploy is extra effort for a different problem. The NSG rule is fine if it's top priority and allows 3389.
- If you're asked **"the IP must never change"** ➜ use a **static** public IP.
- If you're asked **"VM stuck in Failed state"** ➜ **Reapply**. **"Can't connect, suspected host issue"** ➜ **Redeploy**.
- If you're asked **"stop paying for compute"** ➜ **Stop (deallocate)**. Shutting down inside the OS still bills.

## 3.4 Disks: managed vs unmanaged and disk types

**The idea.** A VM's disks are page blobs. **Managed disks** = Azure manages the storage account for you. **Unmanaged** = you manage the storage accounts yourself (legacy).

**How it works.**
- **Managed disks are required** for availability zones, for moving VMs into zones with Site Recovery, and for most modern features. Converting unmanaged ➜ managed: deallocate, then convert (one-way).
- Disk types:
  - Standard HDD (cheapest, dev/test)
  - Standard SSD
  - Premium SSD (production)
  - Premium SSD v2
  - **Ultra Disk** (highest IOPS; data disks only; needs "Ultra Disk compatibility" enabled and a supporting region/zone)

**🧭 Scenario guide**
- If you're asked **"VM must be movable into Availability Zones with Site Recovery"** and the VM shows *Managed disks: Disabled* ➜ change **Managed disks**. The disk type (HDD/SSD) doesn't matter. Ultra Disk is for extreme performance, not resilience.
- If you're asked about **highest IOPS / lowest latency for a database** ➜ **Ultra Disk** (or Premium SSD v2).

## 3.5 vCPU quotas

**The idea.** Each subscription has vCPU limits **per region** so nobody accidentally deploys thousands of cores.

**How it works.**
- Two limits must **both** be satisfied:
  1. **Total regional vCPUs**.
  2. **VM-family vCPUs** (e.g. Standard DSv3 family, Av2 family).
- Usage counts the cores of **both running and stopped-deallocated VMs** (Microsoft's docs: *"based on the total number of cores in use, both allocated and deallocated"*). Only **deleting** a VM frees its quota.
- Quotas are per region: a VM in South Central US doesn't use North Central US quota.
- Raise limits with a **quota increase request** (Subscriptions ➜ Usage + quotas, or the Quotas page).

**Worked method.** Write down the regional limit, add up existing VM cores in that region (running **and** deallocated), then add each new VM **in the order given** and check both limits each time.

> Example: regional limit 15. Existing VMs: 4 cores running + 8 cores deallocated = **12 used**.
> - +2-core VM ➜ 14 ✅
> - another +2 ➜ 16 ❌
> - +8 ➜ 22 ❌

**🧭 Scenario guide**
- If you're asked **"can VM X be created in region Y"** ➜ do the sum above. **Deallocated VMs still count.** VMs in other regions don't.
- If both limits are hit ➜ request an increase or **delete** unused VMs (deallocating isn't enough).

## 3.6 Scale sets and VM extensions

**The idea.** A **virtual machine scale set (VMSS)** is a group of identical, load-balanced VMs that can **autoscale**. **Extensions** are small agents that run tasks inside VMs after deployment, for example installing software.

**How it works.**
- **Custom Script Extension** downloads a script (from Storage/GitHub) and runs it: install IIS, web components, configure the app.
- In an ARM template for a VMSS, extensions go in **`virtualMachineProfile.extensionProfile`**.
- Other options: **DSC extension** (desired state), **cloud-init** (Linux, first boot), **custom images** (bake everything in).
- After changing the scale set model, instances update according to the **upgrade policy** (Manual / Automatic / Rolling).
- Autoscale rules: metric-based (CPU > 70% for 10 min ➜ +1) or schedule-based. Always set min, max and default instance counts.

**🧭 Scenario guide**
- If you're asked **"automatically install web components on scale set VMs"** ➜ **create a configuration script** + **add the Custom Script Extension in the template's `extensionProfile`**.
- If an option says **"create a new scale set"**, **"automation account"** or **"VPN client package"** ➜ these don't install software on the instances.
- If you're asked about a **pre-baked golden image** ➜ custom image / Azure Compute Gallery.

## 3.7 Infrastructure as code: ARM templates and Bicep

**The idea.** Describe your infrastructure in a file and Azure builds it the same way every time. **ARM templates** are JSON. **Bicep** is a cleaner language that compiles to ARM.

**How it works.**
- Template sections: `parameters`, `variables`, `resources`, `outputs`.
- **Export template:** in the portal, any **resource group** (or single resource) ➜ *Export template* gives you the ARM JSON of what exists now. This is great for reproducing an environment.
- Deployment **scopes**:
  - resource group (`az deployment group create`)
  - subscription (`az deployment sub create`)
  - management group
  - tenant
- Bicep keywords:
  - **`targetScope`** = the **kind** of scope the whole file deploys to (`'resourceGroup'` is the default, or `'subscription'`, …).
  - **`scope`** = the **exact** target for a module or resource, e.g. `scope: resourceGroup('WebAppRG')`.
  - `location` = Azure region.
  - `tags` = labels.
- Deployment modes: **Incremental** (default; adds or updates, leaves extras) vs **Complete** (deletes anything not in the template).

**🧭 Scenario guide**
- If you're asked **"modify the Bicep file so it deploys into resource group X"** ➜ **`scope`**. `targetScope` only says "a resource group" in general, not which one. `location` is the region.
- If you're asked to **capture the current state of all resources to automate future deployments** ➜ **Export template** from the resource group. Capturing a VM image covers one VM only. Redeploy/reapply are fixes, not captures.
- If you're asked **"remove resources not defined in the template"** ➜ **Complete mode**.

## 3.8 App Service and App Service plans

**The idea.** App Service hosts web apps without managing VMs. An **App Service plan** is the set of VMs (compute) your apps run on. **You pay for the plan**, and every app in the plan shares it.

**How it works.**

| Tier | Highlights |
|---|---|
| Free / Shared | Shared infrastructure, dev/test, no scale out |
| Basic | Dedicated VMs, manual scale (up to 3), custom domains |
| **Standard** | **Autoscale**, **deployment slots** (5), backups |
| Premium v3 | More instances & slots, better hardware |
| Isolated (App Service Environment) | Dedicated, network-isolated |

- A plan belongs to **one region** and one OS type. Apps in the same region can share a plan.
- **Deployment slots** (staging ➜ swap to production) and **autoscale** need **Standard or higher**.

**🧭 Scenario guide**
- If you're asked to **deploy N web apps in the same region at the lowest cost** ➜ **one App Service plan**. Use more plans only for **different regions**, different OS, or isolation or performance needs.
- If you're asked about **staging slots / swap** or **autoscale** ➜ at least **Standard**.
- If an option says **Application Gateway** or a **CDN endpoint** to *host* apps ➜ these don't host code. App Gateway is a load balancer, and a CDN caches content.

## 3.9 Azure Container Apps networking (and other container options)

**The idea.** Container Apps runs containers serverlessly. Apps live in an **environment**, which can use your own VNet.

**How it works.**
- Environment types and **minimum subnet size** (the subnet must be **dedicated** to the environment):
  - **Workload profiles** (supports UDRs, NAT Gateway egress) ➜ **/27**.
  - **Consumption-only** (legacy; no UDR / NAT GW) ➜ **/23**.
- The subnet can be in any VNet in the same region that has free address space.
- Other container services:
  - **Azure Container Instances (ACI)**: single containers, quick, no orchestration.
  - **AKS**: full Kubernetes.
  - **App Service for containers**: web apps.
  - **Azure Container Registry (ACR)**: stores images.

**🧭 Scenario guide**
- If you're asked for **the smallest subnet for a workload-profiles environment** ➜ **/27**. If /27 isn't offered, pick the next smallest that is **larger** (e.g. /26).
  - Consumption-only ➜ **/23**.
- If you're asked about **run one container quickly, no orchestration** ➜ **ACI**. **Full Kubernetes control** ➜ **AKS**.

### 🔎 Research – Part 3
- **Read (Microsoft Learn):**
  - VM availability options: https://learn.microsoft.com/en-us/azure/virtual-machines/availability
  - Maintenance and update domains: https://learn.microsoft.com/en-us/azure/virtual-machines/maintenance-and-updates
  - VM states & billing: https://learn.microsoft.com/en-us/azure/virtual-machines/states-billing
  - Proximity placement groups: https://learn.microsoft.com/en-us/azure/virtual-machines/co-location
  - vCPU quotas: https://learn.microsoft.com/en-us/azure/virtual-machines/quotas
  - Install apps in a scale set with a template: https://learn.microsoft.com/en-us/azure/virtual-machine-scale-sets/tutorial-install-apps-template
  - Export templates: https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/export-template-portal
  - Bicep scope functions: https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/bicep-functions-scope
  - App Service plans: https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans
  - Container Apps networking: https://learn.microsoft.com/en-us/azure/container-apps/networking
  - Moving VMs into zones with ASR: https://learn.microsoft.com/en-us/azure/site-recovery/move-azure-vms-avset-azone
- **Microsoft Learn path:** search "AZ-104: Deploy and manage Azure compute resources".
- **Watch:** John Savill – search "Azure VM availability sets vs zones", "Azure Bicep", "Azure App Service deep dive".
- **Cheat sheets:** https://tutorialsdojo.com/azure-virtual-machines/ · https://tutorialsdojo.com/azure-app-service/ · https://tutorialsdojo.com/azure-resource-manager-arm/
- **Hands-on:**
  - Create an availability set and look at the FD/UD fields.
  - Stop (deallocate) a VM with a dynamic IP and start it again, then compare the IPs.
  - Export a resource group template.
  - Open *Usage + quotas* in your subscription.

---

# Part 4 – Virtual Networking

## 4.1 VNets, subnets and IP maths

**The idea.** A **virtual network (VNet)** is your private network in Azure, in **one region**. It's split into **subnets**. Resources in the same VNet can talk to each other by default.

**How it works.**
- Address space uses CIDR notation. **Azure reserves 5 IPs in every subnet** (first 4 + last).

| CIDR | Total IPs | Usable in Azure |
|---|---|---|
| /29 (smallest subnet) | 8 | 3 |
| /28 | 16 | 11 |
| /27 | 32 | 27 |
| /26 | 64 | 59 |
| /24 | 256 | 251 |
| /23 | 512 | 507 |

- Special subnets and their usual minimum sizes:
  - `GatewaySubnet` (VPN/ExpressRoute) ➜ **/27** recommended.
  - `AzureBastionSubnet` ➜ **/26**.
  - `AzureFirewallSubnet` ➜ **/26**.
- VNets that you want to connect (peer/VPN) **must not have overlapping address spaces**.

## 4.2 Network security groups (NSGs)

**The idea.** An NSG is a list of **allow/deny rules** (a basic firewall) for traffic in and out of subnets or network interfaces.

**How it works.**
- Each rule has: **priority** (100–4096, **lower number wins and is checked first**), source, destination, port, protocol, allow/deny. Processing stops at the first match.
- **Default rules** (can't be deleted; override them with lower numbers):
  - Inbound: **65000 AllowVnetInBound**, **65001 AllowAzureLoadBalancerInBound**, **65500 DenyAllInBound**.
  - Outbound: 65000 AllowVnetOutBound, 65001 AllowInternetOutBound, 65500 DenyAllOutBound.
- You can associate an NSG with **subnets and/or NICs**. **One NSG can be reused on many subnets/NICs** in the same region. If both a subnet NSG and a NIC NSG exist, traffic must pass **both**.
- Rules can target specific IPs, ranges, **service tags** (Internet, VirtualNetwork, Storage…) or **application security groups**.
- Troubleshoot with **Effective security rules** and Network Watcher's **IP flow verify**.

**🧭 Scenario guide**
- If you're asked for **the minimum number of NSGs** ➜ usually **1**. Associate it with all the subnets and use rules with specific destinations (e.g. HTTPS to subnet 2 and 3's ranges, RDP to one VM's IP).
  - Traffic inside the VNet is already allowed (65000).
  - Everything else inbound is already denied (65500).
  - Use more than one NSG only if the subnets are in **different regions** or the question demands it.
- If you're asked **"RDP doesn't work but the rule allows 3389 at priority 300"** ➜ the NSG isn't the problem. Check the VM state or public IP (3.3).
- If you're asked **"why is traffic blocked even though an allow rule exists"** ➜ look for a **lower-numbered deny**, or a deny in the *other* NSG (subnet vs NIC).

## 4.3 Public IPs and load balancers

**The idea.** A **public IP** makes something reachable from the Internet. A **load balancer** spreads traffic across several VMs (the **backend pool**) and checks their health with **probes**.

**How it works.**
- **Public IP SKUs:**
  - **Standard**: static, secure by default (closed until an NSG allows traffic), zone-redundant.
  - **Basic**: retired September 2025, but still appears in exam questions.
- **Load balancer SKUs:**
  - **Standard**: production, zones, up to 1000 instances, needs NSGs.
  - **Basic**: retired.
- **The SKU-matching rule:** VMs in a **Standard** LB backend pool must have **Standard public IPs or no public IP at all**. A VM with a **Basic** public IP can't be added.
- VMs **can be stopped** and still be added to a backend pool.
- Backend pool members must be in the same VNet.
- **Public LB** = Internet-facing frontend. **Internal LB** = private frontend.
- Which load-balancing service?

| Service | Layer | Scope | Use |
|---|---|---|---|
| **Load Balancer** | 4 (TCP/UDP) | Regional | Any protocol, VMs |
| **Application Gateway** | 7 (HTTP/S) | Regional | URL/path routing, **WAF**, SSL termination |
| **Front Door** | 7 | Global | Global web apps, CDN + WAF |
| **Traffic Manager** | DNS | Global | DNS-based routing (priority, performance, geographic) |

**🧭 Scenario guide**
- If you're asked **"can these VMs join a Standard LB backend pool?"** ➜ VMs with **no PIP** ✅, **Standard PIP** ✅, **Basic PIP** ❌. Whether the VM is stopped doesn't matter.
- If you're asked **how to fix the Basic-PIP VM** ➜ **remove its public IP** (✅) or **upgrade it to Standard** (✅). Adding another **Basic** PIP to a VM ➜ ❌ (creates a mismatch).
- If you're asked about **path-based routing or a web application firewall** ➜ **Application Gateway**. **Global HTTP + WAF** ➜ **Front Door**. **DNS-based failover between regions** ➜ **Traffic Manager**.

## 4.4 VNet peering

**The idea.** Peering connects two VNets so they act like one network over Microsoft's backbone. Use **regional peering** for the same region and **global peering** across regions.

**How it works.**
- A peering is **two links**, one created on each VNet. Status:
  - **Initiated** = only one side exists.
  - **Connected** = both sides exist.
  - **Disconnected** = one side was **deleted**. No traffic flows.
- **Peering isn't transitive.** If A↔B and B↔C, A **can't** reach C unless you peer A↔C, or route through a hub NVA/firewall with "allow forwarded traffic".
- **Gateway transit:** a spoke can use the hub's VPN gateway. Set **"Allow gateway transit"** on the **hub** side and **"Use remote gateways"** on the **spoke** side.
- Address spaces can't overlap. Changing a peered VNet's address space requires **syncing** the peering, and older guidance says delete the peering first.

**🧭 Scenario guide**
- If you're asked about **a peering in the Disconnected state** ➜ VMs can reach **only their own VNet** across that link. **The first step to fix it is to delete the disconnected peering, then recreate it on both sides.** Enabling gateway transit, changing subnets or address space don't fix it.
- If you're asked **"A peers with B, B peers with C, can A reach C?"** ➜ **No** (non-transitive).
- If you're asked **"spoke VNet must use the hub's VPN to reach on-prem"** ➜ **Allow gateway transit** (hub) + **Use remote gateways** (spoke).

## 4.5 VPN Gateway, ExpressRoute and hybrid connectivity

**The idea.** Connect on-prem networks or individual laptops to Azure VNets.

**How it works.**
- **Site-to-site (S2S)** = office network ↔ VNet over IPsec. Needs:
  1. A **VPN gateway** in the VNet's **GatewaySubnet**.
  2. A **local network gateway** (represents your on-prem device: its public IP and on-prem address ranges).
  3. A **connection** (links the two, with a shared key).
  4. The on-prem VPN device configured.
- **Point-to-site (P2S)** = one computer ↔ VNet.
  - Authentication: Azure certificates, Microsoft Entra ID (OpenVPN) or RADIUS.
  - The client installs a **VPN client configuration package**. That package contains the routes. **If the network topology changes (new peering, new address space), download and reinstall the package** so the client learns the new routes.
- **Gateway types:** **route-based** (the normal choice: supports P2S, BGP, ExpressRoute coexistence) vs **policy-based** (legacy, limited).
- **SKUs:**
  - **Basic** (legacy): no BGP, limited P2S, **can't coexist with ExpressRoute**.
  - **VpnGw1–5**: production, VpnGw1 is the cheapest. The *AZ* variants are zone-redundant.
- **ExpressRoute** = a **private** circuit through a connectivity provider. It doesn't use the Internet. A **S2S VPN can be the backup** path (coexistence), which needs a route-based gateway of at least **VpnGw1**.
- **Virtual WAN** = Microsoft-managed hub-and-spoke for **many** branches and any-to-any connectivity. It's big and complex.

**🧭 Scenario guide**
- If you're asked **what to configure for a S2S VPN** ➜ **VPN gateway + local network gateway + connection** (plus GatewaySubnet if missing).
- If it's a **failover for ExpressRoute, cost-effective** ➜ VPN gateway **VpnGw1** (not Basic) + local network gateway + connection. **Virtual WAN / Virtual Hub** ➜ overkill.
- If you're asked **"P2S client reaches VNet1 but not newly peered VNet2"** ➜ **re-download and reinstall the VPN client package**. Gateway transit is already working if on-prem reaches VNet2. Restarting the gateway is for broken S2S tunnels.
- If you're asked about **many branch offices with any-to-any connectivity at global scale** ➜ **Virtual WAN**.

## 4.6 Azure Bastion

**The idea.** Bastion lets you RDP/SSH into VMs **from the Azure portal over HTTPS**, so VMs don't need public IPs or open RDP/SSH ports to the Internet.

**How it works.**
- Deployed into a subnet named **AzureBastionSubnet** (/26 or larger) in a VNet.
- **Always connects to the VM's private IP.**
- Reaches VMs in **its own VNet and in peered VNets** (including globally peered), so one Bastion can serve a hub-and-spoke.
- SKUs:
  - Developer: free, basic.
  - Basic.
  - **Standard**: native client, IP-based connection, shareable links, scaling.
  - Premium: session recording, private-only.

**🧭 Scenario guide**
- If you're asked **"can Bastion connect to VM1 using its private IP?"** (same VNet) ➜ **Yes**.
- **"…only using its public IP?"** ➜ **No** (Bastion uses private IPs; no public IP needed).
- **"…to a VM in another VNet?"** ➜ **Yes only if the VNets are peered**. Otherwise **No**.
- If you're asked about **secure admin access without public IPs or open 3389/22** ➜ **Bastion**. Just-in-time VM access is an alternative from Defender for Cloud.

## 4.7 Azure DNS

**The idea.** Azure DNS hosts your DNS zones on Azure's name servers. You can't *buy* domains with Azure DNS. You buy them from a registrar (or App Service Domains) and then host them in Azure.

**How it works.**
- **Record types:**

| Record | What it does |
|---|---|
| A / AAAA | Name ➜ IPv4 / IPv6 address |
| CNAME | Name ➜ another name (alias); **not allowed at the zone apex** |
| **Alias record** (Azure) | Points to an Azure resource (public IP, Traffic Manager, Front Door); works at the apex |
| MX | Mail servers (also used for domain verification) |
| TXT | Text (domain verification, SPF) |
| **NS** | **Delegates** a (sub)domain to name servers |
| SOA | Zone authority info (auto-created, one per zone) |
| PTR | Reverse lookup: IP ➜ name |
| SRV | Service location |

- **Delegating a subdomain** (e.g. `portal.contoso.com` to its own zone):
  1. Create the child zone `portal.contoso.com`.
  2. Copy its **4 name servers**.
  3. In the parent zone, add an **NS record named `portal`** with those servers.
- **Zone file import/export** (moving a zone from another DNS server, BIND format) ➜ **Azure CLI** (`az network dns zone import`) and the **Azure portal**. **Not PowerShell.** Cloud Shell just runs the CLI.
- **Private DNS zones** = name resolution inside VNets. Link the zone to VNets, and optionally **auto-register** VM names (a VNet can auto-register into only one private zone).

**🧭 Scenario guide**
- If you're asked to **delegate a subdomain** ➜ **NS record** in the parent zone. PTR, CNAME and TXT don't delegate.
- If you're asked to **import an on-prem zone with minimal effort** ➜ **Azure CLI and Azure portal**. (Older material says CLI only. If only one is asked for, choose **CLI**.)
- If you're asked to **point the root domain (`contoso.com`) at an Azure resource** ➜ **alias record** (CNAME can't sit at the apex).
- If you're asked about **VMs resolving each other by name across VNets** ➜ **private DNS zone** linked to the VNets with auto-registration.

### 🔎 Research – Part 4
- **Read (Microsoft Learn):**
  - VNet overview: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview
  - How NSGs work: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-group-how-it-works
  - Public IP addresses: https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses
  - Load balancer SKUs: https://learn.microsoft.com/en-us/azure/load-balancer/skus
  - VNet peering: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview
  - VPN Gateway: https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways
  - Point-to-site: https://learn.microsoft.com/en-us/azure/vpn-gateway/point-to-site-about
  - ExpressRoute + VPN coexistence: https://learn.microsoft.com/en-us/azure/expressroute/expressroute-howto-coexist-resource-manager
  - Bastion: https://learn.microsoft.com/en-us/azure/bastion/bastion-overview
  - Bastion with peering: https://learn.microsoft.com/en-us/azure/bastion/vnet-peering
  - DNS subdomain delegation: https://learn.microsoft.com/en-us/azure/dns/delegate-subdomain
  - DNS zone import/export: https://learn.microsoft.com/en-us/azure/dns/dns-import-export
- **Microsoft Learn path:** search "AZ-104: Configure and manage virtual networks for Azure administrators".
- **Watch:** John Savill – search "Azure networking deep dive", "Azure VNet peering", "Azure Load Balancer vs Application Gateway vs Front Door", "Azure DNS".
- **Cheat sheets:** https://tutorialsdojo.com/azure-virtual-network-vnet/ · https://tutorialsdojo.com/azure-vpn-gateway/ · https://tutorialsdojo.com/azure-load-balancer/ · https://tutorialsdojo.com/azure-dns/
- **Hands-on:**
  - Peer two VNets, delete one side and watch the status change to *Disconnected*.
  - Create an NSG with a deny rule and test it with *IP flow verify*.
  - Deploy Bastion (Developer SKU) and connect to a VM with no public IP.
  - Create a child DNS zone and delegate it with an NS record.
  - Subnet practice: work out the usable IPs for /26, /27 and /28 by hand.

---

# Part 5 – Monitor and Maintain

## 5.1 Choosing the right monitoring or management tool

**The idea.** Azure has many "watch and advise" tools with similar names. The exam mainly tests whether you can pick the right one.

| Tool | What it answers |
|---|---|
| **Azure Monitor – Metrics** | Numbers over time (CPU %, requests); near real-time |
| **Azure Monitor – Logs (Log Analytics)** | Detailed records you query with **KQL** |
| **Activity log** | **Who did what** to resources (create/delete/change); control plane; 90 days |
| **Alerts + action groups** | Notify (email/SMS/push) or act (webhook, Logic App, runbook) when a condition is met |
| **Diagnostic settings** | Send a resource's logs/metrics to Log Analytics, Storage or Event Hubs |
| **Service Health** | Problems with **Azure itself**: incidents, planned maintenance, advisories in your regions |
| **Resource Health** | Is **this specific resource** healthy right now? |
| **Azure Advisor** | **Recommendations**: Cost, Security, Reliability, Operational excellence, Performance |
| **Cost Management** | **Where the money went** (Cost analysis), budgets, alerts, exports; also shows Advisor cost recommendations |
| **Application Insights** | App performance monitoring: requests, exceptions, dependencies |
| **Workbooks** | Interactive reports/dashboards over existing data |

**🧭 Scenario guide**
- If you're asked to **find unattached disks, idle VMs or right-sizing to cut costs** ➜ **Azure Advisor (Cost recommendations)**. You can open it directly or via **Cost Management ➜ Advisor recommendations**.
  - **Only for certain subscriptions/resource groups** ➜ edit the **Advisor configuration** to include just those scopes.
  - **Cost Analysis** shows spending, not idle resources.
  - Billing roles don't manage resources.
  - Custom Monitor queries are more effort.
- If you're asked **"admin must get email when Azure has an outage"** ➜ **Service Health alert** + action group (email). Owner role ≠ notifications.
- If you're asked **"who deleted this VM?"** ➜ **Activity log**.
- If you're asked **"alert when CPU > 80%"** ➜ **metric alert** + action group.
- If you're asked **"query logs across many VMs"** ➜ **Log Analytics** workspace + KQL.

## 5.2 Network Watcher and Traffic Analytics

**The idea.** **Network Watcher** is the network troubleshooting toolbox, enabled per region automatically.

**How it works – tools:**
- **IP flow verify** (is traffic allowed or denied, and by which NSG rule?)
- **NSG diagnostics** and **Effective security rules**
- **Next hop** (where does a packet go?)
- **Connection troubleshoot** and **Connection monitor**
- **Packet capture** (record traffic on a VM)
- **VPN troubleshoot**
- **Topology**
- **Flow logs**: NSG flow logs are being retired in favour of **VNet flow logs**.
- **Traffic Analytics** = analyses flow logs in a **Log Analytics workspace** to show traffic patterns, hot spots and threats.

**Requirements to remember:**
- **Packet capture** and **connection troubleshoot** need the **Network Watcher Agent VM extension** installed on the VM.
- **Traffic Analytics** needs:
  - Network Watcher enabled
  - Flow logs on
  - A storage account for the raw logs
  - A Log Analytics workspace
  - Role: **Owner, Contributor or Network Contributor**

**🧭 Scenario guide**
- If you're asked about **the first step for packet capture on a VM** ➜ **install the Network Watcher Agent VM extension**.
  - Not the Azure Monitor agent (OS/app telemetry).
  - Not the Performance Diagnostics agent (OS performance and boot issues).
  - Not Defender for Servers (security).
- If you're asked **"why can't VM A reach VM B on port 443?"** ➜ **IP flow verify** (which rule blocks it) or **Connection troubleshoot**.
- If you're asked **"visualise traffic distribution across the network"** ➜ **Traffic Analytics**.

## 5.3 App Service diagnostics

**The idea.** When a web app throws errors, developers need the **raw details** of what happened.

**How it works – App Service logs** (App Service ➜ *App Service logs*):
- **Application logging**: your code's trace output (filesystem or blob).
- **Web server logging**: raw HTTP request logs in **W3C format** (method, URL, client IP, **status code** such as 500).
- **Detailed error messages**: the HTML error pages for HTTP 400+ responses.
- **Failed request tracing**: deep traces of failed requests.
- **Log stream**: see the logs **live**.
- Also useful: **Diagnose and solve problems** (built-in troubleshooters) and **Application Insights** (exceptions, performance).

**🧭 Scenario guide**
- If you're asked **"developers need real-time, detailed visibility into HTTP 500 errors"** ➜ **turn on web server logging** (and detailed errors), then watch the **log stream**.
  - An **alert rule** only notifies; it doesn't capture the detail.
  - A **Service Health alert** = Azure platform problems, not your app.
  - A **workbook** = visualisation of data you already collect.
- If you're asked about **exceptions with stack traces and performance over time** ➜ **Application Insights**.

## 5.4 Azure Backup

**The idea.** Backup keeps **copies of data over time** so you can go back to yesterday or last week. This is different from replication (5.5), which keeps a **live copy elsewhere** for disaster recovery.

**How it works – two kinds of vault:**

| Vault | Backs up |
|---|---|
| **Recovery Services vault** | **Azure VMs**, **Azure Files shares**, SQL Server and SAP HANA **in Azure VMs**, on-prem files/folders (**MARS agent**), MABS/DPM servers; also used by Site Recovery |
| **Backup vault** | **Azure Blobs**, Azure Disks, Azure Database for PostgreSQL, AKS, … |

- **The vault must be in the same region as the resources it protects** (for Azure Files, the storage account's region).
- **Order to back up VMs:**
  1. **Create a Recovery Services vault**.
  2. **Create or choose a backup policy** (schedule + retention; *Standard* or *Enhanced*, where Enhanced allows multiple backups a day and is needed for Trusted Launch VMs).
  3. **Enable backup** on the VMs.
- Azure VM backup **doesn't need you to install an agent**. It uses a VM extension automatically. The **MARS agent** is only for file/folder backup (on-prem, or inside a VM).
- Set the vault's **storage redundancy** (GRS default, LRS, ZRS) **before** the first backup. Optional **Cross Region Restore**.
- **Soft delete** keeps deleted backup data for 14 days by default.
- **Restore options for a VM:**
  - **Create a new VM**.
  - **Restore disks** (then build a VM yourself).
  - **Replace existing** disks (the original VM must still exist).
  - **Cross Region Restore** (if enabled).
  - You **can't** restore VM A's backup *onto* a different existing VM B.
- **File recovery (item-level):** pick a recovery point ➜ download a **script** ➜ run it on a machine to **mount the recovery point as drives** ➜ copy the files. Run the script on a machine with the **same OS version (or its matching client OS)**. You can't restore files from Windows Server 2016 onto 2012. On the exam, treat "different server OS version" as **not compatible**.
- **Backup reports:** send the vault's diagnostics to a **Log Analytics workspace**. The workspace can be in **any region and any subscription**. Unlike the vault itself, it doesn't have to match.

**🧭 Scenario guide**
- If you're asked **"what do you create first to back up VMs?"** ➜ **Recovery Services vault**, then a policy.
  - **MABS** is for on-prem and needs a vault too.
  - A **Recovery Plan** belongs to Site Recovery, not Backup.
- If you're asked **which vault can back up which resource** ➜ check **(1) same region** and **(2) supported type**. A Recovery Services vault ➜ VMs and **file shares** in its region. **Blob containers ➜ Backup vault**, never a Recovery Services vault.
- If you're asked **"where can you run file recovery for VM X"** ➜ **only machines with a compatible OS**, usually the VM itself or one with the same OS version.
- If you're asked **"where can you restore VM X"** ➜ **as VM X** (replace disks), as a **new VM**, or as disks. You can't restore onto another existing VM.
- If you're asked **which Log Analytics workspace can store Backup report data** ➜ **any of them**, regardless of region.

## 5.5 Azure Site Recovery (disaster recovery)

**The idea.** Site Recovery **continuously replicates** VMs to another region (or from on-prem to Azure). If the primary region goes down, you **fail over** and run there.

**How it works – lifecycle:**
1. **Enable replication.** Azure installs the Mobility service extension, uses a cache storage account in the source region, and stores everything in a **Recovery Services vault in the target region**.
2. **Test failover** (optional, recommended): bring up copies in an isolated VNet for a **DR drill**, then clean up. Production keeps running.
3. **Failover**: for a **real outage**. Choose a recovery point (latest, latest processed, latest app-consistent, custom).
4. **Commit** the failover.
5. **Reprotect**: start replicating **back** from the secondary region to the primary.
6. **Failback**: once the primary region is healthy, fail over back to it (then reprotect again).

- **Recovery plans** group VMs so they fail over in order (database first, then app), with scripts or runbooks.
- **RPO** = how much data you can lose. **RTO** = how long you can be down.

**🧭 Scenario guide**
- If you're asked **"primary region is down now, which actions?"** ➜ **verify the VMs are protected and healthy ➜ run a failover ➜ reprotect**.
  - **Initiate replication** = initial setup, already done.
  - **Test failover** = drills only.
  - **Failback** = later, when the primary region is back.
- If you're asked to **prove DR works without affecting production** ➜ **test failover**.
- If you're asked to **move a VM into availability zones using Site Recovery** ➜ the VM needs **managed disks** first (3.4).

## 5.6 Streaming metrics (Stream Analytics / event processing)

**The idea.** When events flow in from Event Hubs or IoT Hub, metrics tell you whether processing keeps up.

| Metric | Meaning |
|---|---|
| **Backlogged Input Events** | Events **waiting** to be processed. If it keeps growing, you can't keep up ➜ scale out |
| **Watermark Delay** | How **late** processing is compared with event time |
| Out-of-Order Events / Late Input Events | Events arriving out of sequence or after the allowed window |
| Runtime errors / Function execution errors | Failures, not backlog |
| SU % utilization | How busy the Stream Analytics job is |

These metric names belong to **Azure Stream Analytics** jobs. For an Event Hubs-triggered Azure Function, the equivalent signal is consumer **lag** (incoming vs outgoing messages on the Event Hub, or Application Insights).

**🧭 Scenario guide**
- If you're asked **"which metric shows the number of unprocessed events?"** ➜ **Backlogged Input Events**.
- If you're asked **"how delayed is processing?"** ➜ **Watermark Delay**.
- If you're asked **"are events arriving in the wrong order?"** ➜ **Out-of-Order Events**.

### 🔎 Research – Part 5
- **Read (Microsoft Learn):**
  - Azure Monitor overview: https://learn.microsoft.com/en-us/azure/azure-monitor/overview
  - Service Health: https://learn.microsoft.com/en-us/azure/service-health/overview
  - Advisor: https://learn.microsoft.com/en-us/azure/advisor/advisor-overview
  - Advisor cost recommendations: https://learn.microsoft.com/en-us/azure/advisor/advisor-cost-recommendations
  - Network Watcher: https://learn.microsoft.com/en-us/azure/network-watcher/network-watcher-overview
  - Traffic Analytics: https://learn.microsoft.com/en-us/azure/network-watcher/traffic-analytics
  - App Service diagnostic logs: https://learn.microsoft.com/en-us/azure/app-service/troubleshoot-diagnostic-logs
  - Azure Backup overview: https://learn.microsoft.com/en-us/azure/backup/backup-overview
  - Recovery Services vaults: https://learn.microsoft.com/en-us/azure/backup/backup-azure-recovery-services-vault-overview
  - Restore VMs: https://learn.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms
  - Recover files from VM backup: https://learn.microsoft.com/en-us/azure/backup/backup-azure-restore-files-from-vm
  - Backup reports: https://learn.microsoft.com/en-us/azure/backup/configure-reports
  - Site Recovery overview: https://learn.microsoft.com/en-us/azure/site-recovery/site-recovery-overview
  - Azure-to-Azure replication tutorial: https://learn.microsoft.com/en-us/azure/site-recovery/azure-to-azure-tutorial-enable-replication
  - Stream Analytics job metrics: https://learn.microsoft.com/en-us/azure/stream-analytics/stream-analytics-job-metrics
- **Microsoft Learn path:** search "AZ-104: Monitor and back up Azure resources".
- **Watch:** John Savill – search "Azure Monitor deep dive", "Azure Backup deep dive", "Azure Site Recovery".
- **Cheat sheets:** https://tutorialsdojo.com/azure-advisor/ · https://tutorialsdojo.com/azure-virtual-machines/ (backup and ASR sections)
- **Hands-on:**
  - Open Advisor and filter the Cost category.
  - Create a Service Health alert with an email action group.
  - Back up a test VM, then run *File Recovery* and look at the script.
  - Turn on web server logging for a test web app and open the log stream.

---

# How to read AZ-104 questions

- **Answer every question.** There's no penalty for a wrong answer.
- **Scan the tables first** for the columns that decide the answer: **region, SKU, kind, state (running / deallocated), OS version, redundancy, peering status**.
- **"Minimize administrative effort" or "most cost-effective"** points to the **built-in feature**: lifecycle management, Advisor, one App Service plan, one NSG, group-based licensing, CLI/portal import.
- **Order-of-steps questions:** for each step, ask "what must already exist for this to work?" Then build the chain from the bottom.
- **Yes/No "does the solution meet the goal" series:** judge each solution on its own. The same scenario will have different correct answers.
- **Case studies:** read the actual question first, then search the scenario only for the requirements it mentions. Names sometimes differ between scenario and question, so match by role ("the admin user"), not by name.
- **Outdated wording:** Azure AD = Microsoft Entra ID. Basic SKUs (public IP, load balancer) are retired, but they still appear as distractors. Practice tests sometimes lag behind the docs, so when in doubt, check Microsoft Learn.
