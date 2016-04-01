<properties
    pageTitle="應用程式模型 v2.0 | Microsoft Azure"
    description="公開上市的 Azure AD 與 v2.0 應用程式模型公開預覽版之間的比較。"
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

# 應用程式模型 v2.0 預覽版本：有何差異？

如果您熟悉公開上市 Azure AD 服務或過去已整合應用程式與 Azure AD，v2.0 應用程式模型會有一些您預期不到的差異。  本文件集結了這些差異，讓您得以瞭解。

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。


## Microsoft 帳戶和 Azure AD 帳戶
V2.0 應用程式模型可讓開發人員撰寫可接受使用單一端點同時從 Microsoft 帳戶和 Azure AD 帳戶登入的應用程式。  這可讓您撰寫您的應用程式且帳戶完全無從驗證；可以忽略使用者登入時所用的帳戶類型。  當然，您 *可以* 讓您的應用程式知道某個特定的工作階段中使用的帳戶類型，但您不需要。

比方說，如果您的應用程式呼叫 [Office 365 REST Api](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), ，一些額外的功能和資料會供企業使用者，例如其 SharePoint 網站或目錄資料。  但對於許多動作，例如 [讀取使用者的郵件](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), ，撰寫程式碼可以完全相同的 Microsoft 帳戶與 Azure AD 的帳戶。  

整合應用程式與 Microsoft 帳戶和 Azure AD 帳戶現在是一個簡單的程序。  您可以利用一組端點、單一資源庫和單一應用程式註冊來進入消費者和企業的世界。  若要深入了解 v2.0 應用程式模型預覽，請參閱 [概觀](active-directory-appmodel-v2-overview.md)。


