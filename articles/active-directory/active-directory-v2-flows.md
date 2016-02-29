<properties
    pageTitle="App 模型 v2.0 App 的類型 | Microsoft Azure"
    description="Azure AD 應用程式模型 v2.0 公開預覽版本支援的應用程式與案例類型。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽：應用程式類型
2.0 版應用程式模型支援各種現代應用程式架構，其中都根據產業標準通訊協定驗證 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 和/或 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)。  此文件簡要描述您可以建置的應用程式類型，不涉及您慣用的語言或平台。  它將協助您瞭解的高層級的案例之前 [直接從程式碼](active-directory-appmodel-v2-overview.md#getting-started)。

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 基本概念
使用 2.0 版應用程式模型，每個應用程式必須在註冊 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)。  應用程式註冊處理序會收集與指派一些值給您的應用程式：

-  **應用程式識別碼** ，可唯一識別您的應用程式
- A **重新導向 URI** ，可用來直接回應給您的應用程式
- 其他幾個狀況特定的值。  如需詳細資訊，了解如何 [註冊應用程式](active-directory-v2-app-registration.md)。

註冊之後，應用程式即會與 Azure AD 通訊，向 v2.0 端點傳送要求：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

應用程式與 v2.0 端點的所有互動都會遵循類似高階的模式：

1. 應用程式會將使用者導向至 v2.0 端點以進行登入。
2. 使用者驗證時，要輸入其使用者名稱和密碼或其他資訊。
3. 使用者授權應用程式代其執行作業的方法，是授與應用程式要求的權限。
4. 應用程式會從 v2.0 端點接收某種安全性權杖。
5. 應用程式使用此安全性權杖存取受保護的資訊或資源。
6. 資源伺服器會驗證安全性權杖，以確保可以授與存取權。
7. 應用程式會定期重新整理安全性權杖。

<!-- TODO: Need a page for libraries to link to -->
這七個步驟的每一個都會根據您建置的應用程式類型而略有不同，而我們公開的原始碼程式庫會為您顧及細節。  您可以深入了解 [permissions, consent, and scopes](active-directory-v2-scopes.md), ，或讀取在探索一些具體的範例。

## Web Apps
針對 web 應用程式 (.NET、 PHP、 Java、 Ruby、 Python、 節點等等)，可以透過瀏覽器存取，2.0 版應用程式模型可支援使用者登入使用 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)。  在 OpenID Connect 中，Web 應用程式會接收到 `id_token`，這是一個安全性權杖，可驗證使用者的身分識別並以宣告形式提供使用者的相關資訊：

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

您可以了解所有語彙基元和應用程式中可用的宣告類型 [v2.0 語彙基元參考](active-directory-v2-tokens.md)。

在 Web 伺服器應用程式中，登入驗證流程會採用下列高階步驟：

