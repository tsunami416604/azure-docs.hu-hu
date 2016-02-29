<properties 
   pageTitle="在 Logic Apps 中使用 AS2 連接器 | Microsoft Azure App Service" 
   description="如何建立並設定 AS2 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它" 
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

# 開始使用 AS2 連接器並將它加入您的邏輯應用程式
使用 AS2 連接器可在企業對企業通訊中，透過 AS2 (Applicability Statement 2) 傳輸通訊協定接收和傳送訊息。 資料是透過網際網路安全、可靠地傳輸。 並使用數位憑證和加密來達成安全性。

您可以將 AS2 連接器加入您的商務工作流程，就能在邏輯應用程式的企業對企業工作流程中處理資料。 

## 觸發程序和動作
觸發程序會根據特定的事件啟動新的執行個體，例如來自夥伴的 AS2 訊息送達。 動作即為結果，就像在接收 AS2 訊息之後，然後使用 AS2 傳送訊息。

AS2 連接器可以在邏輯應用程式中做為觸發程序或動作，且支援 JSON 和 XML 格式的資料。 AS2 連接器提供下列觸發程序和動作： 

觸發程序 | 動作
--- | ---
接收和解碼 | 編碼和傳送

## 開始使用的需求
您必須先建立下列項目，才能供 AS2 連接器使用：

需求 | 說明
--- | ---
TPM API 應用程式 | 在建立 AS2 連接器之前，您必須建立 [BizTalk 交易夥伴管理連接器] [1]。 <br/><br/>**請注意** 知道您 TPM API 應用程式的名稱。 
Azure SQL Database | 儲存 B2B 項目，包括夥伴、結構描述、憑證及協議。 每個 B2B API 應用程式都需要自己的 Azure SQL Database。 <br/><br/>**請注意** 複製到此資料庫的連接字串。<br/><br/>[建立 Azure SQL Database](../sql-database-get-started.md)
Azure Blob 儲存體容器 | 啟用 AS2 封存功能時，可儲存訊息屬性。 如果您不需要 AS2 訊息封存功能，就不需要儲存體容器。 <br/><br/>**請注意** 如果您要啟用封存，將連接字串複製到這個 Blob 儲存體。<br/><br/>[關於 Azure 儲存體帳戶](../storage-create-storage-account.md)。

## 建立 AS2 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：  

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 搜尋 「 AS2 連接器 」，選取它，並選取 **建立**。
3. 輸入名稱、App Service 方案和其他屬性。
4. 輸入下列封裝設定：

    屬性 |描述
--- | --- 
資料庫連接字串 |輸入您所建立的 Azure SQL Database 的 ADO.NET 連接字串。 當您複製連接字串時，密碼不會加入至連接字串。 請務必在貼上之前，在連接字串中輸入密碼。
啟用內送訊息的封存 |選擇性的。 啟用這個屬性以儲存從合作夥伴接收之內送 AS2 訊息的訊息屬性。 
Azure Blob 儲存體連接字串 |輸入您建立的 Azure Blob 儲存體容器的連接字串。 啟用封存後，已編碼和解碼的訊息會儲存在此儲存體容器中。
TPM 執行個體名稱 |輸入的名稱 **BizTalk 交易夥伴管理** 您先前建立的 API 應用程式。 當您建立 AS2 連接器時，這個連接器只會執行此特定 TPM 執行個體中的 AS2 協議。

5. 選取 **建立**。 

交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。 當兩個夥伴建立關係時，這稱為協議。 定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。

建立交易夥伴協議所涉及的步驟會說明 [這裡] [2]。

## 使用連接器做為觸發程序

1. 當建立或編輯邏輯應用程式，請選取您建立從右窗格中的 AS2 連接器:  
    ![觸發程序設定][] 3

2. 按一下向右箭號 →:  
    ![觸發程序選項][] 4

3. AS2 連接器會公開單一觸發程序。 選取 *接收和解碼*:  
    ![接收和解碼輸入][] 5

4. 這個觸發程序沒有輸入。 按一下向右箭號 →:  
    ![接收和解碼設定][] 6

連接器會傳回 AS2 承載以及 AS2 特定的中繼資料，做為輸出的一部分。

AS2 Payload 成為 post 張貼至 https://{Host URL 時，會引發觸發程序} / 解碼。  您可以在 API 應用程式設定中找到主機 URL。  您也可能需要在應用程式設定中變更 API 應用程式的 [存取層級] 為 [公用] (已驗證或匿名)。

## 使用連接器做為動作
1. 觸發程序之後 (或選擇 [手動執行此邏輯])，加入您建立從右窗格中的 AS2 連接器:  
    ![動作設定][] 7

2. 按一下向右箭號 →:  
    !動作清單[] 8

3. AS2 連接器只支援一個動作。 選取 *編碼和傳送*:  
    ![編碼和傳送輸入][] 9

4. 輸入動作的輸入，並設定它:  
    ![編碼和傳送設定][] 10

    參數包括： 

    參數 |型別 |描述
--- | --- | ---
裝載 |object|要編碼並張貼到設定的結束點裝載的內容。 必須以 JSON 物件提供承載。
AS2 從 |字串 |AS2 訊息的寄件者的 AS2 身分識別。 這個參數是用來查閱可傳送訊息的適當協議。
AS2 至 |字串 |AS2 訊息接收者的 AS2 身分識別。 這個參數是用來查閱可傳送訊息的適當協議。
夥伴 URL |字串 |夥伴需要傳送訊息的端點。
啟用封存 |布林值 |決定是否應該封存輸出訊息。

此動作會在成功完成時傳回 HTTP 200 回應碼。

## 進一步運用您的連接器
在邏輯應用程式有更多 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

