---
title: "效能和延展性模式"
description: "效能是指系統在指定時間間隔內執行任何動作的回應能力，而延展性則是系統在不影響效能情況下處理負載增量的能力，或是系統處理可用資源快速增加的能力。 雲端應用程式通常會遇到變動的工作負載和活動尖峰。 要預測這些問題 (尤其是在多租用戶案例中) 幾乎不可能。 相反地，應用程式應該能夠在限制範圍內相應放大以符合尖峰需求，並在需求降低時相應縮小。 延展性不只要考量運算執行個體，還要考量其他元素，例如資料儲存體和傳訊基礎結構等。"
keywords: "設計模式"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 26e5fe0bc05bff7b9fb684795a2de3b57d945ae0
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="performance-and-scalability-patterns"></a><span data-ttu-id="03660-108">效能和延展性模式</span><span class="sxs-lookup"><span data-stu-id="03660-108">Performance and Scalability patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="03660-109">效能是指系統在指定時間間隔內執行任何動作的回應能力，而延展性則是系統在不影響效能情況下處理負載增量的能力，或是系統處理可用資源快速增加的能力。</span><span class="sxs-lookup"><span data-stu-id="03660-109">Performance is an indication of the responsiveness of a system to execute any action within a given time interval, while scalability is ability of a system either to handle increases in load without impact on performance or for the available resources to be readily increased.</span></span> <span data-ttu-id="03660-110">雲端應用程式通常會遇到變動的工作負載和活動尖峰。</span><span class="sxs-lookup"><span data-stu-id="03660-110">Cloud applications typically encounter variable workloads and peaks in activity.</span></span> <span data-ttu-id="03660-111">要預測這些問題 (尤其是在多租用戶案例中) 幾乎不可能。</span><span class="sxs-lookup"><span data-stu-id="03660-111">Predicting these, especially in a multi-tenant scenario, is almost impossible.</span></span> <span data-ttu-id="03660-112">相反地，應用程式應該能夠在限制範圍內相應放大以符合尖峰需求，並在需求降低時相應縮小。</span><span class="sxs-lookup"><span data-stu-id="03660-112">Instead, applications should be able to scale out within limits to meet peaks in demand, and scale in when demand decreases.</span></span> <span data-ttu-id="03660-113">延展性不只要考量運算執行個體，還要考量其他元素，例如資料儲存體和傳訊基礎結構等。</span><span class="sxs-lookup"><span data-stu-id="03660-113">Scalability concerns not just compute instances, but other elements such as data storage, messaging infrastructure, and more.</span></span>

| <span data-ttu-id="03660-114">模式</span><span class="sxs-lookup"><span data-stu-id="03660-114">Pattern</span></span> | <span data-ttu-id="03660-115">摘要</span><span class="sxs-lookup"><span data-stu-id="03660-115">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="03660-116">另行快取</span><span class="sxs-lookup"><span data-stu-id="03660-116">Cache-Aside</span></span>](../cache-aside.md) | <span data-ttu-id="03660-117">依需要從資料存放區將資料載入快取中</span><span class="sxs-lookup"><span data-stu-id="03660-117">Load data on demand into a cache from a data store</span></span> |
| [<span data-ttu-id="03660-118">CQRS</span><span class="sxs-lookup"><span data-stu-id="03660-118">CQRS</span></span>](../cqrs.md) | <span data-ttu-id="03660-119">隔離自使用個別介面來更新資料的作業中讀取資料的作業。</span><span class="sxs-lookup"><span data-stu-id="03660-119">Segregate operations that read data from operations that update data by using separate interfaces.</span></span> |
| [<span data-ttu-id="03660-120">事件來源</span><span class="sxs-lookup"><span data-stu-id="03660-120">Event Sourcing</span></span>](../event-sourcing.md) | <span data-ttu-id="03660-121">使用僅附加的存放區記錄完整系列的事件，其描述對網域中的資料採取的動作。</span><span class="sxs-lookup"><span data-stu-id="03660-121">Use an append-only store to record the full series of events that describe actions taken on data in a domain.</span></span> |
| [<span data-ttu-id="03660-122">索引資料表</span><span class="sxs-lookup"><span data-stu-id="03660-122">Index Table</span></span>](../index-table.md) | <span data-ttu-id="03660-123">針對資料存放區中查詢經常參考的欄位建立索引。</span><span class="sxs-lookup"><span data-stu-id="03660-123">Create indexes over the fields in data stores that are frequently referenced by queries.</span></span> |
| [<span data-ttu-id="03660-124">具體化檢視</span><span class="sxs-lookup"><span data-stu-id="03660-124">Materialized View</span></span>](../materialized-view.md) | <span data-ttu-id="03660-125">當資料格式對必要的查詢作業而言不理想時，對一或多個資料存放區中的資料產生預先填入的檢視。</span><span class="sxs-lookup"><span data-stu-id="03660-125">Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.</span></span> |
| [<span data-ttu-id="03660-126">優先順序佇列</span><span class="sxs-lookup"><span data-stu-id="03660-126">Priority Queue</span></span>](../priority-queue.md) | <span data-ttu-id="03660-127">針對傳送給服務的要求排列優先順序，讓高優先順序要求的接收和處理順序在低優先順序要求之前。</span><span class="sxs-lookup"><span data-stu-id="03660-127">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
| [<span data-ttu-id="03660-128">佇列型負載調節</span><span class="sxs-lookup"><span data-stu-id="03660-128">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="03660-129">使用佇列來作為工作與其所叫用服務之間的緩衝區，以使間歇性的繁重負載順暢。</span><span class="sxs-lookup"><span data-stu-id="03660-129">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="03660-130">分區化</span><span class="sxs-lookup"><span data-stu-id="03660-130">Sharding</span></span>](../sharding.md) | <span data-ttu-id="03660-131">將資料存放區分割為一組水平分割或分區。</span><span class="sxs-lookup"><span data-stu-id="03660-131">Divide a data store into a set of horizontal partitions or shards.</span></span> |
| [<span data-ttu-id="03660-132">靜態內容裝載</span><span class="sxs-lookup"><span data-stu-id="03660-132">Static Content Hosting</span></span>](../static-content-hosting.md) | <span data-ttu-id="03660-133">將靜態內容部署到可以直接將其交付給用戶端的雲端儲存體服務。</span><span class="sxs-lookup"><span data-stu-id="03660-133">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span> |
| [<span data-ttu-id="03660-134">節流</span><span class="sxs-lookup"><span data-stu-id="03660-134">Throttling</span></span>](../throttling.md) | <span data-ttu-id="03660-135">控制應用程式、個別租用戶或整個服務的執行個體所使用的資源耗用量。</span><span class="sxs-lookup"><span data-stu-id="03660-135">Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.</span></span> |