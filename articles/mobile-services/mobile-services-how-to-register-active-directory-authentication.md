<properties
    pageTitle="註冊 Azure Active Directory 驗證 | Microsoft Azure"
    description="了解如何在 Mobile Services 應用程式中註冊 Azure Active Directory 驗證。"
    authors="wesmc7777"
    services="mobile-services"
    documentationCenter=""
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/15/2015"
    ms.author="ricksal"/>

# 註冊應用程式以使用 Azure Active Directory 帳戶登入

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

##概觀

本主題說明如何註冊應用程式，以使用 Azure Active Directory 作為行動服務的驗證提供者。

##註冊您的應用程式

>[AZURE.NOTE] 本主題中所述的步驟會用來搭配 [將驗證新增至行動服務應用程式](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md) 教學課程，當您想要使用 [服務主導登入作業](http://msdn.microsoft.com/library/azure/dn283952.aspx) 與您的應用程式。 或者，如果您的應用程式的需求 [用戶端主導登入作業](http://msdn.microsoft.com/library/azure/jj710106.aspx) Azure Active Directory 及.NET 後端行動服務應該開始 [驗證您的應用程式使用 Active Directory 驗證程式庫單一登入](mobile-services-windows-store-dotnet-adal-sso-authentication.md) 教學課程。

1. 登入 [Azure 傳統入口網站]，瀏覽至您的行動服務，按一下 **識別** 索引標籤，然後向下捲動至 **Azure active directory** 身分識別提供者區段，然後複製 **應用程式 URL** 顯示在這裡。

    ![AAD 的行動服務應用程式 URL](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png)

2. 瀏覽至 **Active Directory** [Azure 傳統入口網站]，按一下您的目錄然後 **網域** 並記下目錄的預設網域。

3. 按一下 [ **應用程式** > **新增** > **加入我的組織正在開發的應用程式**。

4. 在 [新增應用程式精靈] 中，輸入 **名稱** 您的應用程式，然後按一下 [  **Web 應用程式和/或 Web API** 型別。

    ![為 AAD 應用程式命名](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png)

5. 在 **登入 URL** 方塊中，貼上您從您的行動服務中複製的應用程式 URL 值。 輸入在相同的唯一值 **應用程式識別碼 URI** ] 方塊中，然後按一下以繼續。

    ![設定 AAD 應用程式屬性](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png)

6. 新增應用程式之後，按一下 [ **設定** ] 索引標籤並複製 **用戶端識別碼** 應用程式。

    >[AZURE.NOTE]對於.NET 後端行動服務，則必須同時編輯 **回覆 URL** 值下 **單一登入** 加上路徑，您的行動服務 url _登入 aad_。 例如，  `https://todolist.azure-mobile.net/signin-aad`

7. 返回您的行動服務 **識別** 索引標籤，然後貼上複製 **用戶端識別碼** azure active directory 身分識別提供者的值。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png)

8.  在 **允許的租用戶** 清單中，輸入您已註冊的應用程式目錄的網域 (例如 `contoso.onmicrosoft.com`)，然後按一下 [ **儲存**。

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Azure classic portal]: https://manage.windowsazure.com/



