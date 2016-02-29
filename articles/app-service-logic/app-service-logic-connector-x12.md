<properties 
   pageTitle="在 Logic Apps 中使用 BizTalk X12 連接器 | Microsoft Azure App Service" 
   description="如何建立並設定 BizTalk X12 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它" 
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
   ms.date="11/30/2015"
   ms.author="rajram"/>

# 開始使用 BizTalk X12 API 應用程式並將它加入您的邏輯應用程式
使用 BizTalk X12 服務可以透過 X12 通訊協定，以企業對企業通訊接收和傳送訊息。 X12 通常也稱為 ASC X12 或 Accredited Standards Committee X12，而且廣泛用於各產業。

您可以將 BizTalk X12 API 應用程式加入您的商務工作流程，就能在邏輯應用程式的企業對企業工作流程中處理資料。 


## 先決條件
- TPM API 應用程式: 之前建立 x12 連接器，您必須先建立 [BizTalk 交易夥伴管理連接器] [1]。
- SQL Azure 資料庫：每個 B2B API 應用程式都需要自己的 Azure SQL Database。
- Azure 服務匯流排：這是選用的，且僅用於批次處理。

## 使用 BizTalk X12 連接器
若要使用 BizTalk X12 連接器，您必須先建立 BizTalk X12 連接器 API 應用程式的執行個體。 這可以在建立邏輯應用程式時同時建立，或從 Azure Marketplace 選取 BizTalk X12 連接器 API 應用程式來建立。

## 設定 BizTalk X12 連接器
交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。 當兩個夥伴建立關係時，這稱為協議。 定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。

若要建立交易夥伴協議的步驟會說明 [這裡] [2]。

## 在 Logic Apps 設計工具介面中使用 X12 連接器
X12 連接器可用做觸發程序或動作。

### 觸發程序
- 啟動 Azure Logic Apps 流程設計工具。
- 按一下 x12 連接器從右窗格中:  
![觸發程序設定][] 3
- 按一下->:  
![觸發程序選項][] 4
- BizTalk X12 連接器會公開單一觸發程序。 選取 *釋放批次*:  
![發行批次輸入][] 5
- 這個觸發程序沒有輸入。 按一下->:  
![設定發行批次][] 6
- 連接器會在輸出中傳回 X12 裝載、協議識別碼，以及訊息是否為批次的資訊。

### 動作
- 按一下 x12 連接器從右窗格中:  
![動作設定][] 7
- 按一下->:  
!動作清單[] 8
- X12 連接器支援許多動作。 選取 *編碼*:  
![編碼輸入][] 9
- 提供輸入動作，然後將它設定:  
![編碼設定][] 10

參數|類型|參數說明
---|---|---
內容|字串|XML 訊息
協議識別碼|字串|協議識別碼
是批次訊息|字串|是批次訊息

動作傳回物件，其中包含 X12 裝載。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。 請參閱 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。


<!--References -->
[1]: app-service-logic-connector-tpm.md 
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

