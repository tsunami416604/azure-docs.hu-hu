<properties 
    pageTitle="在串流分析中使用參考資料和查閱資料表 | Microsoft Azure" 
    description="在串流分析查詢中使用參考資料" 
    keywords="lookup table, reference data"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="paulettm"
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="12/04/2015" 
    ms.author="jeffstok"/>

# 在串流分析的輸入串流中使用參考資料或查詢資料表

參考資料 (也稱為查詢資料表) 基本上是靜態或不常變更的有限資料集，可用來執行查閱或與資料流相互關聯。 通常會使用 Azure 串流分析工作中參考資料使用 [參考資料 Join](https://msdn.microsoft.com/library/azure/dn949258.aspx) 在查詢中。 串流分析會為參考資料的儲存層的 Azure Blob 儲存體和 Azure Data Factory 參考資料可以進行轉換及/或複製到 Azure Blob 儲存體，作為參考資料，從 [任意數目的雲端型和內部資料存放區](./articles/data-factory-data-movement-activities.md)。 參考資料會依 Blob 名稱中指定之日期/時間的遞增順序，以 Blob 序列的形式建立模型 (在輸入組態中定義)。 它 **只** 支援使用日期/時間順序的結尾加入 **大於** 比指定的序列中的最後一個 blob。

## 設定參考資料

若要設定參考資料，您首先必須建立的型別輸入 **參考資料**。 下表說明您在建立參考資料輸入及其描述時必須提供的每個屬性：

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>說明</td>
</tr>
<tr>
<td>輸入別名</td>
<td>在工作查詢中將用來參考這個輸入的易記名稱。</td>
</tr>
<tr>
<td>儲存體帳戶</td>
<td>您的 blob 檔案所在的儲存體帳戶名稱。 如果它和您的串流分析工作位於相同的訂用帳戶中，您可以從下拉式清單加以選取。</td>
</tr>
<tr>
<td>儲存體帳戶金鑰</td>
<td>與儲存體帳戶相關聯的密碼金鑰。 如果儲存體帳戶和您的串流分析工作位於相同的訂用帳戶中，則會自動填入。</td>
</tr>
<tr>
<td>儲存體容器</td>
<td>容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。</td>
</tr>
<tr>
<td>路徑格式</td>
<td>用來在指定容器中找出 Blob 的檔案路徑。 在該路徑內，您也可以指定下列 2 個變數的一個或多個執行個體：<BR>{date}、{time}<BR>範例 1：products/{date}/{time}/product-list.csv<BR>範例 2：products/{date}/product-list.csv
</tr>
<tr>
<td>日期格式 [選用]</td>
<td>如果您已經使用您指定之路徑模式內的 {date}，您可以從支援格式的下拉式清單選取組織檔案所使用的日期格式。 範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [選用]</td>
<td>如果您已經使用您指定之路徑模式內的 {time}，您可以從支援格式的下拉式清單選取組織檔案所使用的時間格式。 範例：HH</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式。 參考資料的支援格式為 CSV 和 JSON。</td>
</tr>
<tr>
<td>編碼</td>
<td>UTF-8 是目前唯一支援的編碼格式</td>
</tr>
</tbody>
</table>

## 產生排程上的參考資料

如果您的參考資料是不常變更的資料集，可以啟用重新整理參考資料的支援，方法是使用 {date} 與 {time} 權杖指定輸入設定內的路徑模式。 串流分析會根據此路徑模式採用更新的參考資料定義。 例如，日期格式為 “YYYY-MM-DD” 且時間格式為 “HH:mm” 的模式 ````"/sample/{date}/{time}/products.csv"````，會告知「串流分析」在 UTC 時區 2015 年 4 月 16 日的下午 5:30 擷取更新的 Blob ````"/sample/2015-04-16/17:30/products.csv"````。

> [AZURE.NOTE] 目前資料流分析工作時請尋找 blob 重新整理只機器時間與 blob 名稱中編碼的時間。 例如，工作會在 UTC 時區 2015 年 4 月 16 日的下午 5:30 到下午5:30:59.9 之間尋找 /sample/2015-04-16/17:30/products.csv。 當機器時間為下午 5:31 時，就會停止尋找 /sample/2015-04-16/17:30/products.csv，並開始尋找 /sample/2015-04-16/17:31/products.csv。 此情況有例外，就是當工作需要回到之前來重新處理資料，或當工作第一次啟動的時候。 啟動時，工作會尋找在指定工作啟動時間之前產生的最新 Blob。 這是為了確保在工作啟動時，會有非空白的參考資料集。 如果無法找到，工作就會失敗，並向使用者顯示診斷注意事項。

[Azure Data Factory](http://azure.microsoft.com/documentation/services/data-factory/) 可用來協調建立更新的 blob 資料流分析更新參考資料定義所需的工作。 Data Factory 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。 資料處理站支援 [連接到雲端的大量基礎和內部資料存放區](./articles/data-factory-data-movement-activities.md) 和您指定的定期輕鬆地移動資料。 如需詳細資訊和如何設定產生資料流分析的預先定義的排程重新整理參考資料的 Data Factory 管線的逐步指引，請參閱 [GitHub 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)。

## 重新整理參考資料的秘訣 ##

1. 覆寫參考資料 Blob 並不會造成「串流分析」重新載入 Blob，並且在某些情況下，它可能會造成工作失敗。 若要變更參考資料的建議的方式是加入新的 blob，使用中工作的輸入定義的相同容器和路徑模式並使用日期/時間 **大於** 比指定的序列中的最後一個 blob。
2.  參考資料 Blobs 不是依 Blob 的「上次修改日期」時間排序，而是只依 Blob 名稱中使用 {date} 和 {time} 替代來指定的時間和日期排序。
3.  在一些情況下，工作必須回到之前，因此參照資料必須不被更改或刪除。

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟
以上就是串流分析 (物聯網資料串流分析專用的受管理服務) 的簡介。 若要深入了解此服務，請參閱：

- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

