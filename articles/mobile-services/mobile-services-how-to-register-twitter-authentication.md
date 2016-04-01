<properties
    pageTitle="註冊 Twitter 驗證 | Microsoft Azure"
    description="了解如何對於˙ Azure Mobile Services 應用程式使用 Twitter 驗證。"
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
    ms.date="11/30/2015" 
    ms.author="glenga"/>

#在行動服務中註冊您的應用程式以進行 Twitter 登入

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Twitter 驗證。

>[AZURE.NOTE] 本教學課程是有關 [Azure 行動服務](http://azure.microsoft.com/services/mobile-services/), ，此方案可協助您建置用於任何平台的可擴充行動應用程式。 行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。 此頁面支援 [將驗證新增至您的應用程式](mobile-services-ios-get-started-users.md) 教學課程，說明如何將使用者登入您的應用程式。 如果這是您第一次使用行動服務，請完成[開始使用行動服務](mobile-services-ios-get-started.md)教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。 若要建立新的 Twitter 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

1. 瀏覽至 [Twitter 開發人員](http://go.microsoft.com/fwlink/p/?LinkId=268300) 網站，使用您的 Twitter 帳戶認證登入並按一下 [ **建立新的應用程式**。

2. 型別 **名稱**, ，**描述**, ，和 **網站** 您的應用程式，然後輸入其中一個下列 URL 格式中的值 **回呼 URL**。

    + **.NET 後端**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
    + **JavaScript 後端**: `https://<mobile_service>.azure-mobile.net/login/twitter`

     >[AZURE.NOTE]請確定您使用正確的重新導向 URL 路徑格式，您的行動服務後端類型。 若這不正確，驗證也將不會成功。
    &nbsp;

    ![][2]

3.  在頁面底部，閱讀並接受條款，然後按一下 **建立 Twitter 應用程式**。

    這會註冊應用程式並顯示應用程式詳細資料。

6. 按一下 [ **金鑰和存取權杖** 應用程式儀表板] 索引標籤並記下的值 **消費者金鑰** 和 **消費者密鑰**。

    > [AZURE.NOTE] 消費者密碼是重要的安全性認證。 請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

7. 按一下 [ **設定** ] 索引標籤上，向下捲動並確定 **允許此應用程式用來以 Twitter 登入** ] 核取方塊，然後按一下 [ **更新設定**。

現在您已準備好提供消費者金鑰和消費者密碼值給行動服務，以在您的應用程式中採用 Twitter 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/


