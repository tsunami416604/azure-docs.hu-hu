<properties
    pageTitle="將驗證加入現有的 Azure 行動服務應用程式 (iOS) | JavaScript 後端 | Microsoft Azure"
    description="了解如何使用行動服務透過眾多識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
    services="mobile-services"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="krisragh"/>


# 將驗證加入至現有的應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

在本教學課程中，您將驗證新增至 [行動服務快速入門教學課程] 使用支援的身分識別提供者。

我們建議您先完成 [行動服務快速入門教學課程]。 或者，直接從 [Azure 傳統入口網站] 按一下 [下載快速入門的 iOS 專案  **行動電話服務** > 您的行動服務 > 左上方定域機組登 > **iOS** > **建立新的 iOS 應用程式** > **下載並執行應用程式** > **OBJECTIVE-C** > **下載**。 在您按一下 [下載]**** 之前，請務必按一下 [建立 TodoItem 資料表]**** (如果您尚未建立資料表的話)。

## <a name="register"></a>註冊應用程式以進行驗證

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>限制已驗證的使用者資料的權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

## <a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

## <a name="store-authentication"></a>將驗證權杖儲存在應用程式

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>後續步驟

接下來，了解 [如何使用使用者識別碼值篩選傳回的資料](mobile-services-javascript-backend-service-side-authorization.md)。









[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[next steps]: #next-steps 
[storing authentication tokens in your app]: #store-authentication 
[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png 
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png 
[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png 
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png 
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png 
[service-side authorization of mobile services users]: mobile-services-javascript-backend-service-side-authorization.md 
[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[live sdk for windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[single sign-on for windows store apps by using live connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet 
[mobile services quick start tutorial]: /develop/mobile/tutorials/get-started-ios 
[get started with data]: /develop/mobile/tutorials/get-started-with-data-ios 
[get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios 
[get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios 
[authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios 
[azure classic portal]: https://manage.windowsazure.com/ 

