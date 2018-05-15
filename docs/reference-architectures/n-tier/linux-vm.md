---
title: 在 Azure 上執行 Linux VM
description: 如何在 Azure 上執行 Linux VM，並注意延展性、恢復能力、管理性和安全性。
author: telmosampaio
ms.date: 04/03/2018
ms.openlocfilehash: f29b7225c2e0edbb1569c9e3a55d112d12041af8
ms.sourcegitcommit: a5e549c15a948f6fb5cec786dbddc8578af3be66
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2018
---
# <a name="run-a-linux-vm-on-azure"></a><span data-ttu-id="dd7ef-103">在 Azure 上執行 Linux VM</span><span class="sxs-lookup"><span data-stu-id="dd7ef-103">Run a Linux VM on Azure</span></span>

<span data-ttu-id="dd7ef-104">本文說明一組經過證實的作法，可用來在 Azure 上執行 Linux 虛擬機器 (VM)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-104">This article describes a set of proven practices for running a Linux virtual machine (VM) on Azure.</span></span> <span data-ttu-id="dd7ef-105">其中包括適用於佈建 VM，以及網路和存放元件的建議。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-105">It includes recommendations for provisioning the VM along with networking and storage components.</span></span> [<span data-ttu-id="dd7ef-106">**部署這個解決方案。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-106">**Deploy this solution.**</span></span>](#deploy-the-solution)

<span data-ttu-id="dd7ef-107">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="dd7ef-107">![[0]][0]</span></span>

## <a name="components"></a><span data-ttu-id="dd7ef-108">元件</span><span class="sxs-lookup"><span data-stu-id="dd7ef-108">Components</span></span>

<span data-ttu-id="dd7ef-109">除了 VM 本身，佈建 Azure VM 還需要額外的元件，包括網路功能和儲存體資源。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-109">Provisioning an Azure VM requires some additional components besides the VM itself, including networking and storage resources.</span></span>

* <span data-ttu-id="dd7ef-110">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-110">**Resource group.**</span></span> <span data-ttu-id="dd7ef-111">[資源群組][resource-manager-overview]是保存 Azure 相關資源的本機容器。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-111">A [resource group][resource-manager-overview] is a logical container that holds related Azure resources.</span></span> <span data-ttu-id="dd7ef-112">一般來說，根據資源的存留期以及將管理資源的人員來群組資源。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-112">In general, group resources based on their lifetime and who will manage them.</span></span> 

* <span data-ttu-id="dd7ef-113">**VM**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-113">**VM**.</span></span> <span data-ttu-id="dd7ef-114">您可以從已發佈的映像清單、自訂的受控映像或您上傳至 Azure Blob 儲存體的虛擬硬碟 (VHD) 檔案佈建 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-114">You can provision a VM from a list of published images, or from a custom managed image or virtual hard disk (VHD) file uploaded to Azure Blob storage.</span></span> <span data-ttu-id="dd7ef-115">Azure 支援執行各種受歡迎的 Linux 散發套件，包括 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 FreeBSD。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-115">Azure supports running various popular Linux distributions, including CentOS, Debian, Red Hat Enterprise, Ubuntu, and FreeBSD.</span></span> <span data-ttu-id="dd7ef-116">如需詳細資訊，請參閱 [Azure 和 Linux][azure-linux]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-116">For more information, see [Azure and Linux][azure-linux].</span></span>

* <span data-ttu-id="dd7ef-117">**受控磁碟**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-117">**Managed Disks**.</span></span> <span data-ttu-id="dd7ef-118">[Azure 受控磁碟][managed-disks]藉由為您處理儲存體來簡化磁碟管理。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-118">[Azure Managed Disks][managed-disks] simplify disk management by handling the storage for you.</span></span> <span data-ttu-id="dd7ef-119">作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD，因此即使主機電腦已關閉仍會保存下來。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-119">The OS disk is a VHD stored in [Azure Storage][azure-storage], so it persists even when the host machine is down.</span></span> <span data-ttu-id="dd7ef-120">對於 Linux VM，作業系統磁碟是 `/dev/sda1`。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-120">For Linux VMs, the OS disk is `/dev/sda1`.</span></span> <span data-ttu-id="dd7ef-121">我們也建議建立一或多個[資料磁碟][data-disk]，這些是用於應用程式資料的持續性 VHD。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-121">We also recommend creating one or more [data disks][data-disk], which are persistent VHDs used for application data.</span></span> 

* <span data-ttu-id="dd7ef-122">**暫存磁碟。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-122">**Temporary disk.**</span></span> <span data-ttu-id="dd7ef-123">VM 是使用暫存磁碟來建立。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-123">The VM is created with a temporary disk.</span></span> <span data-ttu-id="dd7ef-124">此磁碟會儲存在主機電腦的實體磁碟機上。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-124">This disk is stored on a physical drive on the host machine.</span></span> <span data-ttu-id="dd7ef-125">它「不會」儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中遭到刪除。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-125">It is *not* saved in Azure Storage and may be deleted during reboots and other VM lifecycle events.</span></span> <span data-ttu-id="dd7ef-126">僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-126">Use this disk only for temporary data, such as page or swap files.</span></span> <span data-ttu-id="dd7ef-127">對於 Linux VM，暫存磁碟為 `/dev/sdb1` 且掛接於 `/mnt/resource` 或 `/mnt`。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-127">For Linux VMs, the temporary disk is `/dev/sdb1` and is mounted at `/mnt/resource` or `/mnt`.</span></span>

* <span data-ttu-id="dd7ef-128">**虛擬網路 (VNet)。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-128">**Virtual network (VNet).**</span></span> <span data-ttu-id="dd7ef-129">每部 Azure VM 都會部署到可以分割成多個子網路的 VNet。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-129">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span>

* <span data-ttu-id="dd7ef-130">**網路介面 (NIC)**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-130">**Network interface (NIC)**.</span></span> <span data-ttu-id="dd7ef-131">NIC 可讓 VM 與虛擬網路通訊。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-131">The NIC enables the VM to communicate with the virtual network.</span></span>

* <span data-ttu-id="dd7ef-132">**公用 IP 位址。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-132">**Public IP address.**</span></span> <span data-ttu-id="dd7ef-133">需要有公用 IP 位址才能與 VM 通訊 &mdash; 例如透過 SSH。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-133">A public IP address is needed to communicate with the VM &mdash; for example, via SSH.</span></span>

* <span data-ttu-id="dd7ef-134">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-134">**Azure DNS**.</span></span> <span data-ttu-id="dd7ef-135">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-135">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="dd7ef-136">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-136">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

* <span data-ttu-id="dd7ef-137">**網路安全性群組 (NSG)**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-137">**Network security group (NSG)**.</span></span> <span data-ttu-id="dd7ef-138">[網路安全性群組][nsg]可用來允許或拒絕 VM 的網路流量。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-138">[Network security groups][nsg] are used to allow or deny network traffic to VMs.</span></span> <span data-ttu-id="dd7ef-139">NSG 可與子網路或個別 VM 執行個體相關聯。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-139">NSGs can be associated either with subnets or with individual VM instances.</span></span>

* <span data-ttu-id="dd7ef-140">**診斷。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-140">**Diagnostics.**</span></span> <span data-ttu-id="dd7ef-141">診斷記錄對於管理和針對 VM 進行疑難排解十分重要。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-141">Diagnostic logging is crucial for managing and troubleshooting the VM.</span></span>

## <a name="vm-recommendations"></a><span data-ttu-id="dd7ef-142">VM 建議</span><span class="sxs-lookup"><span data-stu-id="dd7ef-142">VM recommendations</span></span>

<span data-ttu-id="dd7ef-143">Azure 提供許多不同的虛擬機器大小。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-143">Azure offers many different virtual machine sizes.</span></span> <span data-ttu-id="dd7ef-144">如需相關資訊，請參閱 [Azure 中虛擬機器的大小][virtual-machine-sizes]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-144">For more information, see [Sizes for virtual machines in Azure][virtual-machine-sizes].</span></span> <span data-ttu-id="dd7ef-145">如果您將現有的工作負載移至 Azure，則從最符合您內部部署伺服器的 VM 大小開始。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-145">If you are moving an existing workload to Azure, start with the VM size that's the closest match to your on-premises servers.</span></span> <span data-ttu-id="dd7ef-146">然後根據 CPU、記憶體和每秒的磁碟輸入/輸出作業 (IOPS) 測量您的實際工作負載效能，並視需要調整大小。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-146">Then measure the performance of your actual workload with respect to CPU, memory, and disk input/output operations per second (IOPS), and adjust the size as needed.</span></span> <span data-ttu-id="dd7ef-147">如果您的 VM 需要多個 NIC，請注意每種 [VM 大小][vm-size-tables]都有定義 NIC 的數目上限。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-147">If you require multiple NICs for your VM, be aware that a maximum number of NICs is defined for each [VM size][vm-size-tables].</span></span>

<span data-ttu-id="dd7ef-148">一般而言，選擇最接近您的內部使用者或客戶的 Azure 區域。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-148">Generally, choose an Azure region that is closest to your internal users or customers.</span></span> <span data-ttu-id="dd7ef-149">不過，並非所有 VM 大小在所有區域都可供使用。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-149">However, not all VM sizes are available in all regions.</span></span> <span data-ttu-id="dd7ef-150">如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-150">For more information, see [Services by region][services-by-region].</span></span> <span data-ttu-id="dd7ef-151">如需特定區域中可用的 VM 大小清單，請從 Azure 命令列介面 (CLI) 執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="dd7ef-151">For a list of the VM sizes available in a specific region, run the following command from the Azure command-line interface (CLI):</span></span>

```
az vm list-sizes --location <location>
```

<span data-ttu-id="dd7ef-152">如需選擇已發佈 VM 映像的相關資訊，請參閱[尋找 Linux VM 映像][select-vm-image]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-152">For information about choosing a published VM image, see [Find Linux VM images][select-vm-image].</span></span>

<span data-ttu-id="dd7ef-153">啟用監視和診斷，包括基本健全狀況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-153">Enable monitoring and diagnostics, including basic health metrics, diagnostics infrastructure logs, and [boot diagnostics][boot-diagnostics].</span></span> <span data-ttu-id="dd7ef-154">如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-154">Boot diagnostics can help you diagnose boot failure if your VM gets into a non-bootable state.</span></span> <span data-ttu-id="dd7ef-155">如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-155">For more information, see [Enable monitoring and diagnostics][enable-monitoring].</span></span>  

## <a name="disk-and-storage-recommendations"></a><span data-ttu-id="dd7ef-156">磁碟和儲存體建議</span><span class="sxs-lookup"><span data-stu-id="dd7ef-156">Disk and storage recommendations</span></span>

<span data-ttu-id="dd7ef-157">為了達到最佳的磁碟 I/O 效能，我們建議使用[進階儲存體][premium-storage]，這會將資料儲存在固態硬碟 (SSD)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-157">For best disk I/O performance, we recommend [Premium Storage][premium-storage], which stores data on solid-state drives (SSDs).</span></span> <span data-ttu-id="dd7ef-158">成本是依佈建的磁碟容量而定。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-158">Cost is based on the capacity of the provisioned disk.</span></span> <span data-ttu-id="dd7ef-159">IOPS 和輸送量 (亦即，資料傳輸速率) 也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-159">IOPS and throughput (that is, data transfer rate) also depend on disk size, so when you provision a disk, consider all three factors (capacity, IOPS, and throughput).</span></span> 

<span data-ttu-id="dd7ef-160">我們也建議使用[受控磁碟][managed-disks]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-160">We also recommend using [Managed Disks][managed-disks].</span></span> <span data-ttu-id="dd7ef-161">受控磁碟不需要儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-161">Managed disks do not require a storage account.</span></span> <span data-ttu-id="dd7ef-162">您只需指定磁碟的大小和類型，它就會以高度可用的資源方式進行部署。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-162">You simply specify the size and type of disk and it is deployed as a highly available resource.</span></span>

<span data-ttu-id="dd7ef-163">新增一或多個資料磁碟。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-163">Add one or more data disks.</span></span> <span data-ttu-id="dd7ef-164">當您建立 VHD 時，它仍未格式化。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-164">When you create a VHD, it is unformatted.</span></span> <span data-ttu-id="dd7ef-165">登入 VM 來格式化磁碟。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-165">Log into the VM to format the disk.</span></span> <span data-ttu-id="dd7ef-166">在 Linux 殼層中，資料磁碟會顯示為`/dev/sdc``/dev/sdd` 等等。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-166">In the Linux shell, data disks are displayed as `/dev/sdc`, `/dev/sdd`, and so on.</span></span> <span data-ttu-id="dd7ef-167">您可以執行 `lsblk` 以列出區塊裝置，包括磁碟。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-167">You can run `lsblk` to list the block devices, including the disks.</span></span> <span data-ttu-id="dd7ef-168">若要使用資料磁碟，請建立磁碟分割和檔案系統，並掛接該磁碟。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-168">To use a data disk, create a partition and file system, and mount the disk.</span></span> <span data-ttu-id="dd7ef-169">例如︰</span><span class="sxs-lookup"><span data-stu-id="dd7ef-169">For example:</span></span>

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

<span data-ttu-id="dd7ef-170">當您新增資料磁碟時，會指派邏輯單元編號 (LUN) 識別碼給磁碟。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-170">When you add a data disk, a logical unit number (LUN) ID is assigned to the disk.</span></span> <span data-ttu-id="dd7ef-171">或者，您可以指定 LUN 識別碼 &mdash; 例如，如果您要更換磁碟，而且想要保留相同的 LUN 識別碼，或者您有會查看特定 LUN 識別碼的應用程式。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-171">Optionally, you can specify the LUN ID &mdash; for example, if you're replacing a disk and want to retain the same LUN ID, or you have an application that looks for a specific LUN ID.</span></span> <span data-ttu-id="dd7ef-172">不過，請記住，每個磁碟的 LUN 識別碼不能重複。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-172">However, remember that LUN IDs must be unique for each disk.</span></span>

<span data-ttu-id="dd7ef-173">您可能會想變更 I/O 排程器以將 SSD 上的效能最佳化，因為具有進階儲存體帳戶的 VM 磁碟為 SSD。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-173">You may want to change the I/O scheduler to optimize for performance on SSDs because the disks for VMs with premium storage accounts are SSDs.</span></span> <span data-ttu-id="dd7ef-174">一般建議是使用適用於 SSD 的 NOOP 排程器，但您應該使用 [iostat] 之類的工具，來監視工作負載的磁碟 I/O 效能。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-174">A common recommendation is to use the NOOP scheduler for SSDs, but you should use a tool such as [iostat] to monitor disk I/O performance for your workload.</span></span>

<span data-ttu-id="dd7ef-175">建立儲存體帳戶以放置診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-175">Create a storage account to hold diagnostic logs.</span></span> <span data-ttu-id="dd7ef-176">標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-176">A standard locally redundant storage (LRS) account is sufficient for diagnostic logs.</span></span>

> [!NOTE]
> <span data-ttu-id="dd7ef-177">如果您未使用受控磁碟，請針對每個 VM 建立個別的 Azure 儲存體帳戶來保存虛擬硬碟 (VHD)，以避免達到儲存體帳戶的 [IOPS 限制][vm-disk-limits]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-177">If you aren't using Managed Disks, create separate Azure storage accounts for each VM to hold the virtual hard disks (VHDs), in order to avoid hitting the [(IOPS) limits][vm-disk-limits] for storage accounts.</span></span> <span data-ttu-id="dd7ef-178">請注意儲存體帳戶的總 I/O 限制。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-178">Be aware of the total I/O limits of the storage account.</span></span> <span data-ttu-id="dd7ef-179">如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-179">For more information, see [virtual machine disk limits][vm-disk-limits].</span></span>

## <a name="network-recommendations"></a><span data-ttu-id="dd7ef-180">網路建議</span><span class="sxs-lookup"><span data-stu-id="dd7ef-180">Network recommendations</span></span>

<span data-ttu-id="dd7ef-181">此公用 IP 位址可以是動態或靜態。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-181">The public IP address can be dynamic or static.</span></span> <span data-ttu-id="dd7ef-182">預設值為動態。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-182">The default is dynamic.</span></span>

* <span data-ttu-id="dd7ef-183">如果您需要一個不會變更的固定 IP 位址 &mdash;(例如，如果您需要在 DNS 中建立一個 A 記錄，或需要將 IP 位址加入安全清單)，請保留一個[靜態 IP 位址][static-ip]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-183">Reserve a [static IP address][static-ip] if you need a fixed IP address that won't change &mdash; for example, if you need to create an A record in DNS, or need the IP address to be added to a safe list.</span></span>
* <span data-ttu-id="dd7ef-184">您也可以建立 IP 位址的完整網域名稱 (FQDN)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-184">You can also create a fully qualified domain name (FQDN) for the IP address.</span></span> <span data-ttu-id="dd7ef-185">然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 記錄][cname-record]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-185">You can then register a [CNAME record][cname-record] in DNS that points to the FQDN.</span></span> <span data-ttu-id="dd7ef-186">如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-186">For more information, see [Create a fully qualified domain name in the Azure portal][fqdn].</span></span> <span data-ttu-id="dd7ef-187">您可以使用 [Azure DNS][azure-dns] 或另一個 DNS 服務。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-187">You can use [Azure DNS][azure-dns] or another DNS service.</span></span>

<span data-ttu-id="dd7ef-188">所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-188">All NSGs contain a set of [default rules][nsg-default-rules], including a rule that blocks all inbound Internet traffic.</span></span> <span data-ttu-id="dd7ef-189">預設的規則不能刪除，但其他規則可以覆寫它們。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-189">The default rules cannot be deleted, but other rules can override them.</span></span> <span data-ttu-id="dd7ef-190">若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-190">To enable Internet traffic, create rules that allow inbound traffic to specific ports &mdash; for example, port 80 for HTTP.</span></span>

<span data-ttu-id="dd7ef-191">若要啟用 SSH，請新增一個 NSG 規則，以允許將輸入流量輸入至 TCP 連接埠 22。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-191">To enable SSH, add an NSG rule that allows inbound traffic to TCP port 22.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="dd7ef-192">延展性考量</span><span class="sxs-lookup"><span data-stu-id="dd7ef-192">Scalability considerations</span></span>

<span data-ttu-id="dd7ef-193">您可以藉由[變更 VM 大小][vm-resize]來相應增加或相應減少 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-193">You can scale a VM up or down by [changing the VM size][vm-resize].</span></span> <span data-ttu-id="dd7ef-194">若要水平相應放大，請將兩個以上的 VM 置於負載平衡器後方。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-194">To scale out horizontally, put two or more VMs behind a load balancer.</span></span> <span data-ttu-id="dd7ef-195">如需詳細資訊，請參閱[多層式 (N-Tier) 參考架構](./n-tier-cassandra.md)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-195">For more information, see the [N-tier reference architecture](./n-tier-cassandra.md).</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="dd7ef-196">可用性考量</span><span class="sxs-lookup"><span data-stu-id="dd7ef-196">Availability considerations</span></span>

<span data-ttu-id="dd7ef-197">若要擁有較高的可用性，請在可用性設定組中部署多個 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-197">For higher availability, deploy multiple VMs in an availability set.</span></span> <span data-ttu-id="dd7ef-198">這也會提供較高的[服務等級協定 (SLA)][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-198">This also provides a higher [service level agreement (SLA)][vm-sla].</span></span>

<span data-ttu-id="dd7ef-199">您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-199">Your VM may be affected by [planned maintenance][planned-maintenance] or [unplanned maintenance][manage-vm-availability].</span></span> <span data-ttu-id="dd7ef-200">您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-200">You can use [VM reboot logs][reboot-logs] to determine whether a VM reboot was caused by planned maintenance.</span></span>

<span data-ttu-id="dd7ef-201">為了防止在正常作業期間意外遺失資料 (例如，因使用者錯誤而造成)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具來實作時間點備份。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-201">To protect against accidental data loss during normal operations (for example, because of user error), you should also implement point-in-time backups, using [blob snapshots][blob-snapshot] or another tool.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="dd7ef-202">管理性考量</span><span class="sxs-lookup"><span data-stu-id="dd7ef-202">Manageability considerations</span></span>

<span data-ttu-id="dd7ef-203">**資源群組。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-203">**Resource groups.**</span></span> <span data-ttu-id="dd7ef-204">請將關係密切且具有相同生命週期的資源置於同一個[資源群組][resource-manager-overview]中。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-204">Put closely associated resources that share the same lifecycle into the same [resource group][resource-manager-overview].</span></span> <span data-ttu-id="dd7ef-205">資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組追蹤帳單成本。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-205">Resource groups allow you to deploy and monitor resources as a group and track billing costs by resource group.</span></span> <span data-ttu-id="dd7ef-206">您也可以刪除整組資源，這對於測試部署非常有用。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-206">You can also delete resources as a set, which is very useful for test deployments.</span></span> <span data-ttu-id="dd7ef-207">請指派有意義的資源名稱，以簡化尋找特定資源及了解其角色的程序。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-207">Assign meaningful resource names to simplify locating a specific resource and understanding its role.</span></span> <span data-ttu-id="dd7ef-208">如需詳細資訊，請參閱[建議的 Azure 資源命名慣例][naming-conventions]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-208">For more information, see [Recommended naming conventions for Azure resources][naming-conventions].</span></span>

<span data-ttu-id="dd7ef-209">**SSH**。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-209">**SSH**.</span></span> <span data-ttu-id="dd7ef-210">在您建立 VM 之前，先產生 2048 位元 RSA 公開-私密金鑰組。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-210">Before you create a Linux VM, generate a 2048-bit RSA public-private key pair.</span></span> <span data-ttu-id="dd7ef-211">建立 VM 的時候使用公開金鑰檔案。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-211">Use the public key file when you create the VM.</span></span> <span data-ttu-id="dd7ef-212">如需詳細資訊，請參閱[如何在 Azure 上搭配 Linux 與 Mac 使用 SSH][ssh-linux]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-212">For more information, see [How to Use SSH with Linux and Mac on Azure][ssh-linux].</span></span>

<span data-ttu-id="dd7ef-213">**停止 VM。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-213">**Stopping a VM.**</span></span> <span data-ttu-id="dd7ef-214">Azure 會區分「已停止」和「已解除配置」狀態。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-214">Azure makes a distinction between "stopped" and "deallocated" states.</span></span> <span data-ttu-id="dd7ef-215">您需要在 VM 狀態停止時支付費用，而不是在取消配置 VM 時支付。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-215">You are charged when the VM status is stopped, but not when the VM is deallocated.</span></span> <span data-ttu-id="dd7ef-216">在 Azure 入口網站中，[停止] 按鈕會取消配置 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-216">In the Azure portal, the **Stop** button deallocates the VM.</span></span> <span data-ttu-id="dd7ef-217">如果您已在登入時透過 OS 關閉，則會停止 VM，但不會取消配置，因此您仍需付費。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-217">If you shut down through the OS while logged in, the VM is stopped but **not** deallocated, so you will still be charged.</span></span>

<span data-ttu-id="dd7ef-218">**刪除 VM。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-218">**Deleting a VM.**</span></span> <span data-ttu-id="dd7ef-219">如果您刪除 VM，並不會刪除 VHD。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-219">If you delete a VM, the VHDs are not deleted.</span></span> <span data-ttu-id="dd7ef-220">這表示您可以放心地刪除 VM，而不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-220">That means you can safely delete the VM without losing data.</span></span> <span data-ttu-id="dd7ef-221">不過，您仍需支付儲存體費用。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-221">However, you will still be charged for storage.</span></span> <span data-ttu-id="dd7ef-222">若要刪除 VHD，請將檔案從 [Blob 儲存體][blob-storage]中刪除。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-222">To delete the VHD, delete the file from [Blob storage][blob-storage].</span></span> <span data-ttu-id="dd7ef-223">若要防止意外刪除，請使用[資源鎖定][resource-lock]來鎖定整個資源群組或鎖定個別資源 (例如 VM)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-223">To prevent accidental deletion, use a [resource lock][resource-lock] to lock the entire resource group or lock individual resources, such as a VM.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="dd7ef-224">安全性考量</span><span class="sxs-lookup"><span data-stu-id="dd7ef-224">Security considerations</span></span>

<span data-ttu-id="dd7ef-225">使用 [Azure 資訊安全中心][security-center]來集中檢視 Azure 資源的安全性狀態。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-225">Use [Azure Security Center][security-center] to get a central view of the security state of your Azure resources.</span></span> <span data-ttu-id="dd7ef-226">資訊安全中心會監視潛在的安全性問題，並提供全面性的部署安全性健康狀態。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-226">Security Center monitors potential security issues and provides a comprehensive picture of the security health of your deployment.</span></span> <span data-ttu-id="dd7ef-227">資訊安全中心是依每個 Azure 訂用帳戶設定。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-227">Security Center is configured per Azure subscription.</span></span> <span data-ttu-id="dd7ef-228">請按照 [Azure 資訊安全中心快速入門指南][security-center-get-started]中所述的方式，來啟用安全性資料收集。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-228">Enable security data collection as described in the [Azure Security Center quick start guide][security-center-get-started].</span></span> <span data-ttu-id="dd7ef-229">啟用資料收集時，資訊安全性中心就會自動掃描任何該訂用帳戶建立的 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-229">When data collection is enabled, Security Center automatically scans any VMs created under that subscription.</span></span>

<span data-ttu-id="dd7ef-230">**修補程式管理。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-230">**Patch management.**</span></span> <span data-ttu-id="dd7ef-231">若已啟用，資訊安全性中心會檢查是否遺漏了任何安全性或重要更新。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-231">If enabled, Security Center checks whether any security and critical updates are missing.</span></span> 

<span data-ttu-id="dd7ef-232">**反惡意程式碼。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-232">**Antimalware.**</span></span> <span data-ttu-id="dd7ef-233">如果啟用，資訊安全性中心會檢查是已安裝反惡意程式碼軟體。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-233">If enabled, Security Center checks whether antimalware software is installed.</span></span> <span data-ttu-id="dd7ef-234">您也可以使用資訊安全中心來從 Azure 入口網站內安裝反惡意程式碼軟體。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-234">You can also use Security Center to install antimalware software from inside the Azure portal.</span></span>

<span data-ttu-id="dd7ef-235">**作業。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-235">**Operations.**</span></span> <span data-ttu-id="dd7ef-236">請使用[角色型存取控制 (RBAC)][rbac] 來控制對您所部署 Azure 資源的存取。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-236">Use [role-based access control (RBAC)][rbac] to control access to the Azure resources that you deploy.</span></span> <span data-ttu-id="dd7ef-237">RBAC 可讓您指派授權角色給您 DevOps 小組的成員。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-237">RBAC lets you assign authorization roles to members of your DevOps team.</span></span> <span data-ttu-id="dd7ef-238">例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-238">For example, the Reader role can view Azure resources but not create, manage, or delete them.</span></span> <span data-ttu-id="dd7ef-239">某些角色專門用於特定的 Azure 資源類型。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-239">Some roles are specific to particular Azure resource types.</span></span> <span data-ttu-id="dd7ef-240">例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立新的 VM 等等。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-240">For example, the Virtual Machine Contributor role can restart or deallocate a VM, reset the administrator password, create a new VM, and so on.</span></span> <span data-ttu-id="dd7ef-241">其他對此架構可能有用的[內建 RBAC 角色][rbac-roles]包括 [DevTest Labs 使用者][rbac-devtest]和[網路參與者][rbac-network]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-241">Other [built-in RBAC roles][rbac-roles] that may be useful for this architecture include [DevTest Labs User][rbac-devtest] and [Network Contributor][rbac-network].</span></span> <span data-ttu-id="dd7ef-242">使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-242">A user can be assigned to multiple roles, and you can create custom roles for even more fine-grained permissions.</span></span>

> [!NOTE]
> <span data-ttu-id="dd7ef-243">RBAC 不會限制使用者登入 VM 可執行的動作。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-243">RBAC does not limit the actions that a user logged into a VM can perform.</span></span> <span data-ttu-id="dd7ef-244">這些權限是由客體 OS上的帳戶類型來決定。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-244">Those permissions are determined by the account type on the guest OS.</span></span>   

<span data-ttu-id="dd7ef-245">使用[稽核記錄檔][audit-logs]來查看佈建動作和其他 VM 事件。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-245">Use [audit logs][audit-logs] to see provisioning actions and other VM events.</span></span>

<span data-ttu-id="dd7ef-246">**資料加密。**</span><span class="sxs-lookup"><span data-stu-id="dd7ef-246">**Data encryption.**</span></span> <span data-ttu-id="dd7ef-247">如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-247">Consider [Azure Disk Encryption][disk-encryption] if you need to encrypt the OS and data disks.</span></span> 

## <a name="deploy-the-solution"></a><span data-ttu-id="dd7ef-248">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="dd7ef-248">Deploy the solution</span></span>

<span data-ttu-id="dd7ef-249">您可在 [GitHub][github-folder] 上進行部署。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-249">A deployment is available on [GitHub][github-folder].</span></span> <span data-ttu-id="dd7ef-250">它會部署下列各項：</span><span class="sxs-lookup"><span data-stu-id="dd7ef-250">It deploys the following:</span></span>

  * <span data-ttu-id="dd7ef-251">用來裝載 VM 且具有名為 **web** 之單一子網路的虛擬網路。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-251">A virtual network with a single subnet named **web** used to host the VM.</span></span>
  * <span data-ttu-id="dd7ef-252">含有兩個傳入規則的 NSG，允許 SSH 和 HTTP 流量到 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-252">An NSG with two incoming rules to allow SSH and HTTP traffic to the VM.</span></span>
  * <span data-ttu-id="dd7ef-253">執行最新版 Ubuntu 16.04.3 LTS 的 VM。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-253">A VM running the latest version of Ubuntu 16.04.3 LTS.</span></span>
  * <span data-ttu-id="dd7ef-254">範例自訂指令碼擴充功能，會將 Apache HTTP 伺服器部署到 Ubuntu VM，並將這兩個資料磁碟格式化。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-254">A sample custom script extension that formats the two data disks and deploys Apache HTTP Server to the Ubuntu VM.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="dd7ef-255">先決條件</span><span class="sxs-lookup"><span data-stu-id="dd7ef-255">Prerequisites</span></span>

1. <span data-ttu-id="dd7ef-256">複製、派生或下載適用於[參考架構][ref-arch-repo] GitHub 存放庫的 zip 檔案。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-256">Clone, fork, or download the zip file for the [reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="dd7ef-257">確定您已在電腦上安裝 Azure CLI 2.0。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-257">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="dd7ef-258">如需 CLI 安裝指示，請參閱[安裝 Azure CLI 2.0][azure-cli-2]。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-258">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="dd7ef-259">安裝 [Azure 建置組塊][azbb] npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-259">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="dd7ef-260">從命令提示字元、bash 提示字元或 PowerShell 提示字元中，輸入下列命令來登入您的 Azure 帳戶。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-260">From a command prompt, bash prompt, or PowerShell prompt, enter the following command to log into your Azure account.</span></span>

  ```bash
  az login
  ```

5. <span data-ttu-id="dd7ef-261">建立 SSH 金鑰組。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-261">Create an SSH key pair.</span></span> <span data-ttu-id="dd7ef-262">如需詳細資訊，請參閱[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](/azure/virtual-machines/linux/mac-create-ssh-keys)。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-262">For more information, see [How to create and use an SSH public and private key pair for Linux VMs in Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).</span></span>

### <a name="deploy-the-solution-using-azbb"></a><span data-ttu-id="dd7ef-263">使用 azbb 部署解決方案</span><span class="sxs-lookup"><span data-stu-id="dd7ef-263">Deploy the solution using azbb</span></span>

1. <span data-ttu-id="dd7ef-264">瀏覽至您在上述必要條件步驟中所下載存放庫的 `virtual-machines/single-vm/parameters/linux` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-264">Navigate to the `virtual-machines/single-vm/parameters/linux` folder for the repository you downloaded in the prerequisites step above.</span></span>

2. <span data-ttu-id="dd7ef-265">開啟 `single-vm-v2.json` 檔案，然後在引號之間輸入使用者名稱和 SSH 公開金鑰，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-265">Open the `single-vm-v2.json` file and enter a username and your SSH public key between the quotes, then save the file.</span></span>

  ```bash
  "adminUsername": "<your username>",
  "sshPublicKey": "ssh-rsa AAAAB3NzaC1...",
  ```

3. <span data-ttu-id="dd7ef-266">執行 `azbb` 以部署範例 VM，如下所示。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-266">Run `azbb` to deploy the sample VM as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

<span data-ttu-id="dd7ef-267">若要驗證部署，請執行下列 Azure CLI 命令，以尋找 VM 的公用 IP 位址：</span><span class="sxs-lookup"><span data-stu-id="dd7ef-267">To verify the deployment, run the following Azure CLI command to find the public IP address of the VM:</span></span>

```bash
az vm show -n ra-single-linux-vm1 -g <resource-group-name> -d -o table
```

<span data-ttu-id="dd7ef-268">如果您在網頁瀏覽器中巡覽到這個位址，您應會看到預設的 Apache2 首頁。</span><span class="sxs-lookup"><span data-stu-id="dd7ef-268">If you navigate to this address in a web browser, you should see the default Apache2 homepage.</span></span>

<!-- links -->
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-linux]: /azure/virtual-machines/virtual-machines-linux-azure-overview
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-linux-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[azure-dns]: /azure/dns/dns-overview
[fqdn]: /azure/virtual-machines/virtual-machines-linux-portal-create-fqdn
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[managed-disks]: /azure/storage/storage-managed-disks-overview
[naming-conventions]: /azure/architecture/best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-linux-planned-maintenance
[premium-storage]: /azure/virtual-machines/linux/premium-storage
[premium-storage-supported]: /azure/virtual-machines/linux/premium-storage#supported-vms
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/security-center-intro
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-linux-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: /azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-linux-sizes
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-linux-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[0]: ./images/single-vm-diagram.png "Azure 中的單一 Linux VM"