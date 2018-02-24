---
title: "時間序列資料"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: ceb8f34d4fd950e5270edfea05945a824c4492f0
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="time-series-solutions"></a>時間序列解決方案

時間序列是一組依時間編排的值。 時間序列資料的範例，包括感應器資料、股票價格、點選流資料和應用程式遙測等。 時間序列資料的分析可用來建立歷史趨勢、即時警示或預測模型。

![Time Series Insights](./images/time-series-insights.png) 

時間序列資料代表資產或處理程序如何隨著時間改變。 資料具有時間戳記，但更重要的是，時間是檢視或分析資料時最具意義的軸。 時間序列資料通常會依照時間順序送達，而且通常會被視為對資料庫的插入，而不是更新資料庫。 因此，系統會隨著時間測量變更，讓您回顧和預測未來的變更。 由此觀之，時間序列資料最適合以散佈圖或折線圖呈現。

![以折線圖呈現的時間序列資料](./images/time-series-chart.png)

以下是時間序列資料的一些範例：

- 擷取一段時間的股票價格以偵測趨勢。
- 伺服器效能，例如 CPU 使用量、I/O 負載、記憶體使用量，以及網路頻寬耗用量。
- 從感應器對工業設備進行的遙測，可用來偵測設備暫停故障並觸發警示通知。
- 一段時間範圍內的即時汽車遙測資料，包括速度、煞車和加速，用以產生駕駛者的彙總風險分數。

藉由上述各種案例，您不難發現何以時間是最具意義的軸。 依事件的送達順序加以顯示，是時間序列資料的重要特性，因為原本就有時間順序存在。 這不同於為標準 OLTP 資料管線擷取的資料；在此類管線中，資料可依任何順序輸入、並且可隨時更新。

## <a name="when-to-use-this-solution"></a>使用此解決方案的時機

當您需要擷取資料，且其策略性價值環繞在一段時間的變化，而您主要只會插入新資料，鮮少進行更新時，請選擇時間序列解決方案。 您可以使用這項資訊來偵測異常狀況、呈現趨勢，以及比較目前的資料與歷史資料，和執行其他作業。 這種類型的架構也非常適合用來建立預測模型及預測結果，因為您具有特定期間變化的歷史記錄，可套用至任何數量的預測模型。 

使用時間序列具有下列優點︰

* 清楚呈現資產或程序如何隨著時間而改變。
* 可協助您快速偵測出多項相關來源的變更，讓異常狀況和浮現的趨勢能夠清楚顯現。
* 最適合用來建立預測模型和預測。

### <a name="internet-of-things-iot"></a>物聯網 (IoT)

IoT 裝置所收集的資料在本質上即適用於時間序列儲存和分析。 內送資料絕大多數都是進行插入，而鮮少更新。 資料會加上時間戳記，並依據收到的順序插入，且這項資料通常會依時間先後順序顯示，讓使用者能探索趨勢、找出異常狀況，並將該資訊用於預測分析。

如需詳細資訊，請參閱[物聯網](../concepts/big-data.md#internet-of-things-iot)。

### <a name="real-time-analytics"></a>即時分析

時間序列資料通常具有時效性 &mdash; 也就是說，必須迅速因應，以即時找出趨勢或產生警示。 在這些情況下，任何資料判讀上的延遲都可能導致運作中止和業務衝擊。 此外，來自各種不同來源 (例如感應器) 的資料也常需要產生關聯。

在理想情況下，您必須具有串流處理層可即時處理內送資料，且全數以高精確度和細微性進行處理。 但並非每次都能達到此理想情況，這取決於您的串流處理架構以及串流緩衝處理與串流處理層級的元件。 您可能需要減少時間序列資料，而稍微犧牲其精確度。 這可藉由滑動時間範圍來達成 (例如，幾秒鐘)，讓處理層能適時執行計算。 在顯示較長的時段時，您也可能需要縮小資料取樣及彙總資料，例如，適當縮放以顯示數個月內擷取的資料。

## <a name="challenges"></a>挑戰

* 時間序列資料通常數量龐大，尤其是 IoT 的資料。 儲存、編製索引、查詢、分析並以視覺化方式呈現時間序列資料可能具有挑戰性。 
* 要找出高速儲存體和強大運算能力的適當組合以處理即時分析，同時盡可能縮短上市時程並降低整體成本投資，可能並不容易。

## <a name="architecture"></a>架構

在許多涉及時間序列資料的案例中 (例如 IoT)，資料都是以即時方式擷取的。 因此，[即時處理](./real-time-processing.md)是其適用的架構。 

來自一或多個資料來源的資料會由 [IoT 中樞](/azure/iot-hub/)、[事件中樞](/azure/event-hubs/)或 [HDInsight 上的 Kafka](/azure/hdinsight/kafka/apache-kafka-introduction) 擷取到串流緩衝處理層中。 接下來，資料會在串流處理層進行處理，然後選擇性地遞交到機器學習服務進行預測分析。 已處理的資料會儲存在分析資料存放區中，例如 [HBase](/azure/hdinsight/hbase/apache-hbase-overview)、[Azure Cosmos DB](/azure/cosmos-db/)、Azure Data Lake 或 Blob 儲存體。 您可以使用分析和報告應用程式或服務來顯示時間序列資料以供分析，例如 Power BI 或 OpenTSDB (如果儲存在 HBase 中)。

另一個選項是使用 [Azure 時間序列深入解析](/azure/time-series-insights/)。 「時間序列深入解析」是適用於時間序列資料的完整受控服務。 在此架構中，「時間序列深入解析」會執行串流處理、資料存放區，以及分析和報告的角色。 它會接受來自 IoT 中樞或事件中樞的串流資料，並以幾近即時的方式儲存、處理、分析及顯示資料。 它不會預先彙總資料，而是會儲存原始事件。

「時間序列深入解析」是具調適性的結構描述，這表示您無須進行任何資料準備，即可開始判讀資訊。 這可讓您順暢地探索、比較及相互關聯各種資料來源。 它也提供類似於 SQL 的篩選和彙總功能，能夠建構、呈現、比較和重疊不同的時間序列模式與熱度圖，並且能夠儲存及共用查詢。 

## <a name="technology-choices"></a>技術選擇

- [資料儲存體](../technology-choices/data-storage.md)
- [分析、視覺效果和報告](../technology-choices/analysis-visualizations-reporting.md)
- [分析資料存放區](../technology-choices/analytical-data-stores.md)
- [串流處理](../technology-choices/stream-processing.md)