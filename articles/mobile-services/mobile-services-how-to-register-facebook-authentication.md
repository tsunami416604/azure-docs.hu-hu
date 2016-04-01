<properties
    pageTitle="註冊 Facebook 驗證 | Azure 行動服務"
    description="了解如何在 Azure 行動服務應用程式中使用 Facebook 驗證。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/15/2015"
    ms.author="glenga"/>

# 在行動服務中註冊應用程式以採用 Facebook 驗證

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Facebook 驗證。

>[AZURE.NOTE] 本教學課程是有關 [Azure Mobile Services], ，此方案可協助您建置用於任何平台的可擴充行動應用程式。 行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。 此頁面是[開始使用驗證](mobile-services-ios-get-started-users.md)教學課程的輔助，說明如何將使用者登入您的應用程式。 如果這是您第一次使用行動服務，請完成[開始使用行動服務](mobile-services-ios-get-started.md)教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請前往 [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)。

1. 導覽至 [Facebook 開發人員](http://go.microsoft.com/fwlink/p/?LinkId=268285)網站，並以您的 Facebook 帳戶認證登入。

2. （選擇性）如果您尚未註冊您，按一下 [ **我的應用程式** 然後按一下 [ **身為開發人員註冊**, ，接受政策並遵循註冊步驟。

3. 按一下 [ **我的應用程式** > **加入新的應用程式** > **進階安裝**。

4. 輸入一個唯一 **顯示名稱** 應用程式，然後選擇 [ **Apps for Pages** 下 **類別**, ，然後按一下 [ **建立應用程式識別碼** 並完成安全性練習。

    這會建立新的 Facebook 應用程式識別碼。

5. 按一下 [ **設定**, ，輸入您的行動服務中的網域 **應用程式定義域**, ，選擇性地輸入 **連絡人電子郵件**, ，按一下 [ **新增平台** ，然後選取 **網站**。

    ![][3]

6. 輸入您的行動服務的 URL **網站 URL**, ，然後按一下 [ **儲存變更**。

7. 按一下 [ **顯示**, ，提供您的密碼有要求，然後記下的值 **應用程式識別碼** 和 **應用程式密鑰**。

    ![][5]
    &nbsp;

    >[AZURE.IMPORTANT] 應用程式密鑰是重要的安全性認證。 請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。
    &nbsp;

8. 按一下 [ **進階** ] 索引標籤中，輸入下列 URL 的其中一個格式中 **有效的 OAuth 重新導向 Uri**, ，然後按一下 [ **儲存變更**:

    + **.NET 後端**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
    + **JavaScript 後端**: `https://<mobile_service>.azure-mobile.net/login/facebook`

     >[AZURE.NOTE]請確定您使用正確的重新導向 URL 路徑格式，您的行動服務後端類型。 若這不正確，驗證也將不會成功。


9. 按一下 [ **狀態和檢閱** > **是** 以啟用您的應用程式的一般公用存取權。

    您用來註冊新應用程式的 Facebook 帳戶是應用程式的管理員，具有該應用程式的管理員存取權。 此步驟會授與一般公用存取權，讓應用程式能夠使用其他 Facebook 帳戶進行驗證。


現在您已準備好提供應用程式 ID 和應用程式密碼值給行動服務，以在您的應用程式中採用 Facebook 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/


