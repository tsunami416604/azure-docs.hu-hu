<properties
    pageTitle="App 模型 v2.0 限制事項 | Microsoft Azure"
    description="Azure AD v2.0 應用程式模型限制事項清單。"
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

# 應用程式模型 v2.0 預覽：限制事項

在公開預覽期間，v2.0 應用程式模型有數個功能未獲支援。  在 v2.0 應用程式模型正式運作之前，這些限制全部都會移除；但如果您在公開預覽期間建置應用程式，請務必注意這些限制。

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 支援生產環境應用程式
與 v2.0 應用程式模型整合的應用程式，不應該當成生產環境層級的應用程式公開發行。  V2.0 應用程式模型目前仍處於公開預覽，隨時可能發布重大變更，此項服務也不提供任何 SLA 保證。  並非所有發生的事件都會受到支援。  如果您願意接受開發中服務帶來的相依性風險，請務必與我們 @AzureAD 連絡，討論您的應用程式或服務的範圍。

## 應用程式限制
v2.0 應用程式模型公開預覽目前不支援下列類型的應用程式。  如需支援的應用程式類型的說明，請參閱 [這篇文章](active-directory-v2-flows.md)。

##### 單一頁面應用程式 (Javascript)
許多新式的應用程式都有單一頁面應用程式前端，以 javascript 編碼為主，而且通常使用 AngularJS、Ember.js、Durandal 等 SPA 架構。通常可以使用 Azure AD 服務支援使用這些應用程式 [OAuth 2.0 隱含流程](active-directory-v2-protocols.md#oauth2-implicit-flow) -但是，此流程尚無法使用 2.0 版應用程式模型中。  近期內可望提供。

如果您想要取得 SPA 使用 2.0 版應用程式模型，您可以實作驗證使用 [web 應用程式流程](active-directory-v2-flows.md#web-apps)。  但這不是建議的方法，而這個案例的相關文件也有所限制。  如果您想要的感覺 SPA 案例，您可以簽出 [正式推出的 Azure AD SPA 程式碼範例](active-directory-devquickstarts-angular.md)。

##### 精靈/伺服器端應用程式
包含長時執行處理序或不需要使用者操作的應用程式，也需他法存取受保護的資源，例如 Web API。  這些應用程式可以驗證並取得使用應用程式的身分識別 （而非使用者的委派的識別） 使用的權杖 [OAuth 2.0 用戶端認證流程](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow)。  

v2.0 應用程式模型目前不支援此流程，也就是說應用程式只能在互動式使用者登入流程之後取得權杖。  近期內即會加入用戶端認證流程。  如果您想要查看用戶端認證流程中正式推出 Azure AD 應用程式模型，看看 [GitHub 上的服務精靈範例](https://github.com/AzureADSamples/Daemon-DotNet)。

##### 鏈結的 Web API (代理者)
許多架構包含需要呼叫另一個下游 Web API 的 Web API，而這兩者都受到 v2.0 應用程式模型的保護。  有 Web API 後端的原生用戶端中常出現這種情況，而呼叫 Office 365 或 Graph API 等 Microsoft 線上服務。

可支援此鏈結的 Web API 案例使用 OAuth 2.0 Jwt 承載認證授與，又稱為 [On-Behalf-Of 流程](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)。  不過，v2.0 應用程式模型預覽目前尚未施行代理者流程。  若要查看此流程可供使用的 Azure AD 中的運作方式服務，請參閱 [代表的程式碼範例在 GitHub 上](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

##### 獨立的 Web API
在 2.0 版應用程式模型預覽中，您有能力 [建立使用 OAuth 權杖而受到保護的 Web API](active-directory-v2-flows.md#web-apis) 從 2.0 版的端點。  不過，該 Web API 只能接收來自共用相同應用程式 ID 之用戶端的權杖。  不支援建置從數個不同用戶端存取的協力廠商 Web 服務。

若要了解如何建立可接受來自已知的用戶端使用相同的應用程式 Id 權杖的 Web API，請參閱 v2.0 應用程式模型 Web API 範例，在我們 [開始](active-directory-appmodel-v2-overview.md#getting-started) 一節。

## 使用者限制
目前使用 v2.0 應用程式模型建置的每個應用程式，都會開放給具有 Microsoft 帳戶或 Azure AD 帳戶的所有使用者。 具有任一種帳戶的任何使用者都能夠順利安裝或瀏覽至您的應用程式，在 v2.0 應用程式模型中輸入其認證，並同意您的應用程式權限。  您可以撰寫應用程式程式碼，拒絕特定集合的使用者登入，但這無法阻止他們同意應用程式。

實際上，您的應用程式無法限制可以登入應用程式的使用者類型。  您無法建置企業營運應用程式 (使用者必須從屬於同一個組織)、僅供企業使用者使用的應用程式 (有 Azure AD 帳戶的使用者)，或僅供消費者使用的應用程式 (有 Microsoft 帳戶的使用者)。

## 應用程式註冊限制
在此時間點，想要整合與 2.0 版應用程式模型的所有應用程式必須建立新的應用程式註冊在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)。  任何現有的 Azure AD 或 Microsoft 帳戶應用程式都與 v2.0 應用程式模型不相容，除新的應用程式註冊入口網站以外，在任何入口網站中註冊的應用程式也不相容。  應用程式沒有任何可從正式運作的 Azure AD 服務移至 v2.0 應用程式模型的移轉路徑。

同樣地，在新的應用程式註冊入口網站中註冊的應用程式將會以獨佔方式使用 v2.0 應用程式模型。  您無法使用應用程式註冊入口網站來建立將與 Azure AD 或 Microsoft 帳戶服務成功整合的應用程式。

在新的應用程式註冊入口網站中註冊的應用程式，目前僅限一組受限的 redirect_uri 值。  Web 應用程式和服務的 redirect_uri 必須以配置或 `https` 開頭，而所有其他平台的 redirect_uri 則必須使用 `urn:ietf:oauth:2.0:oob` 的硬式編碼值。

若要了解如何註冊新的應用程式註冊入口網站中的應用程式，請參閱 [這篇文章](active-directory-v2-app-registration.md)。

## 服務和 API 限制
2.0 版應用程式模型目前支援登入註冊新的應用程式註冊入口網站，在任何應用程式提供落入清單 [支援驗證流程](active-directory-v2-flows.md)。  不過，這些應用程式將只能取得 OAuth 2.0 存取權杖，獲得非常有限的資源。  v2.0 端點只會為下列項目發行 access_token：

- 要求權杖的應用程式。  如果邏輯應用程式是由數個不同的元件或層級組成，應用程式就可以為自己取得 access_token。  若要查看此案例中的動作，請查看我們 [開始](active-directory-appmodel-v2-overview.md#getting-started) 教學課程。
- Outlook 郵件、 行事曆和連絡人 REST Api，這些都是位於 https://outlook.office.com。  若要了解如何撰寫可存取這些 Api 應用程式，請參閱這些 [開始使用 Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 教學課程。

近期內將增加更多 Microsoft 線上服務，以及對您 Web API 和服務的支援。

## 程式庫與 SDK 限制
並非所有語言和平台都有支援 v2.0 應用程式模型預覽的文件庫。  驗證程式庫集合目前僅適用於 .NET、iOS、Android、NodeJS 和 Javascript。  對應的程式碼範例和教學課程，每個都在我們 [開始](active-directory-appmodel-v2-overview.md#getting-started) 一節。

如果您想要使用其他語言或平台的 2.0 版應用程式模型整合應用程式，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定參照](active-directory-v2-protocols.md) 這將會指導您如何建構 v2.0 端點進行通訊所需的 HTTP 訊息。

## 通訊協定限制
v2.0 應用程式模型支援 Open ID Connect 和 OAuth 2.0。  不過，並非每個通訊協定的所有特性與功能都已納入 v2.0 應用程式模型。  部分範例包括：

- 對 OpenID Connect `prompt` 參數的完整支援
- OpenID Connect `login_hint` 參數
- OpenID Connect `domain_hint` 參數
- OpenID Connect `end_sesssion_endpoint`

若要進一步了解 v2.0 應用程式模型中支援的通訊協定功能的範圍，閱讀我們 [OpenID Connect 和 OAuth 2.0 通訊協定參照](active-directory-v2-protocols.md)。


