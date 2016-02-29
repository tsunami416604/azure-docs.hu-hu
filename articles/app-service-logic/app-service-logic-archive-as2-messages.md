<properties 
   pageTitle="封存 AS2 連接器訊息 | Microsoft Azure App Service" 
   description="如何在 Azure App Service 中封存或儲存 AS2 連接器訊息" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="11/30/2015"
   ms.author="rajram"/>


# AS2 連接器訊息的封存概觀

**AS2 連接器** 公開了封存訊息的功能。 將訊息封存儲存 **Azure Blob 容器** 也就是封裝設定的一部分。 

封存功能會同時於訊息和通知 (MDN) 的兩個點公開：

1. **接收/解碼觸發程序**: 因為 API 應用程式執行個體收到訊息封存 
2. **編碼/傳送動作**: 保存已編碼的訊息時，在所有處理完成，而後傳送給合作夥伴之前 

## 做法：擷取訊息的已封存 URL

瀏覽至 AS2 連接器 API 應用程式執行個體，然後按一下 [追蹤]。 使用篩選參數縮小追蹤資訊。 您的訊息一旦出現在檢視中，按一下即可查看其詳細檢視。 訊息的已封存的 URL 會顯示在此詳細檢視:  
![][1]  

## 做法：擷取已封存的訊息。

使用前面擷取的 URL 從 Azure Blob 儲存體擷取已封存的訊息。


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

