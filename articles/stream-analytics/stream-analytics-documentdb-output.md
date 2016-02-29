<properties
    pageTitle="串流分析的 JSON 輸出 | Microsoft Azure"
    description="了解串流分析如何為適用於 JSON 輸出的 Azure DocumentDB 設定目標，來啟用非結構化 JSON 資料的資料封存及低延遲查詢功能。"
    keywords="JSON output"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="11/23/2015"
    ms.author="jeffstok"/>

# 從串流分析為適用於 JSON 輸出的 Azure DocumentDB 設定目標

可以將目標資料流分析 [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) JSON 輸出，讓資料封存和低延遲查詢非結構化 JSON 資料。 您將了解此整合作業的最佳實行方式。

對於不熟悉 DocumentDB 使用者，看看 [DocumentDB 的學習途徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/) 以便開始使用。

## 將 DocumentDB 做為輸出目標的基本概念
串流分析中的 Azure DocumentDB 輸出可將串流處理結果當做 JSON 輸出來寫入您的 DocumentDB 集合中。 串流分析不會在資料庫中建立集合，而是需要您預先建立集合。 這是，讓透明，DocumentDB 集合的計費成本，以便您調整效能、 一致性和直接使用集合的容量 [DocumentDB Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)。 建議每個串流工作使用一個 DocumentDB Database，以邏輯為串流工作分隔集合。

以下詳細說明 DocumentDB 集合的某些選項。

## 微調一致性、 可用性及延遲

為了符合應用程式需求，DocumentDB 允許您微調資料庫與集合，並在一致性、可用性與延遲之間取捨。 您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 此外，根據預設，DocumentDB 允許在每個 CRUD 作業時對您的集合進行索引同步化。 這也是另一個可在 DocumentDB 中控制寫入/讀取效能的實用選項。 如需本主題的詳細資訊，請檢閱 [變更資料庫和查詢的一致性層級](../articles/documentdb-consistency-levels.md) 文件。

## 選擇效能層級

DocumentDB 集合可在 3 個不同的效能層級 (S1、S2 或 S3) 建立，以決定 CRUD 可對該集合的輸送量。 此外，效能會受到您集合的索引/一致性層級影響。 請參閱 [這篇文章](../articles/documentdb-performance-levels.md) 對於了解這些詳細資料中的效能層級。

## 來自串流分析的 Upsert

與 DocumentDB 整合的串流分析可讓您根據指定的 Document ID 欄，將記錄插入 DocumentDB 集合或更新記錄。 這也稱為 *Upsert*。

串流分析可利用最佳化的更新插入方法，而此方法只會在因為 Document ID 發生衝突而插入失敗時，才進行更新。 串流分析會將更新作為修補程式執行，以部分更新文件，也就是以漸進方式新增新屬性或取代現有屬性。 請注意，變更 JSON 文件中陣列屬性的值，會造成整個陣列遭到覆寫，也就是不會合併陣列。

## DocumentDB 中的資料分割

DocumentDB 集合可讓您根據查詢模式和應用程式的效能需求來分割資料。 每個集合最多可包含 10GB 的資料 (上限)，且目前還無法相應增加 (或溢位) 集合。 如需相應放大，串流分析允許您使用指定的前置詞寫入多個集合 (請參閱下方的使用方式詳細資料)。 串流分析會一致 [雜湊分割解析程式](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 提供 PartitionKey 資料行來分割其輸出記錄使用者為基礎的策略。 系統會使用在串流工作開始時間具有所指定前置詞的集合數量，作為該工作會同時寫入的輸出分割計數 (DocumentDB 集合數 = 輸出分割數)。 對於延遲索引只進行插入的單一 S3 集合，預期會有約 0.4 MB/s 的寫入輸送量。 使用多個集合可允許您達成更高的輸送量及增加容量。

如果您計劃在未來增加分割計數，您可能需要停止工作、將現有集合中的資料重新分割至新的集合，然後重新啟動串流分析工作。 有關使用 PartitionResolver 與重新分割的詳細資料，以及範例程式碼，都會包含在後續的文章中。 發行項 [DocumentDB 中分割](../articles/documentdb-partition-data.md#developing-a-partitioned-application) 也提供此詳細資料。

## JSON 輸出的 DocumentDB 設定

若在串流分析中建立 DocumentDB 作為輸出，則會產生資訊的提示，如下所示。 本節說明屬性定義。

![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **輸出別名** – 的別名來參考此 ASA 查詢中的輸出  
-   **帳戶名稱** – 端點 URI 的 DocumentDB 帳戶的名稱。  
-   **帳戶金鑰** – DocumentDB 帳戶的共用的存取金鑰。  
-   **資料庫** – 的 DocumentDB 資料庫名稱。  
-   **集合名稱模式** – 用於集合的集合名稱模式。 您可以使用選用的 {partition} 語彙基元來建構集合名稱的格式，其中的資料分割會從 0 開始。 以下是有效的輸入範例:  
   \&Lt;1\}) MyCollection – 必須存在一個名為"MyCollection"的集合。  
   2) – 這類集合必須存在 – MyCollection {partition}"MyCollection0"，"MyCollection1 」、 「 MyCollection2 」 等等。  
-   **資料分割索引鍵** – 用來指定的索引鍵，跨集合分割輸出的輸出事件中的欄位名稱。 若為單一集合輸出，則可使用任何任意的輸出欄，例如 PartitionId。  
-   **文件識別碼** – 選擇性。 輸出事件中的欄位名稱會用來指定主索引鍵，此為插入或更新作業的依據。  

