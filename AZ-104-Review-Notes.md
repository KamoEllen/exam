# AZ-104 Azure Administrator – Mistakes Review & Study Notes

> Built from the two Tutorials Dojo reports in this repo:
> - **Attempt A – "Timed Mode Set 1"**: 54 / 79 points (**68.35% – FAIL**, pass mark is 72%). Time used: 17 min 25 s.
> - **Attempt B – "Your Progress" page**: 35 / 70 points (**50%**).
>
> Every question you got wrong is below, with **what you picked**, **the right answer**, **why**, and **the rule to remember / teach**.
> Where the Tutorials Dojo (TD) explanation itself is wrong or confusing, there's a **⚠️ Errata** note.

---

## Contents

1. [Scoreboard](#1-scoreboard)
2. [The 10 big lessons (read this first)](#2-the-10-big-lessons-read-this-first)
3. [Domain 1 – Identities & Governance](#3-domain-1--manage-azure-identities-and-governance)
4. [Domain 2 – Storage](#4-domain-2--implement-and-manage-storage)
5. [Domain 3 – Compute](#5-domain-3--deploy-and-manage-azure-compute-resources)
6. [Domain 4 – Virtual Networking](#6-domain-4--implement-and-manage-virtual-networking)
7. [Domain 5 – Monitor & Maintain](#7-domain-5--monitor-and-maintain-azure-resources)
8. [Cheat sheets (tables to memorise)](#8-cheat-sheets)
9. [Teaching kit: self-test questions](#9-teaching-kit-self-test-questions)
10. [Exam technique](#10-exam-technique)

---

## 1. Scoreboard

### Attempt A – Timed Mode Set 1 (by category)

| Domain | Score |
|---|---|
| Monitor and Maintain Azure Resources | 85.71% ✅ strongest |
| Deploy and Manage Azure Compute Resources | 73.68% |
| Implement and Manage Storage | 68.18% |
| Implement and Manage Virtual Networking | 62.50% |
| Manage Azure Identities and Governance | **57.14% ❌ weakest** |

25 points lost on 20 questions: Q3, 4, 5, 6, 13, 14, 15, 16, 20, 21, 27, 31, 32, 34, 42, 45, 47, 49, 53, 55.

### Attempt B – Progress page (by category)

| Domain | Score |
|---|---|
| Implement and Manage Virtual Networking | 80% (12/15) ✅ |
| Deploy and Manage Azure Compute Resources | 58.33% (7/12) |
| Manage Azure Identities and Governance | 40% (4/10) |
| Implement and Manage Storage | **38.46% (5/13) ❌** |
| Monitor and Maintain Azure Resources | **35% (7/20) ❌** |

35 points lost on 29 questions. **3 questions (6 points) were left blank.**

### Where the points went (both attempts)

| Mistake type | Points lost |
|---|---|
| "Put the steps in order" drag-and-drop (File Sync, Azure Files AD DS auth) | **8** (got 0 on both) |
| Questions left blank | **6** |
| Region / location rules (PPG, Recovery Services vault, Bastion, GRS pair) | ~8 |
| Picked a real feature that's the wrong tool for the job | ~15 |
| Yes/No "does the solution meet the goal" series | 5 |

---

## 2. The 10 big lessons (read this first)

1. **Learn the setup sequences by heart.** Order-of-steps questions are worth up to 4 points each and you scored 0 on both. (See [Cheat sheet 8.1](#81-order-of-operations-sequences).)
2. **Never leave a question blank.** There's no penalty for a wrong answer, so a guess can only gain points. You left 6 points on the table.
3. **Check the region first.** PPGs, Recovery Services vaults, Bastion and VNets must match the region of what they serve. The **resource group's** location doesn't matter.
4. **Availability Set ≠ Availability Zone.** A set protects against rack/hardware failures and maintenance **inside one datacenter**. A zone protects against a **whole datacenter failing**.
5. **Update domains = planned maintenance. Fault domains = hardware failure.**
6. **Load balancer SKUs must match.** A Standard LB only accepts VMs with a Standard public IP or **no public IP at all**.
7. **Licenses ≠ roles ≠ group settings.** Premium features come from **licenses**. Subscription permissions come from **IAM (RBAC)** on the subscription. Device local admins come from **Entra ➜ Devices ➜ Device settings**.
8. **For cost clean-up (unattached disks), use Azure Advisor**, not Cost Analysis, Monitor or Billing roles.
9. **Pick the built-in, purpose-made tool.** Lifecycle management beats Azure Functions, Advisor beats custom KQL, Storage Explorer beats mapping a drive to Blob, and the Network Watcher agent beats other agents for packet capture.
10. **Read every word of the Yes/No series questions.** "Grant control" vs "Session control", "Basic" vs "Standard", and "remove the IP" vs "add an IP" each flip the answer.

---

## 3. Domain 1 – Manage Azure Identities and Governance

### A-Q3 · Verify a custom domain in Microsoft Entra ID
- **Scenario:** You added `tutorialsdojo.com` to Entra ID and need Azure to verify it.
- **You picked:** RRSIG ❌ **Correct:** **MX** (TXT also works)
- **Why:** Entra gives you a value to publish in DNS as a **TXT** or **MX** record, then checks that it's there.
  - RRSIG = DNSSEC signature
  - A = name → IPv4 address
  - SOA = zone admin info
  - None of these prove ownership.
- **Remember:** *"Verify a domain = TXT or MX."*

### A-Q31 · Give users Entra ID P1 features
- **You picked:** "Configure external collaboration settings" ❌
- **Correct:** **Licenses blade ➜ select the user ➜ assign the license** (or assign the license to a group).
- **Why:**
  - Premium features only light up for users who have a license **assigned**.
  - External collaboration settings control guest (B2B) access.
  - Directory roles grant admin permissions, not features.
  - Bulk create only makes accounts.
- **Remember:** *"Buying licenses isn't enough. You must assign them to users or groups."*

### A-Q53 · Case study (TechWare) – give an admin subscription-level rights
- **You picked:** "Entra Admin Center ➜ configure group settings" ❌
- **Correct:** **Subscriptions blade ➜ pick the subscription ➜ Access control (IAM)** ➜ add a role assignment (Owner).
- **Why:** Azure **resource** permissions (Owner/Contributor/Reader on a subscription, RG or resource) are **Azure RBAC**, managed in **IAM**. Entra group settings are directory-level, not subscription-level. OAuth 2.0 is app authentication. Exchange distribution groups have nothing to do with Azure permissions.
- **⚠️ Errata:** The question says "Admin Manager", but the scenario calls the user "TechAdmin". Same person, so don't let the name throw you.
- **Remember:** *"Rights on Azure resources = IAM blade at the right scope."*

### B-Q2 · Add local administrators for all Entra-joined devices
- **You picked:** Configure OAuth 2.0 authorization endpoint ❌
- **Correct:** **Manage device settings through the Devices blade** (Entra ID ➜ Devices ➜ Device settings ➜ *Manage additional local administrators on all Microsoft Entra joined devices*).
- **Why:**
  - Users added there get the **Device Administrator** role.
  - This needs Premium (P1/P2).
  - By default, Global Admins and the device owner are local admins.
- **Remember:** *"Anything about devices joining, or admins on devices = Devices ➜ Device settings."*

### B-Q5 · Case study (Adatum) – export the Contributor role to build a custom role
- **You picked:** `Get-AzRoleDefinition -Name Contributor | ConvertFrom-Json` ❌
- **Correct:** `Get-AzRoleDefinition -Name Contributor | ConvertTo-Json`
- **Why:**
  - `Get-AzRoleDefinition` returns the **definition** of a role (its actions). `Get-AzRoleAssignment` lists **who has** a role, which is the wrong cmdlet here.
  - You want to **produce** a JSON file to edit, so the pipe goes **To** JSON.
  - `ConvertFrom-Json` turns JSON text into a PowerShell object, which is the opposite direction.
  - Afterwards: edit the JSON (new name, remove `Id`, set `AssignableScopes`) ➜ `New-AzRoleDefinition -InputFile`.
- **Remember:** *"Definition = what the role can do. Assignment = who has it. To-Json = make the file."*

### B-Q6 · Contributor role ➜ can users enable Traffic Analytics? (Yes/No series)
- **You picked:** No ❌ **Correct:** **Yes**
- **Why:** Traffic Analytics can be enabled by **Owner, Contributor, or Network Contributor** at subscription scope. (Reader, for example, can't.)
- **Remember:** *"Traffic Analytics = Owner / Contributor / Network Contributor."*

### B-Q8 · Conditional Access – require MFA + hybrid-joined device ➜ "grant control"
- **You picked:** No ❌ **Correct:** **Yes**

### B-Q9 · Same scenario ➜ "session control"
- **You picked:** Yes ❌ **Correct:** **No**
- **Why (both):**
  - **Grant controls** = block access, or grant access only if a requirement is met: **require MFA**, **require compliant device**, **require hybrid Entra joined device**, approved app, terms of use, password change.
  - **Session controls** = limits *after* sign-in: app-enforced restrictions, Conditional Access App Control, sign-in frequency, persistent browser session.
- **Remember:** *"Require something before you get in = GRANT. Limit what you do once in = SESSION."*

### B-Q13 · Read a role-assignment JSON file (left blank – 3 points)
- **File:** TD1 = Owner, TD2 = Owner, TD3 = Reader, TD4 = Storage File Data SMB Share Reader (all at subscription scope).
- **Correct answers:**
  - TD1 can deploy VMs ➜ **Yes** (Owner can do everything, including role assignments)
  - TD3 can delete resources ➜ **No** (Reader can only view)
  - TD4 can provision file shares ➜ **No** (that role only *reads files* inside shares, a data-plane role; creating shares is management-plane work)
- **Remember:** *"Owner = everything + grant access. Contributor = everything except grant access. Reader = look only. 'Data' roles = data inside the resource, not the resource itself."*

---

## 4. Domain 2 – Implement and Manage Storage

### A-Q4 · Which storage account can be converted in place to ZRS?
| Account | Kind | Redundancy | Convert to ZRS? |
|---|---|---|---|
| tdaccount1 | GPv2 Standard | LRS | ✅ **Yes – the answer** |
| tdaccount2 | GPv2 Premium | RA-GRS | ❌ Must first switch RA-GRS ➜ GRS/LRS |
| tdaccount3 | **GPv1** | GRS | ❌ GPv1 doesn't support ZRS (you picked this) |
| tdaccount4 | **BlobStorage** (legacy) | LRS | ❌ BlobStorage doesn't support ZRS |
- **Remember:**
  - *"ZRS only on GPv2, FileStorage, BlockBlobStorage."*
  - *"Live migration (in-place conversion) works from LRS or GRS. If the account is RA-GRS, drop the read access first."*
  - *"Legacy kinds (GPv1, BlobStorage) must be upgraded to GPv2 first."*

### A-Q6 · Azure File Sync – the 4 steps in order (0/4)
- **You put:** Server endpoint (1) ➜ Sync group (2) ➜ Agent (3) ➜ Register (4). That's reversed. ❌
- **Correct order:**
  1. **Deploy the Azure File Sync agent** on the server
  2. **Register the server** with the Storage Sync Service
  3. **Create a sync group + cloud endpoint** (the Azure file share)
  4. **Create a server endpoint** (a folder on the registered server)
- **Logic:** You can't register a server without the agent. You can't add a server endpoint for a server that isn't registered, or to a sync group that doesn't exist yet.
- **Memory hook:** **"A-R-C-S" – Agent, Register, Cloud endpoint, Server endpoint.**
- (Steps before these: create the Storage Sync Service; on the server, disable IE Enhanced Security Configuration for registration.)

### A-Q16 · File Sync – what can you add to a sync group? (1/3)
- **Setup:** TDGroup1 already has cloud endpoint TDShare1 and server endpoint `FileServer1 E:\tutorials`.

| Statement | You said | Correct | Rule |
|---|---|---|---|
| Add `C:\files` of FileServer2 as a server endpoint | No ❌ | **Yes** | FileServer2 has no endpoint in this group yet |
| Add `F:\dojo` of FileServer1 as a server endpoint | Yes ❌ | **No** | **One server endpoint per server per sync group** |
| Add TDShare2 as a second cloud endpoint | No ✅ | **No** | **Exactly one cloud endpoint per sync group** |
- **Remember:** *"Sync group = 1 cloud endpoint + many server endpoints, but max 1 per server."*

### B-Q3 · Replicate blobs from Southeast Asia to **Australia Central**
- **You picked:** Configure versioning ❌ **Correct:** **Configure object replication**
- **Why:**
  - GRS always copies to the **paired region** (Southeast Asia ➜ **East Asia**), and you can't pick the target. So GRS can't reach Australia Central.
  - **Object replication** copies block blobs asynchronously to any storage account you choose, in any region.
  - Versioning (plus change feed on the source) is a **prerequisite** of object replication, not the solution itself.
- **Remember:** *"Need a specific other region = object replication. GRS = paired region only."*

### B-Q4 · Case study – move media files to a Blob container over the Internet
- **You picked:** File Explorer mapping a drive with the storage account key ❌
- **Correct:** **Use Azure Storage Explorer**
- **Why:**
  - **You can't map a Blob container as a drive letter.** Drive mapping (SMB) works only for **Azure Files**.
  - Import/Export ships physical disks, which is not "over the Internet".
- **Remember:** *"Drive letter = Azure Files only. Blob = Storage Explorer / AzCopy / portal."*

### B-Q5 · Mount Azure Files with on-prem AD DS credentials – 4 steps (0/4)
- **You put:** Enable AD DS auth ➜ Permissions ➜ Mount ➜ Sync with Entra Connect (sync last). ❌
- **Correct order:**
  1. **Sync on-prem AD to Entra ID with Microsoft Entra Connect** (identities must be hybrid)
  2. **Enable AD DS authentication** on the storage account (register it in AD DS)
  3. **Assign share-level (RBAC) and directory/file-level (NTFS ACL) permissions**
  4. **Mount the file share** with AD credentials
- **Memory hook:** **"Sync ➜ Switch on ➜ Share permissions ➜ Mount."** Identity must exist before you can enable auth for it.

### B-Q7 · Hot GPv2 account, infrequent access, archive after 120 days (pick TWO)
- **You picked:** Azure Function ❌ + lifecycle rule ✅
- **Correct:** **Lifecycle management rule (Archive after 120 days)** + **Set default access tier to Cool**
- **Why:**
  - The data is "infrequently accessed", so Cool is cheaper than Hot and still gives instant access.
  - Lifecycle management is built in, so there's no code to maintain (an Azure Function means more admin work).
  - The account's default tier can **never** be Archive. Archive is set per blob, and reading it needs hours of rehydration.
- **Remember:** *"Default tier = Hot/Cool, never Archive. Moving between tiers automatically = lifecycle management."*

### B-Q8 · Max stored access policies on a container
- **You picked:** 10 ❌ **Correct:** **5**
- **Remember:** *"5 stored access policies per container / share / queue / table."* A sixth gives HTTP 400.
- Bonus teaching point: a SAS tied to a stored access policy can be **revoked** by deleting or changing the policy. An ad-hoc SAS can only be revoked by rotating the account key.

### B-Q12 · Allow only one public IP + recover deleted blobs for 14 days (left blank – 2 points)
- **Correct (from the portal menu shown):**
  - Requirement 1 ➜ **Networking** (storage firewall: "Enabled from selected networks", add Manila's public IP)
  - Requirement 2 ➜ **Data protection** (enable **blob soft delete**, and container soft delete, retention = 14 days)
- **Note:** The PDF doesn't show the dropdown answers for this question because it was unanswered. These are the features that meet the stated requirements.

---

## 5. Domain 3 – Deploy and Manage Azure Compute Resources

### A-Q15 · Can't RDP to a VM; NSG allows RDP at priority 300
- **You picked:** Redeploy TD1 ❌ **Correct:** **Start TD1**
- **Why:**
  - The NSG is fine: RDP (3389) is allowed from Any, and it's the highest-priority custom rule.
  - The VM has no usable public IP because it's **stopped (deallocated)**. A **dynamic** public IP is released on deallocation and assigned again on start.
  - Redeploy moves the VM to a new host. It's for host problems, and it's more effort.
- **Remember:** *"Deallocated = no dynamic public IP. Use a Static IP if it must never change."*

### A-Q20 · vCPU quotas (2/3)
- **Quotas (North Central US):** Av2 family 15, DSv3 family 15, **Total Regional 15**.
- **Existing VMs:** VM1 A4_v2 = 4 (running), VM2 D8s_v3 = 8 (**stopped-deallocated**), VM3 is in another region.
- **Correct:**

| Create (in order) | Regional count | Allowed? |
|---|---|---|
| VM4 A2m_v2 (2) | 4 + 8 + 2 = 14 | ✅ Yes |
| VM5 D2s_v3 (2) | 14 + 2 = 16 > 15 | ❌ **No** (you said Yes) |
| VM6 A8_v2 (8) | 14 + 8 = 22 > 15 | ❌ No |

- **⚠️ Errata:** The TD explanation says quotas count only *running* VMs and that 11 vCPUs remain, then contradicts itself ("only 1 vCPU remains"). The correct rule, from Microsoft's quota docs: **quota counts cores of both allocated and deallocated VMs.** VM2's 8 cores count. That's why only 1 core is left after VM4.
- **Remember:**
  - *"Each new VM must fit BOTH the family quota AND the total regional quota."*
  - *"Deallocated VMs still use quota. Only deleting them frees it."*
  - *"Do the maths in the order given."*

### A-Q21 · Keep ≥2 of 3 VMs running if a datacenter fails
- **You picked:** All VMs in a single Availability Set ❌
- **Correct:** **One VM in each Availability Zone**
- **Why:**
  - An availability set spreads VMs across racks (fault domains) **inside one datacenter**, so a datacenter outage takes them all down.
  - Zones are separate datacenters (own power, cooling and network) in the same region.
- **Remember:** *"Datacenter failure ➜ Zones. Rack/hardware failure or maintenance ➜ Sets. Region failure ➜ another region (ASR/paired)."*

### A-Q27 · Five web apps, same region, cheapest
- **You picked:** Five App Service plans ❌ **Correct:** **One App Service plan**
- **Why:** You pay for the **plan** (the VMs), not per app. Many apps can share one plan in the same region.
- **Remember:** *"Plan = the compute you pay for. Apps ride on it for free. You only need another plan for another region, OS or tier."*

### A-Q32 · ≥2 VMs available during **planned maintenance**
- **You picked:** UD = 3, FD = 1 ❌ **Correct:** **UD = 3, FD = 2**
- **Why:**
  - Planned maintenance reboots **one update domain at a time**. With 3 UDs, only about ⅓ of VMs go down at once.
  - FD = 1 means no protection from rack failure, and the recommended minimum is 2 FDs.
  - UD 1 / FD 3 means everything reboots together.
- **Remember:** *"Update domain = Update/planned. Fault domain = Failure/hardware."* (Max: 20 UDs, 3 FDs in most regions.)

### B-Q7 · Allow ASR to move a VM into Availability Zones
- **VM settings:** South Central US, Standard HDD, Ultra Disk off, **Managed disks: Disabled**.
- **You picked:** Ultra Disk compatibility ❌ **Correct:** **Managed disks**
- **Remember:** *"Availability Zones and ASR zone moves need managed disks."* The region already supports zones, and the disk type doesn't matter.

### B-Q8 · Which proximity placement group can a VM scale set use?
- **Setup:** TD-VMSS1 is in **Australia East** (its RG, TD-RG3, is in East Asia, which is irrelevant).
- **You picked:** All three PPGs ❌
- **Correct:** **TD-Proximity3** (Australia East)
- **Remember:** *"PPG and VMs/VMSS must be in the same REGION. The resource group's location never matters for this."*
- ⚠️ Errata: the TD text says "TD-VMSS1 is located in East Asia" in one line. It's in Australia East, as the table shows.

### B-Q10 · Install web components on VMSS instances automatically (pick TWO)
- **You picked:** VPN client package ❌ + new scale set ❌
- **Correct:** **Create a configuration script** + **configure the `extensionProfile` of the ARM template** (Custom Script Extension)
- **Remember:** *"Install software on VMs/VMSS at deploy time = Custom Script Extension (or DSC) in extensionProfile."*

### B-Q11 · Bicep – deploy MyApp into resource group WebAppRG
- **You picked:** `location` ❌ **Correct:** **`scope`**
- **Why:**
  - `scope` sets **where** a module or resource is deployed (for example `scope: resourceGroup('WebAppRG')`).
  - `targetScope` only says what **type** of scope the file targets (resourceGroup / subscription / managementGroup / tenant).
  - `location` = Azure region. `tags` = metadata.
- **Remember:** *"targetScope = what kind of scope. scope = which exact one."*

### B-Q12 · Capture the current state of all resources for future automation
- **You picked:** Capture an image of a VM ❌
- **Correct:** **Export the resource group as a template**
- **Remember:** *"Reproduce everything = Export template (ARM JSON). A VM image only captures one VM's disk."*

---

## 6. Domain 4 – Implement and Manage Virtual Networking

### A-Q5 · P2S client can't reach a newly peered VNet
- **Setup:** TD1 (point-to-site VPN) ➜ TDVnet1 works. Peering TDVnet1 ↔ TDVnet2 was **just added**.
- **You picked:** Enable gateway transit on TDVnet2 ❌
- **Correct:** **Download the VPN client configuration package again and reinstall it on TD1**
- **Why:**
  - The P2S client gets its routes from the config package.
  - **Any change in topology (new peering, new address space) means re-downloading and reinstalling the client.**
  - Gateway transit already works, because on-prem can reach TDVnet2.
- **Remember:** *"Topology changed + P2S ➜ re-download the client."*

### A-Q13 · Migrate an on-prem DNS zone file to Azure DNS (pick TWO)
- **You picked:** Azure CLI ✅ + Azure PowerShell ❌
- **Correct:** **Azure CLI** + **Azure Portal**
- **Why:**
  - Zone-file **import/export** is supported in the **CLI** (`az network dns zone import`) and the **portal**.
  - PowerShell has no zone-file import cmdlet.
  - Cloud Shell just *runs* the CLI, so it's not a separate tool.
  - ARM templates don't ingest BIND files.
- Note: older material says "CLI only". Microsoft has since added portal import/export.

### A-Q14 · VNet peerings show "Disconnected" (0/2)
- **You picked:** "TDVnet1, TDVnet2 and TDVnet3" ❌ and "Enable Allow gateway transit" ❌
- **Correct:**
  - VMs on TDVnet1 can reach: **TDVnet1 only**
  - First step to get the peering to Connected: **Delete TDVnet1-2** (then recreate it on both sides)
- **Why:**
  - **Disconnected** means the other side's link was deleted, so no traffic flows.
  - You can't fix a disconnected peering. **Delete it and recreate it.**
  - Peering states: *Initiated* (one side made) ➜ *Connected* (both sides) ➜ *Disconnected* (one side removed).
- **Remember:** *"Disconnected = delete + recreate."*

### A-Q42 · Azure Bastion in TDVnet1 (Singapore) (2/3)

| Statement | You said | Correct |
|---|---|---|
| TD3 can only connect to TD1 using TD1's public IP | No ✅ | **No** |
| TD3 can connect to TD1 using its private IP | Yes ✅ | **Yes** |
| TD3 can connect to TD2 (TDVnet2, Tokyo) | Yes ❌ | **No** |
- **Why:** Bastion reaches VMs in **its own VNet** and in **peered** VNets. TDVnet2 isn't peered, so it can't reach TD2.
- **⚠️ Errata:** The TD text says Bastion can use "the public and private IP". In fact **Bastion always connects to the VM's private IP**, and VMs don't need a public IP at all.
- **Remember:** *"Bastion = private IP, same VNet or peered VNets. One Bastion can serve many peered VNets."*

### A-Q47 / A-Q49 · Standard public LB backend pool (Yes/No series)
- **VMs:** TD1 (no PIP), TD2 (Standard PIP), TD3 (Standard PIP), **TD4 (Basic PIP)**. The LB "Manila" is **Standard**.

| Solution | You said | Correct | Why |
|---|---|---|---|
| Attach a **Basic** PIP to TD1 | Yes ❌ | **No** | That *adds* a mismatch, since Basic ≠ Standard |
| **Remove** the PIP from TD4 | No ❌ | **Yes** | VMs with no PIP can join, and TD4's Basic PIP was the only blocker |
- **Rules:**
  - VM public IP SKU must **match** the LB SKU, or the VM has **no** public IP.
  - Stopped VMs **can** still be added to a backend pool.
  - Another fix for TD4: upgrade its PIP to Standard.
- **Remember:** *"Standard LB: Standard PIP or no PIP. Basic doesn't mix."* (Basic LB/PIP SKUs are retired, so expect Standard everywhere.)

### A-Q55 · Container Apps environment subnet (1/2)
- **You picked:** /24 ❌ **Correct:** **/26** (VNet: any of A, B or C ✅)
- **Why:**
  - **Workload profiles** environment ➜ minimum subnet **/27**.
  - **Consumption-only** environment ➜ minimum **/23**.
  - /27 wasn't offered, so the smallest valid option is /26.
- **Remember:** *"Workload profiles = /27. Consumption-only = /23. The subnet must be dedicated to the environment."*

### B-Q5 · Minimum number of NSGs
- **You picked:** 3 ❌ **Correct:** **1**
- **Why:**
  - One NSG can be associated with **many subnets/NICs**, and its rules can target specific IPs (TD1's IP for RDP, TDSub2/TDSub3 ranges for HTTPS).
  - Default rules already allow VNet ↔ VNet traffic (so TD1 ↔ TD2 works) and deny other inbound traffic.
- **Remember:** *"NSGs are reusable. Use rules with specific destinations, not one NSG per subnet."*

### B-Q6 · Delegate `portal.tutorialsdojo.com` to another Azure DNS zone
- **You picked:** PTR record ❌ **Correct:** **NS record named `portal`** in the parent zone
- **Steps:** Create the child zone `portal.tutorialsdojo.com` ➜ copy its 4 name servers ➜ in the parent zone add **NS** record `portal` with those servers.
- **Remember:** *"Delegate = NS. Verify ownership = TXT/MX. Alias name = CNAME. IP ➜ name (reverse) = PTR."*

### B-Q8 · S2S VPN as a failover path for ExpressRoute (pick THREE)
- **You picked:** Basic SKU gateway ❌ + local network gateway ✅ + connection ✅
- **Correct:** **VPN gateway VpnGw1** + **local network gateway** + **connection**
- **Why:** **The Basic SKU doesn't support coexistence with ExpressRoute** (no BGP either). VpnGw1 is the cheapest valid SKU. Virtual WAN / Virtual Hub are overkill.
- **Remember:** *"S2S = VPN gateway + Local network gateway (represents on-prem) + Connection. Coexisting with ER ➜ not Basic."*

---

## 7. Domain 5 – Monitor and Maintain Azure Resources

### A-Q34 · Find unattached disks, only in selected resource groups
- **You picked:** Assign the Billing Administrator role ❌
- **Correct:** **Edit the Azure Advisor configuration to include only those resource groups, then review its Cost recommendations**
- **Why:** Advisor (Configuration ➜ choose subscriptions/RGs) already flags unattached disks. Billing roles don't manage resources. KQL is extra work. Cost Management views work, but they don't *limit* Advisor's scope.

### B-Q10 · Find unattached disks (no RG restriction)
- **You picked:** Cost Analysis ❌ **Correct:** **Advisor recommendations in Azure Cost Management**
- **Remember (both):** *"Unused or idle resources (unattached disks, idle VMs, right-size) = Azure Advisor ➜ Cost. Cost Analysis only shows where the money went."*

### A-Q45 · Region outage – fail over with Azure Site Recovery (pick THREE)
- **You picked:** Verify ✅ + Initiate replication ❌ + Test failover ❌
- **Correct:** **Verify the VMs are protected and healthy ➜ Run a failover ➜ Reprotect**
- **Why:**
  - Replication is already set up.
  - A test failover is for DR **drills**, not a real outage.
  - Failback happens later, once the primary region is healthy again.
- **Full ASR lifecycle:** Enable replication ➜ (test failover) ➜ **failover** ➜ commit ➜ **reprotect** ➜ failback ➜ reprotect.

### B-Q1 · Enable packet capture on a VM
- **You picked:** Performance Diagnostics agent ❌
- **Correct:** **Install the Network Watcher Agent VM extension**
- **Remember:** *"Packet capture / connection troubleshoot = Network Watcher (needs its agent extension on the VM)."*

### B-Q2 · Azure Backup – file recovery & VM restore (1/2)
- **VMs:** TD1 = WS2019, TD2 = WS2016, TD3 = WS2012.
- File recovery from TD2 can run on: you said TD1, TD2 and TD3 ❌. **Correct: TD2 only.**
- Restore TD3 to: **TD3 only** ✅
- **Why:** Item-level file recovery runs a script that mounts the recovery point. It must run on the **same OS version (or a compatible client OS)**, not an older or newer server OS. A full VM restore can create a new VM, restore disks, or replace the disks of the *same* VM. It can't restore onto a different existing VM.

### B-Q4 · Backup reports – which Log Analytics workspace can store the data? (left blank)
- **Workspaces:** TDAnalytics1 (East Asia), TDAnalytics2 (Southeast Asia), TDAnalytics3 (Australia Central). The vault is in Southeast Asia.
- **Answer:** **Any of them (TDAnalytics1, 2 and 3).** Microsoft's Backup Reports docs say the workspace's location and subscription **don't have to match** the vault's.
- ⚠️ The PDF hides the options for unanswered questions, so check this in a retake. The rule above comes from Microsoft's "Configure Azure Backup reports" page.
- **Contrast:** a Recovery Services **vault** *must* be in the same region as the VMs it backs up. The **reporting workspace** doesn't have to be.

### B-Q6 · Case study – first thing to create for VM backup
- **You picked:** Backup policy ❌ **Correct:** **Recovery Services vault**
- **Remember:** *"Vault ➜ Policy ➜ Protect items."* (MABS is for on-prem and also needs a vault. Recovery plans are for ASR.)

### B-Q7 · What can each Recovery Services vault back up? (0/2)
- **Resources:** RSV1 East Asia, RSV2 Central US, SA1 East Asia (with FS1 file share and BC1 blob container), VM1 "West Asia", VM2 Central US.

| Vault | You said | Correct |
|---|---|---|
| RSV1 (East Asia) | FS1 and BC1 ❌ | **FS1** |
| RSV2 (Central US) | VM1, FS1, BC1, VM2 ❌ | **VM2** |
- **Rules:**
  1. **Same region** as the vault.
  2. **Recovery Services vault** backs up: Azure VMs, **Azure Files shares**, SQL Server / SAP HANA in VMs, MARS/MABS/DPM.
  3. **Azure Blobs** (and Managed Disks, PostgreSQL, AKS) use a **Backup vault**, not a Recovery Services vault.
- **Remember:** *"Recovery Services vault = VMs + file shares + DB in VM. Blob backup = Backup vault. Always same region."*

### B-Q9 · Developers need real-time detail on HTTP 500 errors in an App Service app
- **You picked:** Service Health alert ❌
- **Correct:** **Turn on Web server logging** (App Service logs, then use the Log stream)
- **Why:** Service Health = Azure **platform** incidents, not your app. Alert rules notify you but don't capture details. Workbooks visualise existing data.
- **Remember:** *"App errors detail = App Service diagnostic logs (web server logging / detailed errors / failed request tracing). Azure outages = Service Health."*

### B-Q10 · Metric for unprocessed events piling up
- **You picked:** Function Execution Errors ❌ **Correct:** **Backlogged Input Events**
- **Why:** Backlogged = events waiting to be processed. Watermark delay = how *late* processing is. Out-of-order = sequencing. Errors = failures, not queue length.
- ⚠️ Errata: "Backlogged Input Events" and "Watermark Delay" are **Azure Stream Analytics** job metrics, even though the question says "Azure Functions". Teach it as "backlog = queue of unprocessed input".

---

## 8. Cheat sheets

### 8.1 Order-of-operations sequences

| Task | Steps in order |
|---|---|
| **Azure File Sync** | Storage Sync Service ➜ **Agent** on server ➜ **Register** server ➜ **Sync group + cloud endpoint** ➜ **Server endpoint** |
| **Azure Files with AD DS creds** | **Entra Connect sync** ➜ **Enable AD DS auth** on storage account ➜ **Share-level RBAC + NTFS ACLs** ➜ **Mount** |
| **VM backup** | **Recovery Services vault** ➜ **Backup policy** ➜ **Enable backup** on VMs |
| **ASR real failover** | **Verify** health ➜ **Failover** (pick recovery point) ➜ **Commit** ➜ **Reprotect** ➜ later **Failback** |
| **Site-to-site VPN** | VNet + GatewaySubnet ➜ **VPN gateway** ➜ **Local network gateway** ➜ **Connection** ➜ configure on-prem device |
| **Subdomain delegation** | Create child zone ➜ copy its NS servers ➜ **NS record** in parent |
| **Custom role from built-in** | `Get-AzRoleDefinition X \| ConvertTo-Json` ➜ edit ➜ `New-AzRoleDefinition -InputFile` |
| **Custom domain in Entra** | Add domain ➜ add **TXT/MX** in DNS ➜ Verify ➜ (optional) make primary |

### 8.2 "Must be in the same region" rules

| Thing | Must match region of… |
|---|---|
| Proximity placement group | VMs / VMSS using it |
| Recovery Services vault | Resources it backs up |
| VM NIC / VNet | VM |
| Azure Bastion | Its VNet (can reach peered VNets elsewhere) |
| App Service plan | Apps in it |
| **Does NOT need to match** | Resource group location; Backup reports Log Analytics workspace; object replication destination |

### 8.3 Resiliency ladder

| Protects against | Use |
|---|---|
| Planned maintenance reboots | **Update domains** (Availability Set) |
| Rack / hardware failure | **Fault domains** (Availability Set) |
| Whole datacenter failure | **Availability Zones** |
| Whole region failure | **Azure Site Recovery / paired region / GRS** |

### 8.4 Storage quick facts
- Redundancy: **LRS** (3 copies, 1 DC) · **ZRS** (3 zones) · **GRS** (LRS + paired region) · **GZRS** (ZRS + paired region) · **RA-** prefix = read access to the secondary.
- ZRS supported on **GPv2, FileStorage, BlockBlobStorage**. Not GPv1, not legacy BlobStorage.
- Default access tier: Hot/Cool (never Archive). Archive is per blob, offline, hours to rehydrate.
- **Lifecycle management** = automatic tiering/deletion rules.
- **Object replication** = async copy of block blobs to any account in any region (needs versioning + change feed).
- **Stored access policies:** max **5** per container/share/queue/table.
- **Blob soft delete** = recover deleted blobs (1–365 days). **Versioning** = recover overwritten blobs.
- **Storage firewall** (Networking blade) = allow selected VNets / public IPs.
- Drive mapping (SMB, port 445) = **Azure Files only**.

### 8.5 DNS record types
| Record | Purpose |
|---|---|
| A / AAAA | Name ➜ IPv4 / IPv6 |
| CNAME | Alias name ➜ another name |
| MX | Mail server (also used for Entra domain verification) |
| TXT | Free text (domain verification, SPF) |
| **NS** | **Delegate** a (sub)domain to name servers |
| SOA | Zone authority info (one per zone) |
| PTR | Reverse lookup IP ➜ name |
| SRV | Service location |
| RRSIG | DNSSEC signature |

### 8.6 Networking quick facts
- Peering states: Initiated ➜ Connected. **Disconnected ➜ delete & recreate.**
- Peering is **non-transitive**. To use a peer's VPN gateway: "Allow gateway transit" on the hub + "Use remote gateways" on the spoke.
- P2S: after a topology change, **re-download & reinstall** the client package.
- Standard LB ➜ Standard PIPs or no PIP on backend VMs. Stopped VMs can be added.
- VPN gateway SKUs: **Basic** can't coexist with ExpressRoute, has no BGP, and can't use IKEv2 P2S/RADIUS. **VpnGw1+** for production.
- Container Apps subnets: workload profiles **/27**, consumption-only **/23**.
- NSG evaluation: lowest priority number first. Default rules (65000+): allow VNet, allow Azure LB, deny all.
- Dynamic public IP is released on **deallocate**. Static is kept until deleted.

### 8.7 Identity & governance quick facts
- **Owner** = full + assign roles · **Contributor** = full minus role assignment · **Reader** = view · **User Access Administrator** = assign roles only.
- "Data" roles (Storage Blob Data …, Storage File Data SMB …) act on **data**, not on creating or deleting the resource.
- Premium (P1/P2) features need **assigned licenses** (user or group-based).
- Device settings (who can join, extra local admins, MFA to join) ➜ **Entra ➜ Devices ➜ Device settings**.
- Conditional Access: **Grant** (MFA, compliant device, hybrid joined…) vs **Session** (sign-in frequency, app restrictions…).
- Traffic Analytics needs Owner / Contributor / Network Contributor.

### 8.8 Monitoring "which tool?"
| Need | Tool |
|---|---|
| Unattached disks, cost savings, right-sizing | **Azure Advisor** (Cost) |
| Where did spend go | Cost Management ➜ Cost Analysis |
| Packet capture, IP flow verify, next hop | **Network Watcher** (+ agent extension for capture) |
| NSG/VNet flow insights | Traffic Analytics (on flow logs) |
| App HTTP errors in App Service | **App Service logs / web server logging / log stream** |
| Azure platform outages | **Service Health** alerts |
| Backup usage reports | Backup Reports (diagnostics ➜ Log Analytics, any region) |
| Stream backlog | Backlogged Input Events metric |

---

## 9. Teaching kit: self-test questions

Use these to quiz yourself or a study group. Answers are at the bottom.

1. Which two DNS record types can verify a custom domain in Entra ID?
2. Put in order: create server endpoint, install agent, create sync group, register server.
3. A sync group has one cloud endpoint. Can you add a second Azure file share as a cloud endpoint?
4. A VM is stopped (deallocated) and has a dynamic public IP. Why can't you RDP to it?
5. Total regional quota is 20. You have a running 4-core VM and a deallocated 8-core VM. Can you create a 10-core VM?
6. You need to survive a datacenter outage. Availability Set or Availability Zones?
7. You need ≥2 VMs up during planned maintenance. Which setting matters most: UD or FD?
8. A peering shows "Disconnected". What's the first step?
9. Can an Azure Bastion in VNet-A reach a VM in an un-peered VNet-B?
10. Standard LB. VM has a Basic public IP. Can it join the backend pool? Name two fixes.
11. Which tool finds unattached managed disks?
12. You need blob copies in a region that isn't the paired region. What feature?
13. Can you map a drive letter to a Blob container?
14. Max stored access policies per container?
15. What's the minimum subnet for a Container Apps *workload profiles* environment?
16. Which Conditional Access control requires MFA: grant or session?
17. What's the first resource to create for VM backup?
18. Can a Recovery Services vault back up Azure Blob storage?
19. Which PowerShell command exports a built-in role so you can customize it?
20. Where do you add extra local administrators for Entra-joined devices?

<details>
<summary><b>Answers</b></summary>

1. TXT and MX
2. Install agent ➜ register server ➜ create sync group (+cloud endpoint) ➜ create server endpoint
3. No – only one cloud endpoint per sync group
4. The dynamic public IP was released. Start the VM.
5. No – 4 + 8 = 12 used, 12 + 10 = 22 > 20 (deallocated VMs still count)
6. Availability Zones
7. Update domains (use 3 UD + 2 FD)
8. Delete the peering, then recreate it (both sides)
9. No – same VNet or peered VNets only
10. No. Remove the public IP, or upgrade it to Standard.
11. Azure Advisor (Cost recommendations)
12. Object replication
13. No – drive mapping is for Azure Files only. Use Storage Explorer/AzCopy.
14. 5
15. /27 (consumption-only: /23)
16. Grant
17. Recovery Services vault
18. No – blobs use a Backup vault
19. `Get-AzRoleDefinition -Name <role> | ConvertTo-Json`
20. Entra ID ➜ Devices ➜ Device settings
</details>

---

## 10. Exam technique

- **Answer everything.** There's no negative marking. Flag it, guess, and move on.
- **Use your time.** You finished Attempt A in ~17 minutes, which is very fast. Slow down on scenario tables. Most lost points came from a detail you skimmed (region, SKU, "Disconnected", "deallocated", "grant vs session").
- **Ordering questions:** ask "what does this step *need* to exist first?" and work backwards.
- **"Minimize administrative effort / cost"** almost always points to the **built-in feature** (lifecycle management, Advisor, one App Service plan, one NSG, the CLI/portal import).
- **Yes/No series:** judge each proposed solution on its own. The same scenario has different right answers.
- **Tables:** before reading the options, note each resource's **region, SKU, state, kind and OS**. The trap is usually in one of those columns.
- **Case studies:** read the question first, then search the scenario only for the requirement it's about.
- **Retake target:** Identity & Governance, Storage and Monitor were the weakest across both attempts. Review sections 3, 4 and 7 first.
