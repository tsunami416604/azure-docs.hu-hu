<properties 
    pageTitle="DocumentDB Node.js SDK | Microsoft Azure" 
    description="了解所有 Node.js SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Node.js SDK 每個版本之間的變更。" 
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Node.js SDK

<table>
<tr><td>**下載**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**參與**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**文件**</td><td>[Node.js SDK Reference Documentation](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**範例**</td><td>[Node.js Code Samples](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**開始使用**</td><td>[Get started with the Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**目前支援的平台**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

## 版本資訊

### <a name="1.4.0"/>1.4.0</a>

- 實作 Upsert。 documentClient 上新的 upsertXXX 方法。 

### <a name="1.3.0"/>1.3.0</a>

- 已略過以配合其他 SDK 的版本號碼

### <a name="1.2.2"/>1.2.2</a>

- 將 Q Pomise 包裝函式分割至新的儲存機制
- 更新至 npm 登錄的封裝檔案

### <a name="1.2.1"/>1.2.1</a>

- 實作以識別碼為基礎的路由
- 修正問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -目前的屬性與方法 formatnumber （） 發生衝突

### <a name="1.2.0"/>1.2.0</a>

- 新增對地理空間索引的支援。
- 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、&#47;&#47; 等字元，或在結尾處使用空格。 
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>1.1.0</a>

- 實作 V2 索引原則

### <a name="1.0.3"/>1.0.3</a>

- 核心和承諾 SDK 中的問題 [#40] (https://github.com/Azure/azure-documentdb-node/issues/40)-實作 eslint 和 grunt 組態

### <a name="1.0.2"/>1.0.2</a>

- 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -承諾包裝函式不包含標頭發生錯誤。

### <a name="1.0.1"/>1.0.1</a>

- 已實作透過新增 readConflicts、readConflictAsync 及 queryConflicts 來查詢衝突的功能
- 已更新 API 文件
- 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync 錯誤  

### <a name="1.0.0"/>1.0.0</a>

- GA SDK

## 發行和停用日期
Microsoft 提供通知的最少 **12 個月** 之前淘汰 SDK 從容地轉換到較新/支援的版本。

新功能和功能與最佳化只能加入至目前的 SDK，因此它會建議您一律升級為最新 SDK 版本早越好。 

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
之前的版本中的所有版本的 Azure DocumentDB SDK for Node.js **1.0.0** 將淘汰上 **2016 年 2 月 29 日**。 

<br/>

| 版本 | 發行日期 | 停用日期 
| ---     | ---          | ---
| [1.4.0](#1.4.0) | 2015 年 10 月 6日日 |---
| [1.3.0](#1.3.0) | 2015 年 10 月 6日日 |---
| [1.2.2](#1.2.2) | 2015 年 9 月 10日日 |---
| [1.2.1](#1.2.1) | 2015 年 8 月 15日日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 5日日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 |---
| [1.0.3](#1.0.3) | 2015 年 6 月 4日日 |---
| [1.0.2](#1.0.2) | 2015 5 月 23日日 |---
| [1.0.1](#1.0.1) | 2015 5 月 15日日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 08 日 |---
| 0.9.4-prelease | 2015 年 4 月 6日日 | 2016 年 2 月 29日日
| 0.9.3-prelease | 2015 年 1 月 14日日 | 2016 年 2 月 29日日
| 0.9.2-prelease | 2014 年 12 月 18日日 | 2016 年 2 月 29日日
| 0.9.1-prelease | 2014 年 8 月 22日日 | 2016 年 2 月 29日日
| 0.9.0-prelease | 2014 年 8 月 21 日 | 2016 年 2 月 29日日


## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。 


