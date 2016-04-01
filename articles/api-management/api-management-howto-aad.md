<properties 
    pageTitle="如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶" 
    description="了解如何在 API 管理中使用 Azure Active Directory 授權使用者" 
    services="api-management" 
    documentationCenter="API Management" 
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

# 如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶


## 概觀
本指南說明如何為一或多個 Azure Active Directory 中的所有使用者啟用開發人員入口網站的存取。 本指南也說明如何管理 Azure Active Directory 的使用者，方法是加入包含 Azure Active Directory 的使用者的外部群組。

>若要完成本指南中的步驟，您必須先具備要在其中建立應用程式的 Azure Active Directory。

## 如何使用 Azure Active Directory 授權開發人員帳戶

若要開始使用，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **安全性** 從 **API 管理** 左邊，然後按一下 [功能表 **外部身分識別**。

![外部身分識別][api-management-security-external-identities]

按一下 [ **Azure Active Directory**。 請記下的 **重新導向 URL** 和切換到您的 Azure Active Directory 在 Azure 傳統入口網站。

![外部身分識別][api-management-security-aad-new]

按一下 [ **新增** 按鈕來建立新的 Azure Active Directory 應用程式，並選擇 **加入我的組織正在開發的應用程式**。

![加入新的 Azure Active Directory 應用程式][api-management-new-aad-application-menu]

輸入應用程式名稱，選取 **Web 應用程式和/或 Web API**, ，然後按一下 [下一步] 按鈕。

![新 Azure Active Directory 應用程式][api-management-new-aad-application-1]

如 **登入 URL**, ，複製 **重新導向 URL** 從 **Azure Active Directory** 區段 **外部身分識別** 在發行者入口網站] 索引標籤上，並移除 **-aad** 從 URL 結尾的後置詞。 在此範例中， **登入 URL** 是 `https://aad03.portal.current.int-azure-api.net/signin`。 

如 **應用程式識別碼 URL**, ，請輸入 Azure Active Directory 的預設網域或自訂網域，並附加唯一的字串。 在此範例中的預設網域的 **https://contoso5api.onmicrosoft.com** 使用後置詞 **/api** 指定。

![新 Azure Active Directory 應用程式屬性][api-management-new-aad-application-2]

按一下核取按鈕以儲存並建立新的應用程式，然後切換至 **設定** 將新的應用程式設定] 索引標籤。

![新 Azure Active Directory 應用程式已建立][api-management-new-aad-app-created]

如果多個 Azure Active Directory 將用於此應用程式，請按一下 [ **是** 的 **應用程式是多租用戶**。 預設值是 **否**。

![應用程式為多租用戶][api-management-aad-app-multi-tenant]

複製 **重新導向 URL** 從 **Azure Active Directory** 區段 **外部身分識別** 在發行者入口網站] 索引標籤上，並將它貼到 **回覆 URL** 文字方塊。 

![回覆 URL][api-management-aad-reply-url]

捲動至底部的 [設定] 索引標籤，選取 **應用程式權限** 下拉式清單中，並檢查 **讀取目錄資料**。

![應用程式權限][api-management-aad-app-permissions]

選取 **委派權限** 下拉式清單中，並檢查 **啟用登入並讀取使用者的設定檔**。

![委派的權限][api-management-aad-delegated-permissions]

>如需應用程式和委派的權限的詳細資訊，請參閱 [存取 Graph API][]。

複製 **用戶端識別碼** 到剪貼簿。

![用戶端識別碼][api-management-aad-app-client-id]

切換回發行者入口網站，並貼入 **用戶端識別碼** 從 Azure Active Directory 應用程式組態複製。

![用戶端識別碼][api-management-client-id]

切換回 Azure Active Directory 組態，並按一下 [ **選取持續時間** 下拉式清單中 **金鑰** 區段，並指定的間隔。 在此範例中 **1 年** 用。

![金鑰][api-management-aad-key-before-save]

按一下 [ **儲存** ，儲存設定，並顯示金鑰。 複製金鑰至剪貼簿。

>記下此金鑰。 關閉 Azure Active Directory 組態視窗之後，即無法再次顯示金鑰。

![金鑰][api-management-aad-key-after-save]

切換回發行者入口網站，並貼上至金鑰 **用戶端密碼** 文字方塊。

![用戶端密碼][api-management-client-secret]

**允許的租用戶** 指定哪些目錄可存取 API 管理服務執行個體的 Api。 指定您要授與存取的 Azure Active Directory 執行個體的網域。 您可以使用換行符號、空格或逗號來分隔多個網域。

![允許的租用戶][api-management-client-allowed-tenants]

可以指定多個網域 **允許的租用戶** 一節。 在使用者可透過與註冊應用程式之原始網域不同的網域登入前，不同網域的全域管理員必須授與權限，應用程式才能存取目錄資料。 若要授與權限，全域管理員必須登入應用程式，然後按一下 [ **接受**。 在下列範例中 `miaoaad.onmicrosoft.com` 已加入至 **允許的租用戶** 並且來自該網域的全域管理員正在第一次登入。

![權限][api-management-permissions-form]

>如果非全域管理員在全域管理員授與其權限之前便嘗試登入，登入嘗試會失敗，並且顯示錯誤畫面。

指定所需的組態之後, 按一下 **儲存**。

![Save][api-management-client-allowed-tenants-save]

一旦儲存變更，在指定的 Azure Active Directory 中的使用者可以登入開發人員入口網站中的步驟 [開發人員入口網站使用 Azure Active Directory 帳戶登入][]。

## 如何加入外部 Azure Active Directory 群組

為 Azure Active Directory 中的使用者啟用存取之後，您可以將 Azure Active Directory 群組加入至 API 管理，以更輕鬆管理所需產品的群組中開發人員的關聯。

> 為了設定外部 Azure Active Directory 群組，必須先遵循上一節中的程序，在 [身分識別] 索引標籤中設定 Azure Active Directory。 

從外部 Azure Active Directory 群組加入 **可視性** ] 索引標籤，您要授與存取權之群組的產品。 按一下 [ **產品**, ，然後按一下所需產品的名稱。

![Configure product][api-management-configure-product]

切換至 **可視性** 索引標籤，然後按一下 [ **從 Azure Active Directory 加入群組**。

![加入群組][api-management-add-groups]

選取 **Azure Active Directory 租用戶** 從下拉式清單，然後再輸入中的所需的群組名稱 **群組** 要加入文字方塊。

![選取群組][api-management-select-group]

此群組名稱可在 **群組** 列出您的 Azure Active Directory，如下列範例所示。

![Azure Active Directory 群組清單][api-management-aad-groups-list]

按一下 [ **新增** 可驗證群組名稱並加入群組。 在此範例中 **Contoso 5 Developers** 就會加入外部群組。 

![Group added][api-management-aad-group-added]

按一下 [ **儲存** 以儲存新群組選項。

一旦已透過一個產品設定 Azure Active Directory 群組，並使用上核取 **可視性** 的 API 管理服務執行個體中的其他產品] 索引標籤。

若要檢閱並設定其屬性的外部群組之後已加入，按一下群組的名稱 **群組** ] 索引標籤。

![管理群組][api-management-groups]

您可以從這裡編輯 **名稱** 和 **描述** 的群組。

![編輯群組][api-management-edit-group]

來自所設定 Azure Active Directory 的使用者可以登入開發人員入口網站，並透過下一節中的指示，以檢視及訂閱他們看的見的任何群組。

## 如何使用 Azure Active Directory 帳戶登入開發人員入口網站

若要登入開發人員入口網站使用先前的章節中所設定的 Azure Active Directory 帳戶，開啟新瀏覽器視窗 **登入 URL** 從 Active Directory 應用程式組態，然後按一下 **Azure Active Directory**。

![開發人員入口網站][api-management-dev-portal-signin]

輸入您 Azure Active Directory 中的其中一個使用者的認證，然後按一下 **登入**。

![Sign in][api-management-aad-signin]

如果需要其他資訊，可能會出現註冊表單的提示。 完成註冊表單，然後按一下 **註冊**。

![註冊][api-management-complete-registration]

您的使用者現在已登入您的 API 服務執行個體的開發人員入口網站。

![註冊完成][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account



