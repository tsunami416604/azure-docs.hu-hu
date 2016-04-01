<properties
   pageTitle="在 Azure App Service 中使用邏輯應用程式內的 VETR 建立 EAI Logic Apps | Microsoft Azure"
   description="BizTalk XML 服務的驗證、編碼和轉換功能"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="rajram"/>


# 使用 VETR 建立 EAI 邏輯應用程式

大部分的企業應用程式整合 (EAI) 案例可在來源與目的地之間傳達資料。 此種案例通常會有一組常見的需求：

- 確定來自不同系統的資料格式正確。
- 對內送資料執行「查閱」動作以便制定決策。
- 將資料從一種格式轉換成另一種格式。 例如，將資料從 CRM 系統的資料格式轉換成 ERP 系統的資料格式。
- 將資料路由到所需的應用程式或系統。

本文將說明常見的整合模式：「單向訊息中繼」，即 VETR (驗證、擴充、轉換、路由)。 VETR 模式會在來源實體與目的地實體之間傳達資料。 通常來源和目的地為資料來源。

想像一個接受訂單的網站。 使用者會使用 HTTP 將訂單張貼至系統。 系統會在幕後驗證內送資料的正確性、將其正規化，並將它保存在服務匯流排佇列中以進行後續處理。 系統會將訂單從佇列中移除，預期它會採用特定的格式。 因此：端對端流程會是：

**HTTP** → **驗證** → **轉換** → **服務匯流排**

![基本 VETR 流程][1]

下列的 BizTalk API Apps 可協助您建立這種模式：

* **HTTP 觸發程序** -觸發訊息事件的來源
* **驗證** -驗證內送資料的正確性
* **轉換** -將資料從傳入格式來格式化下游系統所需的轉換
* **服務匯流排連接器** -將資料傳送的目的地實體


## 建構基本的 VETR 模式
### 基本概念

在 Azure 入口網站中，選取 **+ 新增**, ，請選取 **Web + 行動**, ，然後選取 **邏輯應用程式**。 選擇名稱、位置、訂閱、資源群組以及運作位置。 資源群組可做為應用程式的容器使用，且應用程式的所有資源都屬於相同的資源群組。

接下來，讓我們新增觸發程序和動作。


## 新增 HTTP 觸發程序
1. 在 **邏輯應用程式範本**, ，請選取 **從頭建立**。
1. 選取 **HTTP 接聽程式** 從組件庫建立新的接聽程式。 稱它為 **HTTP1**。
2. 設定 **自動傳送回應？** 將設定為 false。 設定觸發程序動作，藉由設定 _HTTP 方法_ 至 _POST_ 和設定 _相對 URL_ 至 _/OneWayPipeline_:  
    ![HTTP 觸發程序][2]
3. 選取綠色打勾記號來完成觸發程序。

## 加入驗證動作

現在，讓我們輸入每當引發觸發程序時 (亦即，每當 HTTP 端點上收到呼叫時) 所要執行的動作。

1. 新增 **BizTalk XML 驗證器** 從組件庫，並將它 _(Validate1)_ 建立執行個體。
2. 設定 XSD 結構描述來驗證內送的 XML 訊息。 選取 _驗證_ 動作，然後選取 _triggers('httplistener').outputs。內容_ 做為值 _inputXml_ 參數。

目前，驗證動作是 HTTP 接聽程式之後的第一個動作： 

![BizTalk XML 驗證器][3]

讓我們以類似的方式加入其餘動作。 

## 新增轉換動作
我們將設定可標準化內送資料的轉換。

1. 新增 **BizTalk 轉換服務** 從組件庫。
2. 若要設定轉換來轉換傳入的 XML 訊息，請選取 **轉換** 做的動作，會呼叫此 API 時執行的動作。 選取 ```triggers(‘httplistener’).outputs.Content``` 做為值 _inputXml_。 *對應* 是一個選擇性參數，因為內送資料符合所有已設定的轉換，而只是符合結構描述會套用。
3. 最後，只有當驗證成功時才會執行轉換。 若要設定這種情況，請選取右上方的齒輪圖示，然後選取 _新增條件，以符合_。 將條件設為 ```equals(actions('xmlvalidator').status,'Succeeded')```：  

![BizTalk 轉換][4]


## 新增服務匯流排連接器
接下來，我們將新增可寫入資料的目的地 (服務匯流排佇列)。

1. 新增 **服務匯流排連接器** 從組件庫。 設定 **名稱** 至 _Servicebus1_, ，請將 **連接字串** 您服務匯流排執行個體的連接字串，來設定 **實體名稱** 至 _佇列_, ，並略過 **訂用帳戶名稱**。
2. 選取 **傳送訊息** 動作，並設定 **內容** 動作欄位 _actions('transformservice').outputs。OutputXml_。
3. 設定 **內容類型** 欄位 *應用程式/xml*:  

![服務匯流排][5]


## 傳送 HTTP 回應
完成管線處理後，以下列步驟傳回成功和失敗的 HTTP 回應：

1. 新增 **HTTP 接聽程式** 從組件庫，然後選取 **傳送 HTTP 回應** 動作。
2. 設定 **回應識別碼** 傳送 *訊息*。
2. 設定 **回應內容** 至 *完成管線處理*。
3. **回應狀態碼** 至 *200* 表示 HTTP 200 OK。
4. 選取右上方的下拉式功能表，然後選取 **新增條件，以符合**。  將條件設為下列運算式 ︰  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. 您也可以重複上述步驟來傳送失敗的 HTTP 回應。 變更 **條件** 為下列運算式 ︰  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. 選取 **確定** 然後 **建立**。



## 完成
每次有人傳送訊息至 HTTP 端點時，它會觸發應用程式並執行您剛建立的動作。 若要管理您建立任何這類邏輯應用程式，請選取 **瀏覽** Azure 入口網站，然後選取 **邏輯應用程式**。 選取您的 App，以查看詳細資訊。

一些有用的主題：

[管理和監視 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)  <br/>
[監視邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG


