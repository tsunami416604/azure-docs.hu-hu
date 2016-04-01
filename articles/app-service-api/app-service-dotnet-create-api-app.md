<properties
    pageTitle="在 Azure App Service | Microsoft Azure 中建立 ASP.NET API 應用程式"
    description="了解如何在 Azure App Service 使用 Visual Studio 2013 建立 ASP.NET API 應用程式。"
    services="app-service\api"
    documentationCenter=".net"
    authors="bradygaster"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/08/2015"
    ms.author="tdykstra"/>

# 在 Azure App Service 中建立 ASP.NET API 應用程式

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

本教學課程示範如何建立 ASP.NET Web API 專案設定部署到雲端，做為 [Azure App Service 中的 API 應用程式](app-service-api-apps-why-best-platform.md)。 如需如何設定現有的 Web API 專案部署為 API 應用程式資訊，請參閱 [Web API 專案設定為 API 應用程式](app-service-dotnet-create-api-app-visual-studio.md)。

這是快速而簡單的教學課程，只會顯示如何使用範本建立 Visual Studio 專案。  中也會顯示一系列的第一個是如何 [部署](app-service-dotnet-deploy-api-app.md) 和 [偵錯](../app-service-dotnet-remotely-debug-api-app.md) 您在本教學課程中建立的 API 應用程式專案。 如需如何處理 API 應用程式的深入報導，請參閱 [後續步驟](#next-steps) 教學課程的最後一節。

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

## 建立 API 應用程式專案

當指示您輸入專案的名稱時，請輸入 **ContactsList**。

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

API 應用程式現在已準備好部署，而且您可以依照 [部署 API 應用程式](app-service-dotnet-deploy-api-app.md) 教學課程，若要這麼做。

如需有關如何使用自動產生的用戶端程式碼來呼叫 API 應用程式的資訊，請參閱 [取用 API 應用程式的.NET 用戶端](app-service-api-dotnet-consume.md)。

如需如何自訂自動產生的 Swagger 中繼資料 API 應用程式資訊，請參閱 [自訂 Swashbuckle 產生的 API 定義](app-service-api-dotnet-swashbuckle-customize.md)。

如需如何建立、 刪除和在 Azure 預覽入口網站中設定 API 應用程式資訊，請參閱 [管理 API 應用程式](app-service-api-manage-in-portal.md)。

如需驗證的 API 應用程式的使用者資訊，請參閱 [API 應用程式和行動應用程式在 Azure App Service 中的驗證](../app-service/app-service-authentication-overview.md)。

API 應用程式功能的相關資訊，請參閱 [什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md)。


