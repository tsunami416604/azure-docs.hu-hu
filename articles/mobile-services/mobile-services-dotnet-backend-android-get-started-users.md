<properties
    pageTitle="開始使用驗證 (Android) | Microsoft Azure"
    description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="ricksal"/>


# 將驗證加入至行動服務 Android 應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## 摘要

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。 在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。 由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：


## 必要條件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>限制已驗證的使用者權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

3. 開啟您完成教學課程時所建立的專案 [開始使用行動服務]。

4. 在 [執行] ****功能表中，按一下 [執行應用程式] ****來啟動應用程式。確認應用程式啟動後是否會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

     這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端上快取驗證權杖

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>重新整理權杖快取

[AZURE.INCLUDE [mobile-android-authenticate-app-refresh-token](../../includes/mobile-android-authenticate-app-refresh-token.md)]

## <a name="next-steps"></a>後續步驟

在下一個教學課程中， [[授權使用者使用指令碼] 的行動服務使用者的服務端授權][authorize users with scripts], ，您將行動服務根據經驗證的使用者所提供的使用者識別碼值，並用來篩選行動服務所傳回的資料。





[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[store authentication tokens on the client]: #cache-tokens 
[refresh expired tokens]: #refresh-tokens 
[next steps]: #next-steps 
[get started with mobile services]: mobile-services-dotnet-backend-android-get-started.md 
[get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md 
[get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md 
[authorize users with scripts]: ../mobile-services-dotnet-backend-android-authorize-users-in-scripts.md 
[mobile services .net how-to conceptual reference]: /develop/mobile/how-to-guides/work-with-net-client-library 
[register your windows store app package for microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md 

