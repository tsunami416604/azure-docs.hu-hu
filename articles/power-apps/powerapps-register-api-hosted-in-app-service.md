<properties
    pageTitle="在 PowerApps Enterprise 中開發或建立 App Service 環境代管的 API |Microsoft Azure"
    description="了解如何在 Azure 入口網站中註冊 App Service 環境代管的自訂 API"
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


# 註冊 App Service 環境代管的 API

PowerApps 支援註冊託管於雲端或內部部署任何位置的 API，其功能非常強大。 在某些情況下，您可能想要開發或建立一些新的 API。 例如，您可能會想要：

- 實作新的功能供貴組織使用。
- 建置在現有的功能或資料之上，可以為使用者提供更好的應用程式建置體驗。

當您在 app service 環境中裝載您的 Api 時，您利用現有的全部功能， [app service 環境](../app-service-app-service-environment-intro.md), ，也會取得較佳的整合經驗。

若要在您的應用程式中使用這些 API，您必須在 Azure 入口網站中「註冊」API。 可以使用下列選項：

- 註冊預先建置 [Microsoft managed API 或 IT 管理 API](powerapps-register-from-available-apis.md)。
- 註冊您的 App Service 環境 (在本主題中) 內裝載的 Web 應用程式、API 應用程式，以及行動應用程式。
- 註冊您自己的 Swagger Api 使用的其中一個 [Swagger 2.0 API 定義](powerapps-register-existing-api-from-api-definition.md)。

本文說明如何**註冊您的 App Service 環境內裝載的 Web 應用程式、API 應用程式，以及行動應用程式**。

#### 開始使用的必要條件

- 申請 [PowerApps 企業](powerapps-get-started-azure-portal.md)。
- 建立 [app service 環境](powerapps-get-started-azure-portal.md)。


## 在 App Service 環境中開發和部署 API

在 App Service 環境中開發 API 很簡單。 選擇您慣用的程式設計語言來建置 web API，然後使用 [Swagger 2.0](http://swagger.io) 來描述 API 定義。 部分範例包括：

- [建置和部署 Azure App Service 中的.NET](../app-service-api-dotnet-get-started.md)
- [建置和部署 Azure App Service 中的 Java API 應用程式](../app-service-api-java-api-app.md)
- [建置並部署 Node.js API 應用程式在 Azure App Service](../app-service-api-nodejs-api-app.md)

您也可以選擇在 App Service 環境中部署 Web API，包括從 Visual Studio 部署，以及使用原始檔控制系統連續部署。 [Azure App Service 中的 web 應用程式部署](../web-sites-deploy.md) 的良好資源。

## 在 App Service 環境中註冊您的自訂 API

API 部署至 App Service 環境之後，請使用下列步驟註冊：

1. 在 [Azure 入口網站](https://portal.azure.com/), ，請選取 **PowerApps**, ，然後選取 **管理 Api**:  
![][11]
2. 在 Api 管理中，選取 **新增**:  
![][12]
3. 在 [Add API] (新增 API)**** 中，輸入 API 屬性：

    - 在 [Name] (名稱)**** 中，輸入 API 的名稱。 請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。 請設定有意義且在組織中獨一無二的名稱。
    - 在 **來源**, ，請選取 **匯入從 App Service 環境中裝載 Api**:  
    ![][13]
4. 在 [App Service 環境代管的 API]**** 中選取要匯入的 API。 此清單會顯示每個 web 應用程式、 API 的應用程式和行動裝置應用程式在 app service 環境具有其 **apiDefinition.url** 設定屬性。 若要匯入 API，它會使用利用這個屬性公開的 Swagger 2.0 API 定義。 請確定當您註冊 API 時，可公開存取此 URL:  
![][14]
5. 選取 [ADD] (新增)**** 以完成這些步驟。

> [AZURE.TIP] 註冊 API 時，您是向您的 App Service 環境註冊 API。 一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。

## 摘要和後續步驟

在本主題中，您已看到如何註冊 App Service 環境代管的 API。 以下是一些讓您深入了解 PowerApps 的相關主題與資源：

- [設定 API 的屬性](powerapps-configure-apis.md)
- [使用者存取權授與 Api](powerapps-manage-api-connection-user-access.md)
- [開始建立您的應用程式中 PowerApps](https://powerapps.microsoft.com/tutorials/)



[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png 
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png 
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png 
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png 