## 新的應用程式註冊入口網站
2.0 版應用程式模型會要求您註冊您的 Microsoft 應用程式在新的位置 ︰ [apps.dev.microsoft.com](https://apps.dev.microsoft.com)。  您可以在這個入口網站取得應用程式識別碼、自訂您的應用程式登入頁面的外觀等等。  我們會繼續將越來越多的功能加入至這個應用程式註冊入口網站，但主要根據我們在預覽期間收到的意見反應。  其目的在於讓這個入口網站成為可用來管理與您的 Microsoft 應用程式相關的所有一切的新位置。

而最棒的部分是，您不需要 Azure 或 Office 訂用帳戶即可使用。  您只需要有個人 Microsoft 帳戶或公司/學校帳戶。

請注意，現在，在新的應用程式註冊入口網站中註冊的應用程式只適用於應用程式模型 v2.0，和 *只* 在新的應用程式註冊入口網站中註冊的應用程式將會使用應用程式模型 v2.0。  如果您嘗試使用上述其中一個應用程式搭配正式提供的 Microsoft 帳戶或 Azure AD 服務，或嘗試使用現有的應用程式搭配 v2.0 應用程式模型，可能會發生不相容的情形。


## 所有平台使用一個應用程式識別碼
在 GA Azure AD 服務中，您可能已針對單一專案註冊數個不同的應用程式。  您必須針對原生用戶端和 Web 應用程式使用個別的應用程式註冊：

![舊版應用程式註冊 UI](../media/active-directory-v2-flows/old_app_registration.PNG)

例如，如果您已建置網站和 iOS 應用程式，您必須使用兩個不同的應用程式識別碼分別註冊。  如果您有網站和後端 Web API，您可能已在 Azure AD 中將每個 API 註冊為個別的應用程式。  如果您有 iOS 應用程式和 Android 應用程式，您也可能已註冊兩個不同的應用程式。  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

現在，您的每一個專案只需要有單一應用程式註冊和單一應用程式識別碼。  您可以將數個「平台」加入至每個專案，並且為您加入的每個平台提供適當的資料。  當然，您可以根據您的需求建立任意數量的應用程式，但在大多數的情況下應該只需要一個應用程式識別碼。

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

我們的目標是促成更簡化的應用程式管理及開發經驗，並且為您可能正在處理的單一專案建立更加整合的檢視。


## 範圍，而非資源
在 Azure AD 上市的服務中，應用程式可以表現 **資源**, ，或權杖的收件者。  資源可以定義一些 **範圍** 或 **oAuth2Permissions** 它所了解，讓用戶端應用程式來要求權杖，該資源的一組特定的範圍。  請考慮以 Azure AD Graph API 做為資源的範例：

- 資源識別碼，或`AppID URI`：`https://graph.windows.net/`
- 範圍，或`OAuth2Permissions`：`Directory.Read`、`Directory.Write` 等。  

這一切都適用於 v2.0 應用程式模型。  應用程式仍可做為資源、定義範圍並依據 URI 識別。  用戶端應用程式仍可要求存取這些範圍。  不過，用戶端用來要求這些權限的方式已改變。  在過去，Azure AD 的 OAuth 2.0 授權要求可能如下所示：

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

其中 **資源** 參數指出哪一個資源的用戶端應用程式要求的授權。  Azure AD 根據 Azure 入口網站中的靜態設定計算應用程式所需的權限，並據以發出權杖。  現在，相同的 OAuth 2.0 授權要求如下所示：

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

其中 **範圍** 參數會指出哪一個資源和權限的應用程式所要求的授權。 所需的資源仍是要求中最新的 - 它只會包含在 scope 參數的每個值中。  以此方式使用 scope 參數可讓 v2.0 應用程式模型更符合 OAuth 2.0 規格，和貼近常見的業界作法。  它也可讓應用程式以執行 [增量同意](#incremental-and-dynamic-consent), ，其說明已在下一節。

## 增量和動態同意
在公開上市 Azure AD 服務中註冊的應用程式必須於應用程式建立時，在 Azure 入口網站中指定其所需的 OAuth 2.0 權限：

![權限註冊 UI](../media/active-directory-v2-flows/app_reg_permissions.PNG)

設定應用程式所需的權限 **靜態**。  雖然這可讓應用程式的設定存在於 Azure 入口網站中並使程式碼好用又簡單，但開發人員會面臨一些問題：

- 應用程式必須在應用程式建立時知道可能會需要的所有權限。  隨著時間新增權限有所困難。
- 應用程式必須事先知道可能會存取的所有資源。  很難建立能夠存取任意數目的資源的應用程式。
- 應用程式必須在使用者第一次登入時要求可能會需要的權限。  在某些情況下，這會導致非常冗長的權限清單，而讓使用者在初始登入時打消核准應用程式存取權的念頭。

在 2.0 版應用程式模型中，您可以指定的權限的應用程式需求 **動態**, ，在執行階段，在您的應用程式的一般使用期間。  若要這麼做，您可以在授權要求的 `scope` 參數中包含範圍，以在任何指定的時間點指定您的應用程式所需的範圍：

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

應用程式的上述要求權限可讀取 Azure AD 使用者的目錄資料，以及將資料寫入至其目錄。  如果使用者過去曾針對此特定應用程式同意這些權限，他們只要輸入其認證並登入應用程式。  如果使用者未曾同意這些權限的任何一項，v2.0 端點會要求使用者同意這些權限。  若要深入了解，您可以研讀 [權限、 同意，以及範圍](active-directory-v2-scopes.md)。

允許應用程式透過 `scope` 參數動態要求權限，您即可完全掌控使用者的經驗。  如果您希望，您可以選擇將您的同意體驗提前，並在一個初始授權要求中要求所有的權限。  或者，如果您的應用程式需要大量的權限，您可以選擇在嘗試使用您的應用程式的某些功能時，以遞增方式向使用者收集這些權限。

## 離線存取
V2.0 應用程式模型為應用程式引進了新的知名權限 - `offline_access` 範圍。  如果應用程式需要長期代表使用者存取資源，則所有應用程式都需要要求此權限，即使使用者可能不會主動使用此應用程式亦然。  在同意對話方塊中，`offline_access` 範圍會對使用者顯示做為「離線存取您的資料」，而使用者必須加以同意。  要求 `offline_access` 權限可讓您的 Web 應用程式從 v2.0 端點獲取 OAuth 2.0 refresh_token。  Refresh_token 屬於長效權杖，可用來交換新的 OAuth 2.0 access_token 以延長存取期間。  

如果您的應用程式未要求 `offline_access` 範圍，則不會收到 refresh_token。  這表示當兌換中的 authorization_code [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow), ，您只會收到回從 access_token `/oauth2/token` 端點。  該 access_token 會短時間維持有效 (通常是一小時)，但最後終將過期。  屆時，您的應用程式必須將使用者重新導向回到 `/oauth2/authorize` 端點以擷取新的 authorization_code。  在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意權限。

若要深入了解 OAuth 2.0、 refresh_tokens 和 access_tokens，請參閱 [v2.0 應用程式模型的通訊協定參照](active-directory-v2-protocols.md)。

## 權杖宣告
V2.0 端點所簽發的權杖中的宣告與公開上市 Azure AD 端點所簽發的權杖不會相同 - 移轉至新服務的應用程式不應假設特定的宣告會存在於 id_token 或 access_token 中。   V2.0 端點所簽發的權杖與 OAuth 2.0 和 OpenID Connect 規格相容，但可能會遵循與公開上市 Azure AD 服務不同的語意。

若要深入了解特定 v2.0 應用程式模型權杖中發出的宣告，請參閱 [v2.0 語彙基元參考](active-directory-v2-tokens.md)。


## Preview 限制
在公開預覽期間建置具有 v2.0 應用程式模型的應用程式時，有一些限制要注意。  請參閱 [v2.0 應用程式模型限制的文件](active-directory-v2-limitations.md) 查看如果任何這些限制適用於您特定的案例。


