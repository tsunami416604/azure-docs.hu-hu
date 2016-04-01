<properties
    pageTitle="如何為您的應用程式服務應用程式設定 Facebook 驗證"
    description="了解如何為您的應用程式服務應用程式設定 Facebook 驗證。"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson" 
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

# 如何設定 App Service 應用程式以使用 Facebook 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請移至 [facebook.com]。

> [AZURE.NOTE]
本主題示範 App Service 驗證/授權功能的用法。 這會取代大部分應用程式的 App Service 閘道器。 整個主題中使用閘道器所產生的差異都列在注意事項中。


## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 登入 [Azure portal], ，並瀏覽至您的應用程式。 複製您 **URL**。 您將使用此 URL 設定您的 Facebook 應用程式。
 
2. 在另一個瀏覽器視窗中，瀏覽至 [Facebook Developers] 網站，並使用您的 Facebook 登入帳戶的認證。

3. （選擇性）如果您尚未註冊您，按一下 [ **應用程式** > **身為開發人員註冊**, ，然後接受政策並遵循註冊步驟。

4. 按一下 [ **我的應用程式** > **加入新的應用程式** > **網站** 輸入您的應用程式的唯一名稱，然後按一下 [ **建立新的 Facebook 應用程式識別碼**。

6. 選取您的應用程式，從下拉式清單中，類別目錄，然後按一下 [ **建立應用程式識別碼** 在下一個頁面上，按一下 [ **略過快速入門**。 這會將您帶到應用程式的開發人員儀表板。

8. 在 **應用程式密鑰** 欄位中，按一下 **顯示**, ，提供您的密碼有要求，然後記下的值 **應用程式識別碼** 和 **應用程式密鑰**。 稍後您會設定您的應用程式以使用這些。

    > [AZURE.NOTE] **安全性注意事項**
    應用程式密鑰是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

9. 按一下左側的導覽列上 **設定**, ，型別 **URL** 行動應用程式中的 **應用程式定義域**, ，並輸入 **連絡人電子郵件**。 

    ![][0]

10. 如果您沒有看到下方的網站區段，按一下 [ **新增平台** > **網站**, ，輸入 **URL** 行動應用程式中的 **網站 URL** 欄位，然後按一下 [ **儲存變更**。

11. 按一下 [ **進階** 索引標籤上，新增您的應用程式 **重新導向 URI** 至 **有效的 OAuth 重新導向 Uri**, ，然後按一下 [ **儲存變更**。 您重新導向 URI 是附加路徑，您的應用程式的 URL _/.auth/login/facebook/callback_。 例如，`https://contoso.azurewebsites.net/.auth/login/facebook/callback`。 請確實使用 HTTPS 配置。


    > [AZURE.NOTE]
    如果您使用應用程式服務閘道，而不應用程式服務驗證 / 授權功能重新導向 URL 改為使用閘道的 URL 與 _/signin-facebook_ 路徑。


12. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。 此時，只有系統管理員可以登入此應用程式。 若要驗證其他 Facebook 帳戶，請按一下 [ **狀態和檢閱** 左側的導覽列中。 然後按一下 [ **是** 啟用一般公用存取權。


## <a name="secrets"> </a>將 Facebook 資訊加入應用程式

> [AZURE.NOTE]
如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。 選取 **設定**, ，**識別**, ，然後 **Facebook**。 貼入您稍早取得的值，然後按一下 [ **儲存**。


13. 回到 [Azure portal], ，瀏覽至您的應用程式。 按一下 [ **設定** > **驗證/授權**, ，並確定 **應用程式服務驗證** 是 **上**。

15. 按一下 [ **Facebook**, 、 貼上您先前取得的應用程式識別碼和應用程式密碼值，選擇性地啟用應用程式所需的任何範圍然後按一下 [ **確定**。

    ![][1]
    
    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。 

17. （選擇性）若要限制存取您的站台由 Facebook 驗證的使用者，設定 **不會驗證要求時要採取的動作** 至 **Facebook**。 這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Facebook 以進行驗證。

17. 按一下 [ **儲存**。 

現在，您已可在應用程式中使用 Facebook 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/

