<properties 
    pageTitle="如何在 Azure API 管理中使用 OAuth 2.0 授權開發人員帳戶" 
    description="了解如何在 API 管理中使用 OAuth 2.0 來授權給使用者。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="sdanie"/>

# 如何在 Azure API 管理中使用 OAuth 2.0 授權開發人員帳戶

許多 Api 都支援 [OAuth 2.0](http://oauth.net/2/) 來保護 API，並確保只有有效的使用者存取，而且他們只能存取他們正在權的資源。 為了搭配使用 Azure API 管理的互動式開發人員主控台與這類 API，服務可讓您設定服務執行個體來使用已啟用 OAuth 2.0 的 API。

## <a name="prerequisites"> </a>必要條件

本指南將示範如何設定 API 管理服務執行個體，以便使用開發人員帳戶適用的 OAuth 2.0 授權，但並未示範如何設定 OAuth 2.0 提供者。 儘管步驟相似，且用來在 API 管理服務執行個體中設定 OAuth 2.0 所需的資訊也相同，但每個 OAuth 2.0 提供者的組態並不相同。 本主題演示的範例將 Azure Active Directory 當做 OAuth 2.0 提供者。

>[AZURE.NOTE] 如需有關如何使用 Azure Active Directory 設定 OAuth 2.0 的詳細資訊，請參閱 [研究 Webapp-graphapi-dotnet WebApp DotNet][] 範例。

## <a name="step1"> </a>在 API 管理中設定 OAuth 2.0 授權伺服器

若要開始使用，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>[AZURE.NOTE] 如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **安全性** 從 **API 管理** 左側，按一下 **OAuth 2.0**, ，然後按一下 [ **新增授權伺服器**。

![OAuth 2.0][api-management-oauth2]

按一下後 **新增授權伺服器**, ，會顯示新的授權伺服器表單。

![New server][api-management-oauth2-server-1]

輸入的名稱和選擇性的描述中 **名稱** 和 **描述** 欄位。 

>[AZURE.NOTE] 這些欄位來識別目前 API 管理服務執行個體內的 OAuth 2.0 授權伺服器，而且其值並非來自 OAuth 2.0 伺服器。

輸入 **用戶端註冊頁面 URL**。 此頁面可供使用者建立及管理帳戶，並且會因為使用的 OAuth 2.0 提供者不同而有所差異。  **用戶端註冊頁面 URL** 指向使用者可用來建立及設定 OAuth 2.0 提供者支援使用者帳戶管理他們自己的帳戶的頁面。 即使 OAuth 2.0 提供者支援這項功能，有些組織還是未設定或未使用這項功能。 如果您的 OAuth 2.0 提供者未設定使用者帳戶管理，請在這裡輸入預留位置 URL (例如您公司的 URL) 或 `https://placeholder.contoso.com` 這類 URL。

表單的下一個區段包含 **授權碼授與類型**, ，**授權端點 URL**, ，和 **授權要求方法** 設定。

![New server][api-management-oauth2-server-2]

指定 **授權碼授與類型** 藉由檢查需要的類型。 **授權碼** 的預設指定。

輸入 **授權端點 URL**。 對於 Azure Active Directory，此 URL 將與下列 URL 相似；其中，`<client_id>` 將取代為向 OAuth 2.0 伺服器識別應用程式的用戶端識別碼。

    https://login.windows.net/<client_id>/oauth2/authorize

 **授權要求方法** 指定授權要求傳送到 OAuth 2.0 伺服器的方式。 根據預設 **取得** 已選取。

下一節正是 **權杖端點 URL**, ，**用戶端驗證方法**, ，**傳送方法的存取權杖**, ，和 **預設範圍** 所指定。

![New server][api-management-oauth2-server-3]

對於 Azure Active Directory OAuth 2.0 伺服器， **權杖端點 URL** 將具有下列格式，其中 `<APPID>`  的格式為 `yourapp.onmicrosoft.com`。

    https://login.windows.net/<APPID>/oauth2/token

預設設定 **用戶端驗證方法** 是 **基本**, ，和  **傳送方法的存取權杖** 是 **授權標頭**。 這些值上的表單，這一節設定連同 **預設範圍**。

 **用戶端認證** 區段包含 **用戶端識別碼** 和 **用戶端密碼**, ，這兩個取得的 OAuth 2.0 伺服器的建立和設定程序。 一次 **用戶端識別碼** 和 **用戶端密碼** 未指定， **redirect_uri** 的 **授權碼** 產生。 此 URI 可用來設定 OAuth 2.0 伺服器組態中的回覆 URL。

![New server][api-management-oauth2-server-4]

如果 **授權碼授與類型** 設為 **資源擁有者密碼**, 、 **資源擁有者密碼認證** 區段用來指定這些認證，否則您可以將它保留空白。

![New server][api-management-oauth2-server-5]

完成表單後，按一下 [ **儲存** 儲存 API 管理 OAuth 2.0 授權伺服器設定。 儲存伺服器組態後，您便可以設定 API 以使用此組態，如下一個小節所述。

## <a name="step2"> </a>設定 API 以使用 OAuth 2.0 使用者授權

按一下 [ **Api** 從 **API 管理** 左側，按一下所需之 API 的名稱按一下 **安全性**, ，然後核取方塊 **OAuth 2.0**。

![User authorization][api-management-user-authorization]

選取想要的 **授權伺服器** 從下拉式清單中，然後按一下 **儲存**。

![User authorization][api-management-user-authorization-save]

## <a name="step3"> </a>在開發人員入口網站中測試 OAuth 2.0 使用者授權

設定好 OAuth 2.0 授權伺服器並將 API 設定為使用該伺服器後，您可以前往開發人員入口網站並呼叫 API 來進行測試。  按一下 [ **開發人員入口網站** 右上方功能表中。

![開發人員入口網站][api-management-developer-portal-menu]

按一下 [ **Api** 在上方功能表中，選取 **Echo API**。

![Echo API][api-management-apis-echo-api]

>[AZURE.NOTE] 如果您有設定，或可以看到您的帳戶只有一個 API，然後按一下 Api 將帶您直接前往該 api 的作業。

選取 **GET Resource** 作業中，按一下 [ **開啟主控台**, ，然後選取 **授權碼** 從下拉式清單。

![Open console][api-management-open-console]

當 **授權碼** 已選取，快顯視窗會顯示含有 OAuth 2.0 提供者的登入表單。 在此範例中，登入表單是由 Azure Active Directory 提供。

>[AZURE.NOTE] 如果您已停用的快顯視窗，讓他們的瀏覽器會提示您。 選取 [啟用後， **授權碼** 一次，將會顯示登入表單。

![Sign in][api-management-oauth2-signin]

一旦您已登入， **要求標頭** 會填入 `Authorization : Bearer` 授權要求的標頭。

![Request header token][api-management-request-header-token]

此時，您可以針對剩餘的參數設定需要的值，然後再提交要求。 

## 後續步驟

如需有關如何使用 OAuth 2.0 和 API 管理的詳細資訊，請參閱下列影片以及隨附 [文章](api-management-howto-protect-backend-with-aad.md)。

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps



