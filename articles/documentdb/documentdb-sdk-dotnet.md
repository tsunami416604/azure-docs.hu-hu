<properties 
    pageTitle="DocumentDB .NET SDK | Microsoft Azure" 
    description="全面了解 .NET SDK，包括發行日期、停用日期及 DocumentDB .NET SDK 每個版本之間的變更。" 
    services="documentdb" 
    documentationCenter=".net" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB .NET SDK

<table>
<tr><td>**下載**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**文件**</td><td>[.NET SDK Reference Documentation](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**範例**</td><td>[.NET Code Samples](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr>
<tr><td>**開始**</td><td>[Get started with the DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**目前支援的架構**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## 版本資訊

### <a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - 擴充的 LINQ 支援包括新的分頁、條件式運算式以及範圍比較的運算子。
    - Take 運算子：可啟用 LINQ 中的 SELECT TOP 行為
    - CompareTo 運算子：可啟用字串範圍比較
    - 條件式 (?) 與聯合運算子 (??)
  - **[固定]** ArgumentOutOfRangeException 結合具有模型投影時 Where 單元 linq 查詢中。  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[固定]** 如果選取不是最後一個運算式 LINQ 提供者會假設沒有投影，並產生選取 * 不正確。  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - 實作 Upsert、新增 UpsertXXXAsync 方法
 - 所有要求的效能改進
 - LINQ 提供者支援字串的條件式、聯合和 CompareTo 方法
 - **[固定]** LINQ 提供者--> 包含實作方法來產生為 IEnumerable 與陣列相同的 SQL 清單
 - **[固定]** BackoffRetryUtility 相同 HttpRequestMessage 再次使用而非建立新的重試
 - **[已過時]** --> UriFactory.CreateCollection 現在應使用 UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[固定]** 當使用非 en-us 文化特性資訊，例如 NL-NL 等的當地語系化問題。 
 
### <a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - 以識別碼為基礎的路由
    - 新的 UriFactory 協助程式，協助建構以識別碼為基礎的資源連結
    - DocumentClient 上新的多載可接受 URI
  - LINQ 中新增用於地理空間的 IsValid() 和 isvaliddetailed ()
  - 增強的 LINQ 提供者支援
    - **數學** -Abs、 Acos、 Asin、 Atan、 Ceiling、 Exp、 Floor、 記錄、 Log10、 Pow、 循、 符號、 Sin、 Sqrt、 Tan、 Cos 截斷
    - **字串** -Concat，包含 EndsWith、 IndexOf、 計數、 ToLower、 TrimStart、 取代、 TrimEnd、 StartsWith、 SubString、 ToUpper 反轉
    - **陣列** -Concat，包含，計數
    - **IN** 運算子

### <a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - 新增支援來修改索引編製原則
    - DocumentClient 中新的 ReplaceDocumentCollectionAsync 方法
    - Sdk 之 ResourceResponse 中的新 Readdocumentcollectionasync 屬性<T> 用於追蹤的索引原則變更的百分比進度
    - DocumentCollection.IndexingPolicy 現在可變動
  - 新增空間索引編製和查詢的支援
    - 新的 Microsoft.Azure.Documents.Spatial 命名空間，可序列化/還原序列化空間類型，例如 Point 和 Polygon
    - 新的 SpatialIndex 類別，可對儲存在 DocumentDB 中的 GeoJSON 資料編製索引
  - **[固定]** : 從 linq 運算式所產生的 SQL 查詢不正確 [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- 對 Newtonsoft.Json v5.0.7 的相依性 
- 變更為支援 Order By
  - LINQ 提供者支援 OrderBy() 或 OrderByDescending()
  - IndexingPolicy 支援 Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- 支援使用新的 HashPartitionResolver 和 RangePartitionResolver 類別及 IPartitionResolver 來分割資料
- DataContract 序列化
- LINQ 提供者中的 Guid 支援
- LINQ 中的 UDF 支援 

### <a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
NuGet 封裝名稱在預覽和 GA 之間已變更。 我們將 **Microsoft.Azure.Documents.Client** 至 **Microsoft.Azure.DocumentDB**
<br/>


### <a name="0.9.x-preview"/>[0.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- 預覽 SDK [已過時]

## 發行和停用日期
Microsoft 提供通知的最少 **12 個月** 之前淘汰 SDK 從容地轉換到較新/支援的版本。

新功能和功能與最佳化只能加入至目前的 SDK，因此它會建議您一律升級為最新 SDK 版本早越好。 

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
所有之前的版本中，Azure DocumentDB SDK for.NET 版本 **1.0.0** 將淘汰上 **2016 年 2 月 29 日**。 
 
<br/>
 
| 版本 | 發行日期 | 停用日期 
| ---     | ---          | ---
| [1.5.2](#1.5.2) | 2015 年 12 月 14日日 |---
| [1.5.1](#1.5.1) | 2015 年 11 月 23日日 |---
| [1.5.0](#1.5.0) | 2015 年 10 月 5日日 |---
| [1.4.1](#1.4.1) | 2015 年 8 月 25日日 |---
| [1.4.0](#1.4.0) | 2015 年 8 月 13日日 |---
| [1.3.0](#1.3.0) | 2015 年 8 月 5日日 |---
| [1.2.0](#1.2.0) | 2015 年 7 月 6日日 |---
| [1.1.0](#1.1.0) | 2015 年 4 月 30日日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 08 日 |---
| [0.9.3-prelease](#0.9.x-preview) | 2015 年 3 月 12日日 | 2016 年 2 月 29日日
| [0.9.2-prelease](#0.9.x-preview) | 2015 年 1 月， | 2016 年 2 月 29日日
| [.9.1-prelease](#0.9.x-preview) | 2014 年 10 月 13日日 | 2016 年 2 月 29日日
| [0.9.0-prelease](#0.9.x-preview) | 2014 年 8 月 21 日 | 2016 年 2 月 29日日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。 