![Web 應用程式泳道映像](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

使用接收自 v2.0 端點的公開簽署金鑰的 id_token 驗證，足以確保使用者的身分識別，以及設定可在後續頁面要求用來識別使用者的工作階段 Cookie。

若要查看此案例中的動作，試試 web 應用程式登入的程式碼範例中的其中一個我們 [開始](active-directory-appmodel-v2-overview.md#getting-started) 一節。

除了簡易登入，Web 伺服器應用程式可能也需要存取一些其他 Web 服務，例如 REST API。  在此情況下，web 伺服器應用程式可以參與結合 OpenID Connect 和 OAuth 2.0 流程，請使用 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow)。 這種情況下底下有涵蓋 [Web Api 區段](#web-apis), ，然後在我們 [WebApp WebAPI 快速入門主題](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## Web API
您也可以使用 v2.0 應用程式模型來保護 Web 服務，例如應用程式的 RESTful Web API。  Web API 使用 OAuth 2.0 access_token 保護資料及驗證連入要求，而不使用 id_token 和工作階段 Cookie。  Web API 的呼叫端尾部會附加 HTTP 要求授權標頭的 access_token：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

然後 Web API 會使用這個 access_token 來驗證 API 呼叫端的身分識別，並從編碼在 access_token 中的宣告擷取呼叫端的相關資訊。  您可以了解所有語彙基元和應用程式中可用的宣告類型 [v2.0 語彙基元參考](active-directory-v2-tokens.md)。

Web API 可以讓使用者選擇-在/選擇退出特定功能或資料的能力藉由公開權限，否則會以 [範圍](active-directory-v2-scopes.md)。  為了讓發出呼叫的應用程式取得範圍的權限，使用者在整個流程中必須同意接受範圍。  V2.0 端點會負責向使用者要求權限，並將這些權限記錄在 Web API 收到的所有 access_token 中。  Web API 只需要關心驗證每次呼叫所收到的 access_token，以及執行適當的授權檢查即可。

Web API 可以接收來自所有類型應用程式的 access_token，包括 Web 伺服器應用程式、桌面和行動應用程式、單一頁面應用程式、伺服器端精靈，甚至是其他的 Web API。  範例：Web 伺服器應用程式呼叫 Web API 的完整流程。

![Web 應用程式 Web API 泳道映像](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

若要深入了解 authorization_codes、 refresh_tokens 和取得 access_tokens 的詳細的步驟，了解 [OAuth 2.0 通訊協定](active-directory-v2-protocols-oauth-code.md)。

若要了解如何安全使用 2.0 版的應用程式模型和 OAuth 2.0 access_tokens Web API，請參閱 Web API 程式碼範例我們 [入門 」 一節](active-directory-appmodel-v2-overview.md#getting-started)。


## 行動和原生應用程式
安裝在裝置中的應用程式 (如行動和桌面應用程式) 通常需要存取儲存資料和代替使用者執行各種功能的後端服務或 Web API。  這些應用程式可以將登入和授權加入使用 v2.0 模型的後端服務和 [OAuth 2.0 授權碼流程](active-directory-v2-protocols-oauth-code.md)。  

在這個流程中，應用程式會在使用者登入時，接收來自 v2.0 端點的 authorization_code，這代表應用程式有權限代替目前登入的使用者呼叫後端服務。  然後應用程式就可以在背景中交換 OAuth 2.0 access_token 和 refresh_token 的 authoriztion_code。  應用程式可以使用 access_token 在 HTTP 要求中向 Web API 驗證，也可以在舊的 access_token 過期時，用 refresh_token 取得新的 access_token。

![原生應用程式泳道映像](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## 單一頁面應用程式 (Javascript)
許多新式的應用程式都有單一頁面應用程式前端，以 javascript 編碼為主，而且通常使用 AngularJS、Ember.js、Durandal 等 SPA 架構。Azure AD 應用程式模型 2.0 版支援使用這些應用程式 [OAuth 2.0 隱含流程](active-directory-v2-protocols-implicit.md)。

在流程中，應用程式會直接收到來自 v2.0 授權端點的權杖，而不需要執行任何後端伺服器對伺服器交換。  這可讓所有的驗證邏輯和工作階段處理完全在 javascript 用戶端中發生，而不需要執行額外的頁面重新導向。

若要查看此案例中的，嘗試在單一頁面應用程式程式碼範例的其中一個我們 [開始](active-directory-appmodel-v2-overview.md#getting-started) 一節。

## 目前的預覽版本限制
v2.0 應用程式模型預覽目前不支援這些類型的應用程式，但計劃於正式運作時提供支援。  其他限制和限制 v2.0 應用程式模型公開預覽版本中所述 [v2.0 預覽限制文章](active-directory-v2-limitations.md)。

### 精靈/伺服器端應用程式
包含長時執行處理序或不需要使用者操作的應用程式，也需他法存取受保護的資源，例如 Web API。  這些應用程式可以驗證並取得使用應用程式的身分識別 (而非使用者的委派的識別) 使用的權杖 [OAuth 2.0 用戶端認證流程](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow)。  

v2.0 應用程式模型目前不支援此流程，也就是說應用程式只能在互動式使用者登入流程之後取得權杖。  近期內即會加入用戶端認證流程。  如果您想要查看用戶端認證流程正式推出的 Azure AD 中的服務，請參閱 [GitHub 上的服務精靈範例](https://github.com/AzureADSamples/Daemon-DotNet)。

### 鏈結的 Web API (代理者)
許多架構包含需要呼叫另一個下游 Web API 的 Web API，而這兩者都受到 v2.0 應用程式模型的保護。  有 Web API 後端的原生用戶端中常出現這種情況，而呼叫 Office 365 或 Graph API 等 Microsoft 線上服務。

可支援此鏈結的 Web API 案例使用 OAuth 2.0 Jwt 承載認證授與，又稱為 [On-Behalf-Of 流程](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)。  不過，v2.0 應用程式模型預覽目前尚未施行代理者流程。  若要查看此流程可供使用的 Azure AD 中的運作方式服務，請參閱 [代表的程式碼範例在 GitHub 上](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

