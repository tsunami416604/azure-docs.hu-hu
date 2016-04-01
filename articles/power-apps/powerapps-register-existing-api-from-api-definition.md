<properties
    pageTitle="從 PowerApps Enterprise 中的 API 建立 Swagger 2.0 API 定義 | Microsoft Azure"
    description="了解如何透過使用現有 API 建立的 Swagger 2.0 API 定義註冊 API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="12/09/2015"
   ms.author="guayan"/>

# 從 Swagger 2.0 API 定義註冊 API  
許多組織已經有一些現有的 API，使用者可以在其應用程式內使用和取用這些 API。 若要在您的應用程式中使用這些 API，您必須在 Azure 入口網站中「註冊」API。 可以使用下列選項： 

- 註冊預先建置 [Microsoft managed API 或 IT 管理 API](powerapps-register-from-available-apis.md)。
- 註冊 web 應用程式、 API 的應用程式和行動裝置應用程式裝載於 [App Service 環境](powerapps-register-api-hosted-in-app-service.md)。
- 使用 Swagger 2.0 API 定義 (在本主題中) 註冊其中一個您自己的 Swagger API。

本文將說明如何以 **註冊一個使用 Swagger 2.0 API 定義您的 Api** 您建立從現有的 API。 

#### 開始使用的必要條件

- 申請 [PowerApps 企業](powerapps-get-started-azure-portal.md)
- 建立 [app service 環境](powerapps-get-started-azure-portal.md)

## 使用 Swagger 2.0 API 定義註冊現有的 API

註冊這些現有的 API 非常簡單。 步驟包括：

1. 建立 [Swagger 2.0](http://swagger.io) API 定義，您現有的 api。 PowerApps 使用 Swagger 2.0 作為 API 定義格式。 您可以使用在所參考的工具 [Swagger 2.0 網站](http://swagger.io) 可協助您輕鬆撰寫 Swagger 2.0 API 定義。 注意事項：  

    - ``host`` 屬性應指向您現有 API 的實際端點。 請勿使用配置或任何子路徑。 例如，輸入 ``api.contoso.com``。  <br/><br/>
    - ``basePath`` 屬性應列出您現有 API 端點 (若有的話) 的子路徑。 以正斜線開頭 ``/``。 例如，輸入 ``/purchaseorderapi``。

2. 確定您的 App Service 環境能安全地存取現有的 API：  <br/><br/>
    a） 您是否想要讓使用網際網路來存取您的 API，您可以設定 HTTP 基本存取驗證 app service 環境之間現有的 API。 Go [這裡](powerapps-configure-apis.md) 若要查看如何。  <br/><br/>
    b） 如果您想要保留您的 API，您的組織網路內，您可以安全地存取您組織的網路設定虛擬網路的 app service 環境。 Go [這裡](../app-service-app-service-environment-intro.md) 若要查看如何。

3. 在 [Azure 入口網站](https://portal.azure.com/), ，請選取 **PowerApps**, ，然後選取 **管理 Api**:  
![][11]
4. 在 Api 管理中，選取 **新增**:  
![][12]
5. 在 **加入 API**, ，輸入 API 的內容 ︰  

    - 在 **名稱**, ，輸入您的 API 的名稱。 請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。 設定有意義且在組織中獨一無二的名稱。
    - 在 **來源**, ，請選取 **Swagger 2.0 從匯入**。

6. 在 **API 定義 (Swagger 2.0)**, 上, 傳 Swagger 2.0 API 定義檔案 ︰  
 ![][13]
7. 選取 **新增** 來完成這些步驟。

> [AZURE.TIP] 當您註冊 API 時，您要註冊 API app service 環境。 一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。

## 摘要和後續步驟

在本主題中，您已經了解如何從 Swagger 2.0 API 定義註冊 API。 以下是一些讓您深入了解 PowerApps 的相關主題與資源：  

- [設定 API 原則](powerapps-configure-apis.md)
- [讓使用者存取 API](powerapps-manage-api-connection-user-access.md)
- [開始在 PowerApps 中建立應用程式](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png


