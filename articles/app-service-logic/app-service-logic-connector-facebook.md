<properties
   pageTitle="在 Logic Apps 中使用 Facebook 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Facebook 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>



# 開始使用 Facebook 連接器並將它加入您的邏輯應用程式

連線至 Facebook 帳戶以張貼訊息或發佈相片。 邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。

您可以使用 Facebook 連接器來：

- 使用觸發程序擷取「使用者時間軸上的新貼文」或「網頁上的新貼文」。 擷取到新推文時，它會觸發流程的新執行個體，並將要求中所收到的資料傳遞到處理的流程中。
- 使用讓您「發佈貼文」和「發佈相片 」等的動作。 這些動作會取得回應，並可供流程中的動作取用。

您可以將 Facebook 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 觸發程序和動作

 觸發程序| 動作
--- | ---
 <ul><li>使用者時間軸上的新文章</li><li>] 頁面上的新文章</li></ul>| <ul><li>發佈文章</li><li>發佈相片</li></ul>



## 建立邏輯應用程式的 Facebook 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Facebook 連接器」，將其選取，然後選取 [建立]****。
3. 輸入名稱、App Service 方案和其他屬性：  
    ![][1]
4.  選取 [**建立**]。


## 在邏輯應用程式中使用 Facebook 連接器

建立 API 應用程式之後，您現在可以使用 Facebook 連接器做為邏輯應用程式的觸發程序/動作 。 若要這樣做，您需要：

1.  邏輯應用程式中開啟 **觸發程序和動作** 以開啟邏輯應用程式設計工具並設定您的流程:  
    ![][3]
2.  Facebook 連接器會列在組件庫:  
    ![][4]
3. 選取 Facebook 連接器以自動加入至設計工具。 選取 **授權**, 、 輸入您的認證，然後選取 **允許**:  
    ![][5]
    ![][6]
    ![][7]
    ![][8]
4.  選取觸發程序:  
    ![][9]

您現在可以在其他動作中使用從 Facebook 觸發程序擷取的貼文。 在下列流程中，每當使用者的 Facebook 在時間表上，張貼新文章時相同的文章將會成為貼文使用者的 Twitter 時間軸中:  
    ![][10]

您可以類似的方式，使用 Facebook 連接器動作建立流程。 下列流程會擷取 Yammer 群組上張貼的新訊息並發佈使用者所管理的 Facebook 網頁上相同的文章:  
    ![][11]
> [AZURE.TIP] 若要取得 Facebook 頁面識別碼或 Yammer 群組識別碼，請在 URL 中尋找數值代碼。

## 進一步運用您的連接器

現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md).
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。



[1]: ./media/app-service-logic-connector-facebook/img1.png 
[2]: ./media/app-service-logic-connector-facebook/img2.png 
[3]: ./media/app-service-logic-connector-facebook/img3.png 
[4]: ./media/app-service-logic-connector-facebook/img4.png 
[5]: ./media/app-service-logic-connector-facebook/img5.png 
[6]: ./media/app-service-logic-connector-facebook/img6.png 
[7]: ./media/app-service-logic-connector-facebook/img7.png 
[8]: ./media/app-service-logic-connector-facebook/img8.png 
[9]: ./media/app-service-logic-connector-facebook/img9.png 
[10]: ./media/app-service-logic-connector-facebook/img10.png 
[11]: ./media/app-service-logic-connector-facebook/img11.png 

