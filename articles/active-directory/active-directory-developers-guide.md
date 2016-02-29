<properties
   pageTitle="Azure Active Directory 開發人員指南 | Microsoft Azure"
   description="本文提供 Azure Active Directory 開發人員導向資源的完整指南。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/10/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 開發人員指南

## 概觀
Azure Active Directory 是身分識別管理即服務 (IDMaaS) 平台，提供有效的方法，協助開發人員將身分識別管理整合到他們的應用程式中。 下列文章提供實作的概觀和 Azure Active Directory 的重要功能。 我們建議您按順序閱讀，或跳到 [開始](#getting-started) 如果您已經準備好。


1. [Azure Active Directory 整合的優點](active-directory-how-to-integrate.md): 探索為什麼與 Azure Active Directory 整合是提供安全登入和授權的最佳解決方案。

1. [Active Directory 驗證案例](active-directory-authentication-scenarios.md): 利用簡易驗證來登入您的應用程式提供的 Azure Active Directory 中。

1. [整合應用程式與 Azure Active Directory](active-directory-integrating-applications.md): 了解如何加入、 更新及移除應用程式，從 Azure Active Directory，以及整合式應用程式的商標指導方針。

1. [Azure Active Directory Graph API](active-directory-graph-api.md): 使用 Azure Active Directory Graph API 以程式設計方式透過 REST API 端點存取 Azure Active Directory。 請注意，Azure AD Graph API 也可透過存取 [Microsoft Graph](https://graph.microsoft.io/), 、 統一的 API，可讓您存取多個 Microsoft 雲端服務 Api 透過單一的 REST API 端點，並以單一的存取權杖。

1. [Azure Active Directory 驗證程式庫](active-directory-authentication-libraries.md): 輕鬆地驗證使用者，以使用 Azure 驗證程式庫取得存取權杖。


## 開始使用

這些教學課程適用於多種平台，可協助您快速開始使用 Azure Active Directory 進行開發。 必要條件是，您必須 [取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)。

### 行動裝置與電腦應用程式快速入門指南

|[![i作業系統](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![A] ndroid(./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows 電話](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows 存放區](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![X] amarin(./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![C] ordova(./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows 市集](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Web 應用程式快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![J] ava(./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![J] avascript(./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![N] ode.js(./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Web API 快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![N] ode.js(./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### 查詢目錄快速入門指南

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## 作法

這些文章描述如何使用 Azure Active Directory 執行特定工作︰

- [取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)
- [在 Azure Active Directory 應用程式資源庫中列出您的應用程式](active-directory-app-gallery-listing.md)
- [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
- [使用 Office 365 API 建立應用程式](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [提交 Office 365 的 Web 應用程式到賣方儀表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [預覽：如何建置可同時透過個人和公司/學校帳戶登入使用者的應用程式](active-directory-appmodel-v2-overview.md)
- [預覽：如何建置註冊與登入取用者的應用程式](active-directory-b2c-overview.md)


## 參考

這些文章提供 REST 和驗證程式庫 API、通訊協定、錯誤、程式碼範例和端點的基礎參考。  

###  支援
- [標記問題](http://stackoverflow.com/questions/tagged/azure-active-directory): 尋找的堆疊溢位的 Azure Active Directory 的解決方案，藉由搜尋標記 [azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory) 和 [adal](http://stackoverflow.com/questions/tagged/adal)。

### 代碼

- [Azure Active Directory 開放原始碼程式庫](http://github.com/AzureAD): 尋找媒體櫃的原始程式檔最簡單的方法是使用我們 [程式庫清單](active-directory-authentication-libraries.md)。

- [Azure Active Directory 範例](http://github.com/AzureADSamples): 瀏覽範例清單最簡單的作法是使用 [的程式碼範例索引](active-directory-code-samples.md)。


### Graph API

- [Graph API 參考](https://msdn.microsoft.com/library/azure/hh974476.aspx): Azure Active Directory Graph API 的 REST 參考。 [檢視的互動式 Graph API 參考體驗](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- [Graph API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): 用來控制應用程式有租用戶中的目錄資料的存取權的 OAuth 2.0 權限範圍。


### 驗證通訊協定

- [SAML 2.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn195591.aspx): SAML 2.0 通訊協定可讓應用程式提供單一登入體驗給使用者。


- [OAuth 2.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn645545.aspx): 您可以使用 OAuth 2.0 通訊協定來授權存取 web 應用程式和 web Api，您的 Azure Active Directory 租用戶中。


- [OpenID Connect 1.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn645541.aspx): OpenID Connect 1.0 通訊協定擴充 OAuth 2.0 以做為驗證通訊協定。


- [WS-同盟 1.2 通訊協定參照](https://msdn.microsoft.com/library/azure/dn903702.aspx): Web 服務同盟 1.2 版規格中指定的 WS-同盟 1.2 通訊協定。

- [支援的權杖和宣告類型](active-directory-token-and-claims.md): 您可以使用本指南，了解和評估 SAML 2.0 和 JSON Web Token (JWT) 權杖中的宣告。

## 影片

### Build 2015

這些概觀簡報有關於使用 Azure Active Directory 功能開發應用程式，適用於直接在工程團隊工作的講者。 簡報涵蓋一些基本主題，包括 IDMaaS、驗證、身分識別同盟和單一登入。

- [Azure Active Directory：新式應用程式的「身分識別管理即服務」](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [使用 Azure Active Directory 開發新式 Web 應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [使用 Azure Active Directory 開發新式原生應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) 是每星期五 1:1 影片系列，專門提供簡短 (10-15 分鐘) 訪談，由專家就各種 Azure 主題。  請使用頁面上的 [服務篩選] 功能，查看所有 Azure Active Directory 影片。

- [Azure 身分識別 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure 身分識別 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure 身分識別 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## 社交

- [Active Directory 團隊部落格](http://blogs.technet.com/b/ad/): 在 Azure Active Directory 的世界中的最新發展。

- [Azure Active Directory 圖形團隊部落格](http://blogs.msdn.com/b/aadgraphteam): 旨在說明 Graph API 的 Azure Active Directory 資訊。

- [雲端識別](http://www.cloudidentity.net): 關於身分識別管理即服務 」，從主要 Azure Active Directory PM。  

- [在 Twitter 上的 azure Active Directory](https://twitter.com/azuread): Azure Active Directory 中 140 個字元或更少的公告。

