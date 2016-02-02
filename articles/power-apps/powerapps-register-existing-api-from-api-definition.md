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

本文將說明如何**使用 Swagger 2.0 API 定義註冊其中一個您自己的 API** (您從現有 API 建立的 Swagger 2.0 API 定義)。

#### 開始使用的必要條件

- 申請 [PowerApps 企業](powerapps-get-started-azure-portal.md)
- 建立 [app service 環境](powerapps-get-started-azure-portal.md)

## 使用 Swagger 2.0 API 定義註冊現有的 API

註冊這些現有的 API 非常簡單。 步驟包括：

1. 建立 [Swagger 2.0](http://swagger.io) API 定義，您現有的 api。 PowerApps 使用 Swagger 2.0 作為 API 定義格式。 您可以使用在所參考的工具 [Swagger 2.0 網站](http://swagger.io) 可協助您輕鬆撰寫 Swagger 2.0 API 定義。 注意事項：

    - `主機` 屬性必須指向實際的端點，您現有的 api。 請勿使用配置或任何子路徑。 例如，輸入 `api.contoso.com`。 <br/><br/>
    - `的基本路徑` 屬性應列出現有的 API 端點的子路徑，如果有的話。 以正斜線開頭 `/`。 例如，輸入 `/purchaseorderapi`。

2. 請確定您現有的 API 是安全地存取您的 app service 環境:  <br/><br/>
    a) 您是否想要讓使用網際網路來存取您的 API，您可以設定 HTTP 基本存取驗證 app service 環境之間現有的 API。 Go [here](powerapps-configure-apis.md) to see how. <br/><br/>
    b) 如果您想要保留您的 API，您的組織網路內，您可以安全地存取您組織的網路設定虛擬網路的 app service 環境。 Go [here](../app-service-app-service-environment-intro.md) to see how.

3. 在 [Azure 入口網站](https://portal.azure.com/), ，請選取 **PowerApps**, ，然後選取 **管理 Api**:  
![][11]
4. 在 Api 管理中，選取 **新增**:  
![][12]
5. 在 [Add API] (新增 API)**** 中，輸入 API 屬性：

    - 在 [Name] (名稱)**** 中，輸入 API 的名稱。 請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。 設定有意義且在組織中獨一無二的名稱。
    - 在 [**來源**] 中選取 [**從 Swagger 2.0 匯入**]。

6. 在 **API 定義 (Swagger 2.0)**, 上, 傳 Swagger 2.0 API 定義檔案:  
 ![][13]
7. 選取 [ADD] (新增)**** 以完成這些步驟。

> [AZURE.TIP] 當您註冊 API 時，您要註冊 API app service 環境。 一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。

## 摘要和後續步驟

在本主題中，您已經了解如何從 Swagger 2.0 API 定義註冊 API。 以下是一些讓您深入了解 PowerApps 的相關主題與資源：

- [設定 API 的屬性](powerapps-configure-apis.md)
- [使用者存取權授與 Api](powerapps-manage-api-connection-user-access.md)
- [開始建立您的應用程式中 PowerApps](https://powerapps.microsoft.com/tutorials/)



[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png 
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png 
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png 

