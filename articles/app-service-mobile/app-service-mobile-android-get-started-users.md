<properties
    pageTitle="在具有行動應用程式的 Android 上新增驗證 | Azure App Service"
    description="了解如何在 Azure App Service 中使用行動應用程式透過眾多識別提供者驗證 Android 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="12/06/2015"
    ms.author="glenga"/>

# 將驗證加入 Android 應用程式中

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 摘要

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入 Android 上的 TodoList 快速入門專案。 本教學課程根據 [Get started with Mobile Apps] 教學課程，您必須先完成。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ 在 Android Studio 中，開啟您完成教學課程時所建立的專案 [Get started with Mobile Apps], ，然後從 **執行** 功能表上，按一下 **執行應用程式** ，並確認應用程式啟動後，會引發未處理的例外狀況，狀態碼 401 （未經授權）。

     這是因為應用程式嘗試存取後端以未驗證的使用者，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動應用程式後端的資源之前必須驗證使用者。

## 將驗證新增至應用程式

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端快取驗證權杖

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##後續步驟

現在您已經完成了這個基本驗證的教學課程，可以考慮繼續進行下列其中一個教學課程：

+ [將推播通知新增至 Android 應用程式](app-service-mobile-android-get-started-push.md)   
  了解如何新增推播通知支援新增至您的應用程式，並設定行動應用程式後端使用 Azure 通知中心傳送推播通知。

+ [啟用 Android 應用程式的離線同步處理](app-service-mobile-android-get-started-offline-data.md)  
  了解如何使用行動應用程式後端應用程式中加入離線支援。 離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。 



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Get started with Mobile Apps]: app-service-mobile-android-get-started.md


