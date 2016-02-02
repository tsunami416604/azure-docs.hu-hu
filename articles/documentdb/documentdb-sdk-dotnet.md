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


## DocumentDB .NET SDK

<table>





</table></br>

## 版本資訊

### <a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)

  - 擴充的 LINQ 支援包括新的分頁、條件式運算式以及範圍比較的運算子。
    - Take 運算子可啟用 LINQ 中的 SELECT TOP 行為
    - 
    - 
  - [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)

 - [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)

 - 實作 Upsert、新增 UpsertXXXAsync 方法
 - 所有要求的效能改進
 - LINQ 提供者支援字串的條件式、聯合和 CompareTo 方法
 - **[已修正]** LINQ 提供者 --> 在 List 上實作 Contains 方法，以產生與 IEnumerable 和 Array 上相同的 SQL
 - **[已修正]** 重試時，BackoffRetryUtility 會再次使用相同的 HttpRequestMessage，而非建立新的
 - **[已過時]** UriFactory.CreateCollection --> 現應使用 UriFactory.CreateDocumentCollection

### <a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)

 - **[已修正]** 使用非 en 文化特性資訊時 (例如 nl-NL 等) 的當地語系化問題。

### <a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)

  - 以識別碼為基礎的路由
    - 新的 UriFactory 協助程式，協助建構以識別碼為基礎的資源連結
    - DocumentClient 上新的多載可接受 URI
  - LINQ 中新增用於地理空間的 IsValid() 和 isvaliddetailed ()
  - 增強的 LINQ 提供者支援
    - **算術** - Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate
    - **字串** - Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper
    - **陣列** - Concat、Contains、Count
    - **IN** 運算子

### <a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)

  - 新增支援來修改索引編製原則
    - DocumentClient 中新的 ReplaceDocumentCollectionAsync 方法
    - 
    - DocumentCollection.IndexingPolicy 現在可變動
  - 新增空間索引編製和查詢的支援
    - 新的 Microsoft.Azure.Documents.Spatial 命名空間，可序列化/還原序列化空間類型，例如 Point 和 Polygon
    - 新的 SpatialIndex 類別，可對儲存在 DocumentDB 中的 GeoJSON 資料編製索引
  - 

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
NuGet 封裝名稱在預覽和 GA 之間已變更。
<br/>


### 

- 預覽 SDK [已過時]

## 發行和停用日期

Microsoft 至少會在停用 SDK 的 **12 個月**之前提供通知，以供順利轉換至較新/支援的版本。



使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。
> [AZURE.WARNING]
所有 **1.0.0** 之前的 Azure DocumentDB SDK for .NET 版本都將於 **2016 年 2 月29 日**時淘汰。 

<br/>

| 版本| 發行日期| 停用日期
| ---     | ---          | ---
| [1.5.2](#1.5.2)| | ---
| [1.5.1](#1.5.1)| | ---
| [1.5.0](#1.5.0)| | ---
| [1.4.1](#1.4.1)| | ---
| [1.4.0](#1.4.0)| | ---
| [1.3.0](#1.3.0)| | ---
| [1.2.0](#1.2.0)| | ---
| [1.1.0](#1.1.0)| | ---
| [1.0.0](#1.0.0)| | ---
| | | 
| | | 
| | | 
| | 2014 年 8 月 21 日| 

## 常見問題集

[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱







