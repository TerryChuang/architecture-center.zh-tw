---
title: "基本 Web 應用程式"
description: "在 Microsoft Azure 中執行的基本 Web 應用程式建議使用架構。"
author: MikeWasson
ms.date: 12/12/2017
cardTitle: Basic web application
ms.openlocfilehash: 598eb547f0e96ae334af391183a792637caa8631
ms.sourcegitcommit: 1c0465cea4ceb9ba9bb5e8f1a8a04d3ba2fa5acd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="basic-web-application"></a><span data-ttu-id="67b62-103">基本 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="67b62-103">Basic web application</span></span>
[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="67b62-104">針對使用 [Azure App Service][app-service] 和 [Azure SQL Database][sql-db]，此參考架構會示範一組經過證實的作法。</span><span class="sxs-lookup"><span data-stu-id="67b62-104">This reference architecture shows a set of proven practices for a web application that uses [Azure App Service][app-service] and [Azure SQL Database][sql-db].</span></span> [<span data-ttu-id="67b62-105">**部署這個解決方案。**</span><span class="sxs-lookup"><span data-stu-id="67b62-105">**Deploy this solution.**</span></span>](#deploy-the-solution)

<span data-ttu-id="67b62-106">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="67b62-106">![[0]][0]</span></span>

<span data-ttu-id="67b62-107">下載這個架構的 [Visio 檔案][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="67b62-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="67b62-108">架構</span><span class="sxs-lookup"><span data-stu-id="67b62-108">Architecture</span></span> 

> [!NOTE]
> <span data-ttu-id="67b62-109">此架構不會著重於應用程式開發，也不會假設任何特定的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="67b62-109">This architecture does not focus on application development, and does not assume any particular application framework.</span></span> <span data-ttu-id="67b62-110">目的是了解不同的 Azure 服務如何搭配在一起。</span><span class="sxs-lookup"><span data-stu-id="67b62-110">The goal is to understand how various Azure services fit together.</span></span>
>
>

<span data-ttu-id="67b62-111">此架構具有下列元件：</span><span class="sxs-lookup"><span data-stu-id="67b62-111">The architecture has the following components:</span></span>

* <span data-ttu-id="67b62-112">**資源群組**。</span><span class="sxs-lookup"><span data-stu-id="67b62-112">**Resource group**.</span></span> <span data-ttu-id="67b62-113">[資源群組](/azure/azure-resource-manager/resource-group-overview)是 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="67b62-113">A [resource group](/azure/azure-resource-manager/resource-group-overview) is a logical container for Azure resources.</span></span>

* <span data-ttu-id="67b62-114">**App Service 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="67b62-114">**App Service app**.</span></span> <span data-ttu-id="67b62-115">
            [Azure App Service][app-service] 是完全受控的平台，用於建立及部署雲端應用程式。</span><span class="sxs-lookup"><span data-stu-id="67b62-115">[Azure App Service][app-service] is a fully managed platform for creating and deploying cloud applications.</span></span>     

* <span data-ttu-id="67b62-116">**App Service 方案**。</span><span class="sxs-lookup"><span data-stu-id="67b62-116">**App Service plan**.</span></span> <span data-ttu-id="67b62-117">
            [App Service 方案][app-service-plans]提供受控虛擬機器 (VM) 來裝載您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="67b62-117">An [App Service plan][app-service-plans] provides the managed virtual machines (VMs) that host your app.</span></span> <span data-ttu-id="67b62-118">所有與方案相關聯的應用程式都會在相同的虛擬機器執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="67b62-118">All apps associated with a plan run on the same VM instances.</span></span>

* <span data-ttu-id="67b62-119">**部署位置**。</span><span class="sxs-lookup"><span data-stu-id="67b62-119">**Deployment slots**.</span></span>  <span data-ttu-id="67b62-120">[部署位置][deployment-slots]可讓您預先準備好部署，然後將其與生產環境部署交換。</span><span class="sxs-lookup"><span data-stu-id="67b62-120">A [deployment slot][deployment-slots] lets you stage a deployment and then swap it with the production deployment.</span></span> <span data-ttu-id="67b62-121">這樣一來，您可以避免直接在生產環境中部署。</span><span class="sxs-lookup"><span data-stu-id="67b62-121">That way, you avoid deploying directly into production.</span></span> <span data-ttu-id="67b62-122">如需特定建議事項，請參閱[可管理性](#manageability-considerations)一節。</span><span class="sxs-lookup"><span data-stu-id="67b62-122">See the [Manageability](#manageability-considerations) section for specific recommendations.</span></span>

* <span data-ttu-id="67b62-123">**IP 位址**。</span><span class="sxs-lookup"><span data-stu-id="67b62-123">**IP address**.</span></span> <span data-ttu-id="67b62-124">App Service 應用程式具有公用 IP 位址和網域名稱。</span><span class="sxs-lookup"><span data-stu-id="67b62-124">The App Service app has a public IP address and a domain name.</span></span> <span data-ttu-id="67b62-125">網域名稱是 `azurewebsites.net` 的子網域，例如 `contoso.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="67b62-125">The domain name is a subdomain of `azurewebsites.net`, such as `contoso.azurewebsites.net`.</span></span>  

* <span data-ttu-id="67b62-126">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="67b62-126">**Azure DNS**.</span></span> <span data-ttu-id="67b62-127">[Azure DNS][azure-dns] 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。</span><span class="sxs-lookup"><span data-stu-id="67b62-127">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="67b62-128">只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="67b62-128">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span> <span data-ttu-id="67b62-129">若要使用自訂網域名稱 (例如 `contoso.com`)，請建立 DNS 記錄，將自訂網域名稱對應至 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="67b62-129">To use a custom domain name (such as `contoso.com`) create DNS records that map the custom domain name to the IP address.</span></span> <span data-ttu-id="67b62-130">如需詳細資訊，請參閱 [在 Azure App Service 中設定自訂網域名稱][custom-domain-name]。</span><span class="sxs-lookup"><span data-stu-id="67b62-130">For more information, see [Configure a custom domain name in Azure App Service][custom-domain-name].</span></span>  

* <span data-ttu-id="67b62-131">**Azure SQL Database**。</span><span class="sxs-lookup"><span data-stu-id="67b62-131">**Azure SQL Database**.</span></span> <span data-ttu-id="67b62-132">[SQL Database][sql-db] 是雲端中的關聯式資料庫即服務。</span><span class="sxs-lookup"><span data-stu-id="67b62-132">[SQL Database][sql-db] is a relational database-as-a-service in the cloud.</span></span>

* <span data-ttu-id="67b62-133">**邏輯伺服器**。</span><span class="sxs-lookup"><span data-stu-id="67b62-133">**Logical server**.</span></span> <span data-ttu-id="67b62-134">在 Azure SQL Database 中，邏輯伺服器會裝載您的資料庫。</span><span class="sxs-lookup"><span data-stu-id="67b62-134">In Azure SQL Database, a logical server hosts your databases.</span></span> <span data-ttu-id="67b62-135">您可以為每部邏輯伺服器建立多個資料庫。</span><span class="sxs-lookup"><span data-stu-id="67b62-135">You can create multiple databases per logical server.</span></span>

* <span data-ttu-id="67b62-136">**Azure 儲存體**。</span><span class="sxs-lookup"><span data-stu-id="67b62-136">**Azure Storage**.</span></span> <span data-ttu-id="67b62-137">建立包含 Blob 容器的 Azure 儲存體帳戶，以儲存診斷記錄。</span><span class="sxs-lookup"><span data-stu-id="67b62-137">Create an Azure storage account with a blob container to store diagnostic logs.</span></span>

* <span data-ttu-id="67b62-138">**Azure Active Directory** (Azure AD)。</span><span class="sxs-lookup"><span data-stu-id="67b62-138">**Azure Active Directory** (Azure AD).</span></span> <span data-ttu-id="67b62-139">使用 Azure AD 或其他識別提供者進行驗證。</span><span class="sxs-lookup"><span data-stu-id="67b62-139">Use Azure AD or another identity provider for authentication.</span></span>

## <a name="recommendations"></a><span data-ttu-id="67b62-140">建議</span><span class="sxs-lookup"><span data-stu-id="67b62-140">Recommendations</span></span>

<span data-ttu-id="67b62-141">您的需求可能和此處所述的架構不同。</span><span class="sxs-lookup"><span data-stu-id="67b62-141">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="67b62-142">以本節的建議作為起點。</span><span class="sxs-lookup"><span data-stu-id="67b62-142">Use the recommendations in this section as a starting point.</span></span>

### <a name="app-service-plan"></a><span data-ttu-id="67b62-143">App Service 方案</span><span class="sxs-lookup"><span data-stu-id="67b62-143">App Service plan</span></span>
<span data-ttu-id="67b62-144">使用標準或進階層，因為其支援相應放大、自動調整規模和安全通訊端層 (SSL)。</span><span class="sxs-lookup"><span data-stu-id="67b62-144">Use the Standard or Premium tiers, because they support scale out, autoscale, and secure sockets layer (SSL).</span></span> <span data-ttu-id="67b62-145">每個層皆支援數個*執行個體大小* (因核心和記憶體數量不同而有所差異)。</span><span class="sxs-lookup"><span data-stu-id="67b62-145">Each tier supports several *instance sizes* that differ by number of cores and memory.</span></span> <span data-ttu-id="67b62-146">建立方案之後，您可以變更層或執行個體的大小。</span><span class="sxs-lookup"><span data-stu-id="67b62-146">You can change the tier or instance size after you create a plan.</span></span> <span data-ttu-id="67b62-147">如需 App Service 方案的詳細資訊，請參閱 [App Service 價格][app-service-plans-tiers]。</span><span class="sxs-lookup"><span data-stu-id="67b62-147">For more information about App Service plans, see [App Service Pricing][app-service-plans-tiers].</span></span>

<span data-ttu-id="67b62-148">您必須支付 App Service 方案中的執行個體費用，即使該應用程式已停用。</span><span class="sxs-lookup"><span data-stu-id="67b62-148">You are charged for the instances in the App Service plan, even if the app is stopped.</span></span> <span data-ttu-id="67b62-149">請務必刪除您不使用的方案 (例如測試部署)。</span><span class="sxs-lookup"><span data-stu-id="67b62-149">Make sure to delete plans that you aren't using (for example, test deployments).</span></span>

### <a name="sql-database"></a><span data-ttu-id="67b62-150">SQL Database</span><span class="sxs-lookup"><span data-stu-id="67b62-150">SQL Database</span></span>
<span data-ttu-id="67b62-151">使用 SQL Database [V12 版][sql-db-v12]。</span><span class="sxs-lookup"><span data-stu-id="67b62-151">Use the [V12 version][sql-db-v12] of SQL Database.</span></span> <span data-ttu-id="67b62-152">SQL Database 支援基本、標準和進階[服務層][sql-db-service-tiers]，且各層內皆有多個以[資料庫交易單位 (DTU)][sql-dtu] 計量的效能層級。</span><span class="sxs-lookup"><span data-stu-id="67b62-152">SQL Database supports Basic, Standard, and Premium [service tiers][sql-db-service-tiers], with multiple performance levels within each tier measured in [Database Transaction Units (DTUs)][sql-dtu].</span></span> <span data-ttu-id="67b62-153">進行容量規劃，然後選擇符合您需求的層和效能層級。</span><span class="sxs-lookup"><span data-stu-id="67b62-153">Perform capacity planning and choose a tier and performance level that meets your requirements.</span></span>

### <a name="region"></a><span data-ttu-id="67b62-154">區域</span><span class="sxs-lookup"><span data-stu-id="67b62-154">Region</span></span>
<span data-ttu-id="67b62-155">將 App Service 方案和 SQL Database 佈建於相同區域，可使網路延遲降至最低。</span><span class="sxs-lookup"><span data-stu-id="67b62-155">Provision the App Service plan and the SQL Database in the same region to minimize network latency.</span></span> <span data-ttu-id="67b62-156">通常會選擇最接近使用者的區域。</span><span class="sxs-lookup"><span data-stu-id="67b62-156">Generally, choose the region closest to your users.</span></span>

<span data-ttu-id="67b62-157">資源群組也會有區域，該區域會指定儲存部署中繼資料的位置。</span><span class="sxs-lookup"><span data-stu-id="67b62-157">The resource group also has a region, which specifies where deployment metadata is stored.</span></span> <span data-ttu-id="67b62-158">將資源群組及其資源放置於相同區域。</span><span class="sxs-lookup"><span data-stu-id="67b62-158">Put the resource group and its resources in the same region.</span></span> <span data-ttu-id="67b62-159">這麼做可在部署期間提升可用性。</span><span class="sxs-lookup"><span data-stu-id="67b62-159">This can improve availability during deployment.</span></span> 

## <a name="scalability-considerations"></a><span data-ttu-id="67b62-160">延展性考量</span><span class="sxs-lookup"><span data-stu-id="67b62-160">Scalability considerations</span></span>

<span data-ttu-id="67b62-161">Azure App Service 的主要優點是能夠根據負載調整應用程式規模。</span><span class="sxs-lookup"><span data-stu-id="67b62-161">A major benefit of Azure App Service is the ability to scale your application based on load.</span></span> <span data-ttu-id="67b62-162">以下是規劃調整應用程式時，應記住的一些考量。</span><span class="sxs-lookup"><span data-stu-id="67b62-162">Here are some considerations to keep in mind when planning to scale your application.</span></span>

### <a name="scaling-the-app-service-app"></a><span data-ttu-id="67b62-163">調整 App Service 應用程式的規模</span><span class="sxs-lookup"><span data-stu-id="67b62-163">Scaling the App Service app</span></span>

<span data-ttu-id="67b62-164">有兩種方式可用來調整 App Service 應用程式的規模：</span><span class="sxs-lookup"><span data-stu-id="67b62-164">There are two ways to scale an App Service app:</span></span>

* <span data-ttu-id="67b62-165">*相應增加*，表示變更執行個體的大小。</span><span class="sxs-lookup"><span data-stu-id="67b62-165">*Scale up*, which means changing the instance size.</span></span> <span data-ttu-id="67b62-166">執行個體大小會決定每個虛擬機器執行個體上的記憶體、核心數及儲存體。</span><span class="sxs-lookup"><span data-stu-id="67b62-166">The instance size determines the memory, number of cores, and storage on each VM instance.</span></span> <span data-ttu-id="67b62-167">您可以手動變更執行個體大小或方案層，來進行相應增加。</span><span class="sxs-lookup"><span data-stu-id="67b62-167">You can scale up manually by changing the instance size or the plan tier.</span></span>  

* <span data-ttu-id="67b62-168">*相應放大*，表示新增執行個體以處理增加的負載。</span><span class="sxs-lookup"><span data-stu-id="67b62-168">*Scale out*, which means adding instances to handle increased load.</span></span> <span data-ttu-id="67b62-169">每個定價層都有執行個體的數目上限。</span><span class="sxs-lookup"><span data-stu-id="67b62-169">Each pricing tier has a maximum number of instances.</span></span> 

  <span data-ttu-id="67b62-170">您可以變更執行個體計數來手動進行相應放大，或使用[自動調整][web-app-autoscale]，讓 Azure 根據排程和/或效能計量，自動新增或移除執行個體。</span><span class="sxs-lookup"><span data-stu-id="67b62-170">You can scale out manually by changing the instance count, or use [autoscaling][web-app-autoscale] to have Azure automatically add or remove instances based on a schedule and/or performance metrics.</span></span> <span data-ttu-id="67b62-171">每個縮放作業通常會在數秒內快速生效。</span><span class="sxs-lookup"><span data-stu-id="67b62-171">Each scale operation happens quickly&mdash;typically within seconds.</span></span> 

  <span data-ttu-id="67b62-172">若要啟用自動調整，請建立自動調整設定檔，其中會定義執行個體數量的下限和上限。</span><span class="sxs-lookup"><span data-stu-id="67b62-172">To enable autoscaling, create an autoscale *profile* that defines the minimum and maximum number of instances.</span></span> <span data-ttu-id="67b62-173">您可以排程設定檔。</span><span class="sxs-lookup"><span data-stu-id="67b62-173">Profiles can be scheduled.</span></span> <span data-ttu-id="67b62-174">例如，您可以針對工作日和週末各別建立設定檔。</span><span class="sxs-lookup"><span data-stu-id="67b62-174">For example, you might create separate profiles for weekdays and weekends.</span></span> <span data-ttu-id="67b62-175">設定檔可包含何時新增或移除執行個體的規則 (選擇性)。</span><span class="sxs-lookup"><span data-stu-id="67b62-175">Optionally, a profile contains rules for when to add or remove instances.</span></span> <span data-ttu-id="67b62-176">(例如：如果 CPU 使用量超過 70% 達 5 分鐘之久，則新增兩個執行個體。)</span><span class="sxs-lookup"><span data-stu-id="67b62-176">(Example: Add two instances if CPU usage is above 70% for 5 minutes.)</span></span>
  
<span data-ttu-id="67b62-177">調整 Web 應用程式規模的建議事項：</span><span class="sxs-lookup"><span data-stu-id="67b62-177">Recommendations for scaling a web app:</span></span>

* <span data-ttu-id="67b62-178">盡可能避免進行相應增加和減少，因為這樣可能會觸發應用程式重新啟動。</span><span class="sxs-lookup"><span data-stu-id="67b62-178">As much as possible, avoid scaling up and down, because it may trigger an application restart.</span></span> <span data-ttu-id="67b62-179">相反地，選取在一般負載下符合您效能需求的層和大小，然後相應放大執行個體來處理流量的變更。</span><span class="sxs-lookup"><span data-stu-id="67b62-179">Instead, select a tier and size that meet your performance requirements under typical load and then scale out the instances to handle changes in traffic volume.</span></span>    
* <span data-ttu-id="67b62-180">啟用自動調整。</span><span class="sxs-lookup"><span data-stu-id="67b62-180">Enable autoscaling.</span></span> <span data-ttu-id="67b62-181">如果您的應用程式具有可預測的規律工作負載，可建立設定檔以預先排定執行個體計數。</span><span class="sxs-lookup"><span data-stu-id="67b62-181">If your application has a predictable, regular workload, create profiles to schedule the instance counts ahead of time.</span></span> <span data-ttu-id="67b62-182">如果工作負載不可預測，可使用規則式自動調整來因應負載的變更。</span><span class="sxs-lookup"><span data-stu-id="67b62-182">If the workload is not predictable, use rule-based autoscaling to react to changes in load as they occur.</span></span> <span data-ttu-id="67b62-183">您可以結合這兩種方法。</span><span class="sxs-lookup"><span data-stu-id="67b62-183">You can combine both approaches.</span></span>
* <span data-ttu-id="67b62-184">CPU 使用率通常很適合作為自動調整規則的計量。</span><span class="sxs-lookup"><span data-stu-id="67b62-184">CPU usage is generally a good metric for autoscale rules.</span></span> <span data-ttu-id="67b62-185">不過，您應對您的應用程式進行負載測試，找出潛在的瓶頸，並根據該資料設定自動調整規則。</span><span class="sxs-lookup"><span data-stu-id="67b62-185">However, you should load test your application, identify potential bottlenecks, and base your autoscale rules on that data.</span></span>  
* <span data-ttu-id="67b62-186">自動調整規則包含「冷卻」期間，也就是調整動作完成後，再啟動新調整動作前的等候間隔。</span><span class="sxs-lookup"><span data-stu-id="67b62-186">Autoscale rules include a *cool-down* period, which is the interval to wait after a scale action has completed before starting a new scale action.</span></span> <span data-ttu-id="67b62-187">冷卻期間會讓系統穩定後，才能再次進行調整。</span><span class="sxs-lookup"><span data-stu-id="67b62-187">The cool-down period lets the system stabilize before scaling again.</span></span> <span data-ttu-id="67b62-188">針對新增執行個體設定較短的冷卻期間，以及針對移除執行個體設定較長的冷卻期間。</span><span class="sxs-lookup"><span data-stu-id="67b62-188">Set a shorter cool-down period for adding instances, and a longer cool-down period for removing instances.</span></span> <span data-ttu-id="67b62-189">例如，為新增執行個體設定 5 分鐘，但為移除執行個體設定 60 分鐘。</span><span class="sxs-lookup"><span data-stu-id="67b62-189">For example, set 5 minutes to add an instance, but 60 minutes to remove an instance.</span></span> <span data-ttu-id="67b62-190">最好在負載過重時快速增加新執行個體來處理額外的流量，然後逐漸減少執行個體。</span><span class="sxs-lookup"><span data-stu-id="67b62-190">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

### <a name="scaling-sql-database"></a><span data-ttu-id="67b62-191">調整 SQL Database 的規模</span><span class="sxs-lookup"><span data-stu-id="67b62-191">Scaling SQL Database</span></span>
<span data-ttu-id="67b62-192">如果您的 SQL Database 需要較高服務層或效能層級，您可以相應增加個別資料庫，且無須停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="67b62-192">If you need a higher service tier or performance level for SQL Database, you can scale up individual databases with no application downtime.</span></span> <span data-ttu-id="67b62-193">如需詳細資訊，請參閱 [SQL Database 選項和效能：了解每個服務層中可用的項目][sql-db-scale]。</span><span class="sxs-lookup"><span data-stu-id="67b62-193">For more information, see [SQL Database options and performance: Understand what's available in each service tier][sql-db-scale].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="67b62-194">可用性考量</span><span class="sxs-lookup"><span data-stu-id="67b62-194">Availability considerations</span></span>
<span data-ttu-id="67b62-195">本文撰寫時，App Service 的服務等級協定 (SLA) 為 99.95%，而 SQL Database 的 SLA 為 99.99%，適用於基本、標準和進階層。</span><span class="sxs-lookup"><span data-stu-id="67b62-195">At the time of writing, the service level agreement (SLA) for App Service is 99.95% and the SLA for SQL Database is 99.99% for Basic, Standard, and Premium tiers.</span></span> 

> [!NOTE]
> <span data-ttu-id="67b62-196">App Service SLA 適用於單一和多重執行個體。</span><span class="sxs-lookup"><span data-stu-id="67b62-196">The App Service SLA applies to both single and multiple instances.</span></span>  
>
>

### <a name="backups"></a><span data-ttu-id="67b62-197">備份</span><span class="sxs-lookup"><span data-stu-id="67b62-197">Backups</span></span>
<span data-ttu-id="67b62-198">如果發生資料遺失，SQL Database 會提供還原時間點和異地還原。</span><span class="sxs-lookup"><span data-stu-id="67b62-198">In the event of data loss, SQL Database provides point-in-time restore and geo-restore.</span></span> <span data-ttu-id="67b62-199">這些功能適用於所有層，而且會自動啟用。</span><span class="sxs-lookup"><span data-stu-id="67b62-199">These features are available in all tiers and are automatically enabled.</span></span> <span data-ttu-id="67b62-200">您不需要排程或管理備份。</span><span class="sxs-lookup"><span data-stu-id="67b62-200">You don't need to schedule or manage the backups.</span></span> 

- <span data-ttu-id="67b62-201">使用還原時間點將資料庫回復至較早的時間點，以[從人為錯誤中復原][sql-human-error]。</span><span class="sxs-lookup"><span data-stu-id="67b62-201">Use point-in-time restore to [recover from human error][sql-human-error] by returning the database to an earlier point in time.</span></span> 
- <span data-ttu-id="67b62-202">使用異地還原從異地備援儲存體中還原資料庫，以[從服務中斷中復原][sql-outage-recovery]。</span><span class="sxs-lookup"><span data-stu-id="67b62-202">Use geo-restore to [recover from a service outage][sql-outage-recovery] by restoring a database from a geo-redundant backup.</span></span> 

<span data-ttu-id="67b62-203">如需詳細資訊，請參閱 [雲端商務持續性和 SQL Database 的資料庫災害復原][sql-backup]。</span><span class="sxs-lookup"><span data-stu-id="67b62-203">For more information, see [Cloud business continuity and database disaster recovery with SQL Database][sql-backup].</span></span>

<span data-ttu-id="67b62-204">App Service 提供[備份和還原][web-app-backup]應用程式檔案的功能。</span><span class="sxs-lookup"><span data-stu-id="67b62-204">App Service provides a [backup and restore][web-app-backup] feature for your application files.</span></span> <span data-ttu-id="67b62-205">不過，請注意，備份檔案包含純文字的應用程式設定，且這些設定可能包含祕密，例如連接字串。</span><span class="sxs-lookup"><span data-stu-id="67b62-205">However, be aware that the backed-up files include app settings in plain text and these may include secrets, such as connection strings.</span></span> <span data-ttu-id="67b62-206">請避免使用 App Service 的備份功能來備份 SQL 資料庫，因為它會將資料庫匯出為 SQL.bacpac 檔案，如此會耗用 [DTU][sql-dtu]。</span><span class="sxs-lookup"><span data-stu-id="67b62-206">Avoid using the App Service backup feature to back up your SQL databases because it exports the database to a SQL .bacpac file, consuming [DTUs][sql-dtu].</span></span> <span data-ttu-id="67b62-207">請改用上述的 SQL Database 還原時間點。</span><span class="sxs-lookup"><span data-stu-id="67b62-207">Instead, use SQL Database point-in-time restore described above.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="67b62-208">管理性考量</span><span class="sxs-lookup"><span data-stu-id="67b62-208">Manageability considerations</span></span>
<span data-ttu-id="67b62-209">針對生產、部署和測試環境，各別建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="67b62-209">Create separate resource groups for production, development, and test environments.</span></span> <span data-ttu-id="67b62-210">這可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。</span><span class="sxs-lookup"><span data-stu-id="67b62-210">This makes it easier to manage deployments, delete test deployments, and assign access rights.</span></span>

<span data-ttu-id="67b62-211">將資源指派給資源群組時，請考慮下列項目：</span><span class="sxs-lookup"><span data-stu-id="67b62-211">When assigning resources to resource groups, consider the following:</span></span>

* <span data-ttu-id="67b62-212">生命週期。</span><span class="sxs-lookup"><span data-stu-id="67b62-212">Lifecycle.</span></span> <span data-ttu-id="67b62-213">一般來說，會將生命週期相同的資源放在同個資源群組中。</span><span class="sxs-lookup"><span data-stu-id="67b62-213">In general, put resources with the same lifecycle into the same resource group.</span></span>
* <span data-ttu-id="67b62-214">存取。</span><span class="sxs-lookup"><span data-stu-id="67b62-214">Access.</span></span> <span data-ttu-id="67b62-215">您可以使用[角色型存取控制][rbac] (RBAC) 將存取原則套用至群組中的資源。</span><span class="sxs-lookup"><span data-stu-id="67b62-215">You can use [role-based access control][rbac] (RBAC) to apply access policies to the resources in a group.</span></span>
* <span data-ttu-id="67b62-216">計費。</span><span class="sxs-lookup"><span data-stu-id="67b62-216">Billing.</span></span> <span data-ttu-id="67b62-217">您可以檢視資源群組的彙總成本。</span><span class="sxs-lookup"><span data-stu-id="67b62-217">You can view the rolled-up costs for the resource group.</span></span>  

<span data-ttu-id="67b62-218">如需詳細資訊，請參閱 [Azure Resource Manager 概觀](/azure/azure-resource-manager/resource-group-overview)。</span><span class="sxs-lookup"><span data-stu-id="67b62-218">For more information, see [Azure Resource Manager overview](/azure/azure-resource-manager/resource-group-overview).</span></span>

### <a name="deployment"></a><span data-ttu-id="67b62-219">Deployment</span><span class="sxs-lookup"><span data-stu-id="67b62-219">Deployment</span></span>
<span data-ttu-id="67b62-220">部署包含兩個步驟：</span><span class="sxs-lookup"><span data-stu-id="67b62-220">Deployment involves two steps:</span></span>

1. <span data-ttu-id="67b62-221">佈建 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="67b62-221">Provisioning the Azure resources.</span></span> <span data-ttu-id="67b62-222">對於此步驟，我們建議您使用 [Azure Resoure Manager 範本][arm-template]。</span><span class="sxs-lookup"><span data-stu-id="67b62-222">We recommend that you use [Azure Resoure Manager templates][arm-template] for this step.</span></span> <span data-ttu-id="67b62-223">範本可讓您輕鬆地透過 PowerShell 或 Azure 命令列介面 (CLI) 進行自動部署。</span><span class="sxs-lookup"><span data-stu-id="67b62-223">Templates make it easier to automate deployments via PowerShell or the Azure command line interface (CLI).</span></span>
2. <span data-ttu-id="67b62-224">部署應用程式 (程式碼、二進位檔和內容檔案)。</span><span class="sxs-lookup"><span data-stu-id="67b62-224">Deploying the application (code, binaries, and content files).</span></span> <span data-ttu-id="67b62-225">您有數個選項，包括從本機 Git 存放庫進行部署、使用 Visual Studio 或從雲端式原始檔控制進行連續部署。</span><span class="sxs-lookup"><span data-stu-id="67b62-225">You have several options, including deploying from a local Git repository, using Visual Studio, or continuous deployment from cloud-based source control.</span></span> <span data-ttu-id="67b62-226">請參閱[將您的應用程式部署至 Azure App Service][deploy]。</span><span class="sxs-lookup"><span data-stu-id="67b62-226">See [Deploy your app to Azure App Service][deploy].</span></span>  

<span data-ttu-id="67b62-227">App Service 應用程式一律會有一個名為 `production` 的部署位置，代表實際作用的生產網站。</span><span class="sxs-lookup"><span data-stu-id="67b62-227">An App Service app always has one deployment slot named `production`, which represents the live production site.</span></span> <span data-ttu-id="67b62-228">我們建議您為部署更新建立預備位置。</span><span class="sxs-lookup"><span data-stu-id="67b62-228">We recommend creating a staging slot for deploying updates.</span></span> <span data-ttu-id="67b62-229">使用預備位置的優點包括：</span><span class="sxs-lookup"><span data-stu-id="67b62-229">The benefits of using a staging slot include:</span></span>

* <span data-ttu-id="67b62-230">您可以確認部署成功後，再交換至生產環境。</span><span class="sxs-lookup"><span data-stu-id="67b62-230">You can verify the deployment succeeded, before swapping it into production.</span></span>
* <span data-ttu-id="67b62-231">部署至預備位置，可確保所有執行個體在交換至生產環境之前就已準備就緒。</span><span class="sxs-lookup"><span data-stu-id="67b62-231">Deploying to a staging slot ensures that all instances are warmed up before being swapped into production.</span></span> <span data-ttu-id="67b62-232">許多應用程式都有重要的暖機和冷啟動時間。</span><span class="sxs-lookup"><span data-stu-id="67b62-232">Many applications have a significant warmup and cold-start time.</span></span>

<span data-ttu-id="67b62-233">我們也建議您建立第三個位置來保存上一次的正確部署。</span><span class="sxs-lookup"><span data-stu-id="67b62-233">We also recommend creating a third slot to hold the last-known-good deployment.</span></span> <span data-ttu-id="67b62-234">當您交換預備環境和生產環境之後，請將先前的生產環境部署 (現在處於預備環境) 移到「上一次的正確部署」位置。</span><span class="sxs-lookup"><span data-stu-id="67b62-234">After you swap staging and production, move the previous production deployment (which is now in staging) into the last-known-good slot.</span></span> <span data-ttu-id="67b62-235">這樣一來，如果您之後發現問題，您可以快速還原至上一次的正確版本。</span><span class="sxs-lookup"><span data-stu-id="67b62-235">That way, if you discover a problem later, you can quickly revert to the last-known-good version.</span></span>

<span data-ttu-id="67b62-236">![[1]][1]</span><span class="sxs-lookup"><span data-stu-id="67b62-236">![[1]][1]</span></span>

<span data-ttu-id="67b62-237">如果還原至之前的版本，請確定任何資料庫的結構描述變更都可以與舊版相容。</span><span class="sxs-lookup"><span data-stu-id="67b62-237">If you revert to a previous version, make sure any database schema changes are backward compatible.</span></span>

<span data-ttu-id="67b62-238">請勿使用生產環境部署上的位置進行測試，因為同個 App Service 方案內的所有應用程式會共用相同的虛擬機器執行個體。</span><span class="sxs-lookup"><span data-stu-id="67b62-238">Don't use slots on your production deployment for testing because all apps within the same App Service plan share the same VM instances.</span></span> <span data-ttu-id="67b62-239">例如，負載測試可能會使實際作用的生產網站降級。</span><span class="sxs-lookup"><span data-stu-id="67b62-239">For example, load tests might degrade the live production site.</span></span> <span data-ttu-id="67b62-240">針對生產和測試，請建立不同的 App Service 方案。</span><span class="sxs-lookup"><span data-stu-id="67b62-240">Instead, create separate App Service plans for production and test.</span></span> <span data-ttu-id="67b62-241">透過將測試部署放在不同方案內，您可以使其與生產版本隔離。</span><span class="sxs-lookup"><span data-stu-id="67b62-241">By putting test deployments into a separate plan, you isolate them from the production version.</span></span>

### <a name="configuration"></a><span data-ttu-id="67b62-242">組態</span><span class="sxs-lookup"><span data-stu-id="67b62-242">Configuration</span></span>
<span data-ttu-id="67b62-243">儲存作為[應用程式設定][app-settings]的組態設定。</span><span class="sxs-lookup"><span data-stu-id="67b62-243">Store configuration settings as [app settings][app-settings].</span></span> <span data-ttu-id="67b62-244">在您的 Resource Manager 範本中或使用 PowerShell 定義應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="67b62-244">Define the app settings in your Resource Manager templates, or using PowerShell.</span></span> <span data-ttu-id="67b62-245">在執行階段上，應用程式設定可作為應用程式的環境變數。</span><span class="sxs-lookup"><span data-stu-id="67b62-245">At runtime, app settings are available to the application as environment variables.</span></span>

<span data-ttu-id="67b62-246">永遠不會將密碼、存取金鑰或連接字串簽入原始檔控制。</span><span class="sxs-lookup"><span data-stu-id="67b62-246">Never check passwords, access keys, or connection strings into source control.</span></span> <span data-ttu-id="67b62-247">而是會將這些項目作為參數傳遞至部署指令碼，將這些值儲存為應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="67b62-247">Instead, pass these as parameters to a deployment script that stores these values as app settings.</span></span>

<span data-ttu-id="67b62-248">依預設，當您交換部署位置時，應用程式設定也會進行交換。</span><span class="sxs-lookup"><span data-stu-id="67b62-248">When you swap a deployment slot, the app settings are swapped by default.</span></span> <span data-ttu-id="67b62-249">如果生產環境和預備環境需要不同設定，您可以建立衷於位置且不要進行交換的應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="67b62-249">If you need different settings for production and staging, you can create app settings that stick to a slot and don't get swapped.</span></span>

### <a name="diagnostics-and-monitoring"></a><span data-ttu-id="67b62-250">診斷和監控</span><span class="sxs-lookup"><span data-stu-id="67b62-250">Diagnostics and monitoring</span></span>
<span data-ttu-id="67b62-251">啟用[診斷記錄][diagnostic-logs]，包括應用程式記錄和 Web 伺服器記錄。</span><span class="sxs-lookup"><span data-stu-id="67b62-251">Enable [diagnostics logging][diagnostic-logs], including application logging and web server logging.</span></span> <span data-ttu-id="67b62-252">將記錄設定為使用 Blob 儲存體。</span><span class="sxs-lookup"><span data-stu-id="67b62-252">Configure logging to use Blob storage.</span></span> <span data-ttu-id="67b62-253">基於效能考量，請針對診斷記錄建立個別的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="67b62-253">For performance reasons, create a separate storage account for diagnostic logs.</span></span> <span data-ttu-id="67b62-254">記錄和應用程式資料不可使用相同儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="67b62-254">Don't use the same storage account for logs and application data.</span></span> <span data-ttu-id="67b62-255">如需記錄的詳細指引，請參閱[監視和診斷指引][monitoring-guidance]。</span><span class="sxs-lookup"><span data-stu-id="67b62-255">For more detailed guidance on logging, see [Monitoring and diagnostics guidance][monitoring-guidance].</span></span>

<span data-ttu-id="67b62-256">使用 [New Relic][new-relic] 或 [Application Insights][app-insights] 來監視負載下的應用程式效能和行為。</span><span class="sxs-lookup"><span data-stu-id="67b62-256">Use a service such as [New Relic][new-relic] or [Application Insights][app-insights] to monitor application performance and behavior under load.</span></span> <span data-ttu-id="67b62-257">請留意 Application insights 的[資料速率限制][app-insights-data-rate]。</span><span class="sxs-lookup"><span data-stu-id="67b62-257">Be aware of the [data rate limits][app-insights-data-rate] for Application Insights.</span></span>

<span data-ttu-id="67b62-258">使用 [Visual Studio Team Services][vsts] 等工具執行負載測試。</span><span class="sxs-lookup"><span data-stu-id="67b62-258">Perform load testing, using a tool such as [Visual Studio Team Services][vsts].</span></span> <span data-ttu-id="67b62-259">如需雲端應用程式中的一般效能分析概觀，請參閱[效能分析入門][perf-analysis]。</span><span class="sxs-lookup"><span data-stu-id="67b62-259">For a general overview of performance analysis in cloud applications, see [Performance Analysis Primer][perf-analysis].</span></span>

<span data-ttu-id="67b62-260">針對應用程式進行疑難排解的秘訣：</span><span class="sxs-lookup"><span data-stu-id="67b62-260">Tips for troubleshooting your application:</span></span>

* <span data-ttu-id="67b62-261">在 Azure 入口網站中使用 [[疑難排解] 刀鋒視窗][troubleshoot-blade]，來尋找常見問題的解決方案。</span><span class="sxs-lookup"><span data-stu-id="67b62-261">Use the [troubleshoot blade][troubleshoot-blade] in the Azure portal to find solutions to common problems.</span></span>
* <span data-ttu-id="67b62-262">啟用[記錄串流][web-app-log-stream]，可讓您近乎即時地查看記錄資訊。</span><span class="sxs-lookup"><span data-stu-id="67b62-262">Enable [log streaming][web-app-log-stream] to see logging information in near-real time.</span></span>
* <span data-ttu-id="67b62-263">[Kudu 儀表板][kudu]有數個工具可用來監視和偵錯您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="67b62-263">The [Kudu dashboard][kudu] has several tools for monitoring and debugging your application.</span></span> <span data-ttu-id="67b62-264">如需詳細資訊，請參 [您應該知道的 Azure 網站線上工具][kudu] (部落格文章)。</span><span class="sxs-lookup"><span data-stu-id="67b62-264">For more information, see [Azure Websites online tools you should know about][kudu] (blog post).</span></span> <span data-ttu-id="67b62-265">您可以從 Azure 入口網站連線至 Kudu 儀表板。</span><span class="sxs-lookup"><span data-stu-id="67b62-265">You can reach the Kudu dashboard from the Azure portal.</span></span> <span data-ttu-id="67b62-266">開啟您應用程式的刀鋒視窗，按一下 [工具]，然後按一下 [Kudu]。</span><span class="sxs-lookup"><span data-stu-id="67b62-266">Open the blade for your app and click **Tools**, then click **Kudu**.</span></span>
* <span data-ttu-id="67b62-267">如果您使用 Visual Studio，請參閱[使用 Visual Studio 為 Azure App Service 中的 Web 應用程式進行疑難排解][troubleshoot-web-app]，來取得偵錯和疑難排解的秘訣。</span><span class="sxs-lookup"><span data-stu-id="67b62-267">If you use Visual Studio, see the article [Troubleshoot a web app in Azure App Service using Visual Studio][troubleshoot-web-app] for debugging and troubleshooting tips.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="67b62-268">安全性考量</span><span class="sxs-lookup"><span data-stu-id="67b62-268">Security considerations</span></span>
<span data-ttu-id="67b62-269">本節列出本文所述的 Azure 服務專屬的安全性考量，</span><span class="sxs-lookup"><span data-stu-id="67b62-269">This section lists security considerations that are specific to the Azure services described in this article.</span></span> <span data-ttu-id="67b62-270">但不是安全性最佳做法的完整清單。</span><span class="sxs-lookup"><span data-stu-id="67b62-270">It's not a complete list of security best practices.</span></span> <span data-ttu-id="67b62-271">如需更多的安全性考量，請參閱[保護 Azure App Service 中的應用程式][app-service-security]。</span><span class="sxs-lookup"><span data-stu-id="67b62-271">For some additional security considerations, see [Secure an app in Azure App Service][app-service-security].</span></span>

### <a name="sql-database-auditing"></a><span data-ttu-id="67b62-272">SQL Database 稽核</span><span class="sxs-lookup"><span data-stu-id="67b62-272">SQL Database auditing</span></span>
<span data-ttu-id="67b62-273">稽核可協助您保持法規遵循，以及深入了解可指出商務考量或疑似安全違規的不一致和不合規行為。</span><span class="sxs-lookup"><span data-stu-id="67b62-273">Auditing can help you maintain regulatory compliance and get insight into discrepancies and irregularities that could indicate business concerns or suspected security violations.</span></span> <span data-ttu-id="67b62-274">請參閱[開始使用 SQL Database 稽核][sql-audit]。</span><span class="sxs-lookup"><span data-stu-id="67b62-274">See [Get started with SQL database auditing][sql-audit].</span></span>

### <a name="deployment-slots"></a><span data-ttu-id="67b62-275">部署位置</span><span class="sxs-lookup"><span data-stu-id="67b62-275">Deployment slots</span></span>
<span data-ttu-id="67b62-276">每個部署位置都具有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="67b62-276">Each deployment slot has a public IP address.</span></span> <span data-ttu-id="67b62-277">使用 [Azure Active Directory 登入][aad-auth]可保護非生產位置，因此只有您的開發和 DevOps 小組成員可以連線至這些端點。</span><span class="sxs-lookup"><span data-stu-id="67b62-277">Secure the nonproduction slots using [Azure Active Directory login][aad-auth] so that only members of your development and DevOps teams can reach those endpoints.</span></span>

### <a name="logging"></a><span data-ttu-id="67b62-278">記錄</span><span class="sxs-lookup"><span data-stu-id="67b62-278">Logging</span></span>
<span data-ttu-id="67b62-279">記錄絕對不能記下使用者的密碼，或其他可能可用來認可身分識別詐騙的資訊。</span><span class="sxs-lookup"><span data-stu-id="67b62-279">Logs should never record users' passwords or other information that might be used to commit identity fraud.</span></span> <span data-ttu-id="67b62-280">儲存之前請清除資料中的這些詳細資料。</span><span class="sxs-lookup"><span data-stu-id="67b62-280">Scrub those details from the data before storing it.</span></span>   

### <a name="ssl"></a><span data-ttu-id="67b62-281">SSL</span><span class="sxs-lookup"><span data-stu-id="67b62-281">SSL</span></span>
<span data-ttu-id="67b62-282">App Service 應用程式包含 `azurewebsites.net` 子網域的 SSL 端點 (不需額外費用)。</span><span class="sxs-lookup"><span data-stu-id="67b62-282">An App Service app includes an SSL endpoint on a subdomain of `azurewebsites.net` at no additional cost.</span></span> <span data-ttu-id="67b62-283">SSL 端點包含 `*.azurewebsites.net` 網域的萬用字元憑證。</span><span class="sxs-lookup"><span data-stu-id="67b62-283">The SSL endpoint includes a wildcard certificate for the `*.azurewebsites.net` domain.</span></span> <span data-ttu-id="67b62-284">如果您使用自訂網域名稱，您必須提供符合自訂網域的憑證。</span><span class="sxs-lookup"><span data-stu-id="67b62-284">If you use a custom domain name, you must provide a certificate that matches the custom domain.</span></span> <span data-ttu-id="67b62-285">最簡單的方式是直接透過 Azure 入口網站購買憑證。</span><span class="sxs-lookup"><span data-stu-id="67b62-285">The simplest approach is to buy a certificate directly through the Azure portal.</span></span> <span data-ttu-id="67b62-286">您也可以從其他憑證授權單位匯入憑證。</span><span class="sxs-lookup"><span data-stu-id="67b62-286">You can also import certificates from other certificate authorities.</span></span> <span data-ttu-id="67b62-287">如需詳細資訊，請參閱[購買並設定您 Azure App Service 的 SSL 憑證][ssl-cert]。</span><span class="sxs-lookup"><span data-stu-id="67b62-287">For more information, see [Buy and Configure an SSL Certificate for your Azure App Service][ssl-cert].</span></span>

<span data-ttu-id="67b62-288">作為最佳安全性做法，您的應用程式應該透過重新導向 HTTP 要求來強制執行 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="67b62-288">As a security best practice, your app should enforce HTTPS by redirecting HTTP requests.</span></span> <span data-ttu-id="67b62-289">您可以在您的應用程式中實作此項目，或如[針對 Azure App Service 中的應用程式啟用 HTTPS][ssl-redirect]中所述使用 URL 重寫規則。</span><span class="sxs-lookup"><span data-stu-id="67b62-289">You can implement this inside your application or use a URL rewrite rule as described in [Enable HTTPS for an app in Azure App Service][ssl-redirect].</span></span>

### <a name="authentication"></a><span data-ttu-id="67b62-290">驗證</span><span class="sxs-lookup"><span data-stu-id="67b62-290">Authentication</span></span>
<span data-ttu-id="67b62-291">我們建議您透過識別提供者 (IDP) 進行驗證，例如 Azure AD、Facebook、Google 或 Twitter。</span><span class="sxs-lookup"><span data-stu-id="67b62-291">We recommend authenticating through an identity provider (IDP), such as Azure AD, Facebook, Google, or Twitter.</span></span> <span data-ttu-id="67b62-292">使用 OAuth 2 或 OpenID Connect (OIDC) 作為驗證流程。</span><span class="sxs-lookup"><span data-stu-id="67b62-292">Use OAuth 2 or OpenID Connect (OIDC) for the authentication flow.</span></span> <span data-ttu-id="67b62-293">Azure AD 提供的功能可讓您管理使用者及群組、建立應用程式角色、整合您的內部部署身分識別，以及使用 Office 365 及商務用 Skype 等後端服務。</span><span class="sxs-lookup"><span data-stu-id="67b62-293">Azure AD provides functionality to manage users and groups, create application roles, integrate your on-premises identities, and consume backend services such as Office 365 and Skype for Business.</span></span>

<span data-ttu-id="67b62-294">應避免讓應用程式直接管理使用者登入和認證，因為應用程式會產生潛在的攻擊面。</span><span class="sxs-lookup"><span data-stu-id="67b62-294">Avoid having the application manage user logins and credentials directly, as it creates a potential attack surface.</span></span>  <span data-ttu-id="67b62-295">最少，您應必須有電子郵件確認、密碼復原和多重要素驗證；驗證密碼強度並安全地儲存密碼雜湊。</span><span class="sxs-lookup"><span data-stu-id="67b62-295">At a minimum, you would need to have email confirmation, password recovery, and multi-factor authentication; validate password strength; and store password hashes securely.</span></span> <span data-ttu-id="67b62-296">大型的識別提供者會為您處理這些所有的事，並不斷地監視並改善其安全性做法。</span><span class="sxs-lookup"><span data-stu-id="67b62-296">The large identity providers handle all of those things for you, and are constantly monitoring and improving their security practices.</span></span>

<span data-ttu-id="67b62-297">請考慮使用 [App Service 服務驗證][app-service-auth]來實作 OAuth/OIDC 驗證流程。</span><span class="sxs-lookup"><span data-stu-id="67b62-297">Consider using [App Service authentication][app-service-auth] to implement the OAuth/OIDC authentication flow.</span></span> <span data-ttu-id="67b62-298">App Service 驗證的優點包括：</span><span class="sxs-lookup"><span data-stu-id="67b62-298">The benefits of App Service authentication include:</span></span>

* <span data-ttu-id="67b62-299">容易設定。</span><span class="sxs-lookup"><span data-stu-id="67b62-299">Easy to configure.</span></span>
* <span data-ttu-id="67b62-300">簡單的驗證案例不需要程式碼。</span><span class="sxs-lookup"><span data-stu-id="67b62-300">No code is required for simple authentication scenarios.</span></span>
* <span data-ttu-id="67b62-301">支援委派授權使用 OAuth 存取權杖來代表使用者使用資源。</span><span class="sxs-lookup"><span data-stu-id="67b62-301">Supports delegated authorization using OAuth access tokens to consume resources on behalf of the user.</span></span>
* <span data-ttu-id="67b62-302">提供內建權杖快取。</span><span class="sxs-lookup"><span data-stu-id="67b62-302">Provides a built-in token cache.</span></span>

<span data-ttu-id="67b62-303">App Service 驗證的部分限制：</span><span class="sxs-lookup"><span data-stu-id="67b62-303">Some limitations of App Service authentication:</span></span>  

* <span data-ttu-id="67b62-304">限制的自訂選項。</span><span class="sxs-lookup"><span data-stu-id="67b62-304">Limited customization options.</span></span>
* <span data-ttu-id="67b62-305">委派授權僅限於每個登入工作階段的一個後端資源。</span><span class="sxs-lookup"><span data-stu-id="67b62-305">Delegated authorization is restricted to one backend resource per login session.</span></span>
* <span data-ttu-id="67b62-306">如果您使用多個 IDP，則沒有任何內建機制可用於首頁領域探索。</span><span class="sxs-lookup"><span data-stu-id="67b62-306">If you use more than one IDP, there is no built-in mechanism for home realm discovery.</span></span>
* <span data-ttu-id="67b62-307">在多租用戶的情況下，應用程式必須實作邏輯以驗證權杖簽發者。</span><span class="sxs-lookup"><span data-stu-id="67b62-307">For multi-tenant scenarios, the application must implement the logic to validate the token issuer.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="67b62-308">部署解決方案</span><span class="sxs-lookup"><span data-stu-id="67b62-308">Deploy the solution</span></span>
<span data-ttu-id="67b62-309">此架構的範例 Resoure Manager 範本可在 [GitHub 上取得][paas-basic-arm-template]。</span><span class="sxs-lookup"><span data-stu-id="67b62-309">An example Resoure Manager template for this architecture is [available on GitHub][paas-basic-arm-template].</span></span>

<span data-ttu-id="67b62-310">若要使用 PowerShell 部署範本，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="67b62-310">To deploy the template using PowerShell, run the following commands:</span></span>

```
New-AzureRmResourceGroup -Name <resource-group-name> -Location "West US"

$parameters = @{"appName"="<app-name>";"environment"="dev";"locationShort"="uw";"databaseName"="app-db";"administratorLogin"="<admin>";"administratorLoginPassword"="<password>"}

New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile .\PaaS-Basic.json -TemplateParameterObject  $parameters
```

<span data-ttu-id="67b62-311">如需詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源][deploy-arm-template]。</span><span class="sxs-lookup"><span data-stu-id="67b62-311">For more information, see [Deploy resources with Azure Resource Manager templates][deploy-arm-template].</span></span>

<!-- links -->

[aad-auth]: /azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication
[app-insights]: /azure/application-insights/app-insights-overview
[app-insights-data-rate]: /azure/application-insights/app-insights-pricing
[app-service]: https://azure.microsoft.com/documentation/services/app-service/
[app-service-auth]: /azure/app-service-api/app-service-api-authentication
[app-service-plans]: /azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
[app-service-plans-tiers]: https://azure.microsoft.com/pricing/details/app-service/
[app-service-security]: /azure/app-service-web/web-sites-security
[app-settings]: /azure/app-service-web/web-sites-configure
[arm-template]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-dns]: /azure/dns/dns-overview
[custom-domain-name]: /azure/app-service-web/web-sites-custom-domain-name
[deploy]: /azure/app-service-web/web-sites-deploy
[deploy-arm-template]: /azure/resource-group-template-deploy
[deployment-slots]: /azure/app-service-web/web-sites-staged-publishing
[diagnostic-logs]: /azure/app-service-web/web-sites-enable-diagnostic-log
[kudu]: https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/
[monitoring-guidance]: ../../best-practices/monitoring.md
[new-relic]: http://newrelic.com/
[paas-basic-arm-template]: https://github.com/mspnp/reference-architectures/tree/master/managed-web-app/basic-web-app/Paas-Basic/Templates
[perf-analysis]: https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md
[rbac]: /azure/active-directory/role-based-access-control-what-is
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[sla]: https://azure.microsoft.com/support/legal/sla/
[sql-audit]: /azure/sql-database/sql-database-auditing-get-started
[sql-backup]: /azure/sql-database/sql-database-business-continuity
[sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[sql-db-overview]: /azure/sql-database/sql-database-technical-overview
[sql-db-scale]: /azure/sql-database/sql-database-service-tiers#scaling-up-or-scaling-down-a-single-database
[sql-db-service-tiers]: /azure/sql-database/sql-database-service-tiers
[sql-db-v12]: /azure/sql-database/sql-database-features
[sql-dtu]: /azure/sql-database/sql-database-service-tiers
[sql-human-error]: /azure/sql-database/sql-database-business-continuity#recover-a-database-after-a-user-or-application-error
[sql-outage-recovery]: /azure/sql-database/sql-database-business-continuity#recover-a-database-to-another-region-from-an-azure-regional-data-center-outage
[ssl-redirect]: /azure/app-service-web/web-sites-configure-ssl-certificate#bkmk_enforce
[sql-resource-limits]: /azure/sql-database/sql-database-resource-limits
[ssl-cert]: /azure/app-service-web/web-sites-purchase-ssl-web-site
[troubleshoot-blade]: https://azure.microsoft.com/updates/self-service-troubleshooting-for-app-service-web-apps-customers/
[troubleshoot-web-app]: /azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio
[visio-download]: https://archcenter.azureedge.net/cdn/app-service-reference-architectures.vsdx
[vsts]: https://www.visualstudio.com/features/vso-cloud-load-testing-vs.aspx
[web-app-autoscale]: /azure/app-service-web/web-sites-scale
[web-app-backup]: /azure/app-service-web/web-sites-backup
[web-app-log-stream]: /azure/app-service-web/web-sites-enable-diagnostic-log#streamlogs
[0]: ./images/basic-web-app.png "基本 Azure Web 應用程式的架構"
[1]: ./images/paas-basic-web-app-staging-slots.png "交換生產和預備環境部署的位置"