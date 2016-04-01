<properties
    pageTitle="如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證"
    description="了解如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證。"
    authors="mattchenderson" 
    services="app-service\mobile"
    documentationCenter=""
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="mahender"/>

# 如何設定 App Service 應用程式以使用 Microsoft 帳戶登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Microsoft 帳戶作為驗證提供者。


> [AZURE.NOTE]
本主題示範 App Service 驗證/授權功能的用法。 這會取代大部分應用程式的 App Service 閘道器。 整個主題中使用閘道器所產生的差異都列在注意事項中。


## <a name="register"> </a>使用 Microsoft 帳戶註冊您的應用程式

1. 登入 [Azure portal], ，並瀏覽至您的應用程式。 複製您 **URL**。 您將使用此 URL 設定您的 Microsoft 帳戶應用程式。

2. 瀏覽至 [My Applications] Microsoft 帳戶開發人員中心頁面，並視需要使用您的 Microsoft 帳戶登入。

4. 按一下 [ **建立應用程式**, ，然後輸入 **應用程式名稱** 按一下 **我接受**。

5. 按一下 [ **API 設定**。 選取 **是** 的 **行動或桌面用戶端應用程式**。 在 **重新導向 URL** 欄位中，輸入您的應用程式 **重新導向 URL** 按一下 **儲存**。 您重新導向 URI 是附加路徑，您的應用程式的 URL _/.auth/login/microsoftaccount/callback_。 例如，`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`。 請確實使用 HTTPS 配置。

    ![][0]


    > [AZURE.NOTE]
    如果您使用應用程式服務閘道，而不應用程式服務驗證 / 授權功能重新導向 URL 改為使用閘道的 URL 與 _/signin-microsoft_ 路徑。


6. 按一下 [ **應用程式設定** 並記下的值 **用戶端識別碼** 和 **用戶端密碼**。


    > [AZURE.NOTE] 用戶端密碼是重要的安全性認證。 請勿與任何人共用此用戶端密碼，或在用戶端應用程式中加以散發。
    

## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至應用程式

> [AZURE.NOTE]
如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。 選取 **設定**, ，**識別**, ，然後 **Microsoft 帳戶**。 貼入您稍早取得的值，然後按一下 [ **儲存**。


7. 回到 [Azure portal], ，瀏覽至您的應用程式。 按一下 [ **設定**, ，然後 **驗證 / 授權**。

8. 如果驗證 / 授權功能未啟用，切換到 **上**。

9. 按一下 [ **Microsoft 帳戶**。 貼上先前取得的應用程式識別碼與應用程式密碼值，然後選擇性啟用應用程式需要的任何範圍。 然後按一下 [ **確定**。

    ![][1]
    
    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。 

17. （選擇性）若要限制存取您的站台的 Microsoft 帳戶來驗證的使用者，請設定 **不會驗證要求時要採取的動作** 至 **Microsoft 帳戶**。 這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Microsoft 帳戶以進行驗證。

11. 按一下 [ **儲存**。 


現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/


