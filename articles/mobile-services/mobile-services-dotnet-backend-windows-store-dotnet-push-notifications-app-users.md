<properties
    pageTitle="傳送推播通知給已驗證的使用者 (Universal Windows 8.1) | Azure Mobile Services"
    description="了解如何使用 Azure 行動服務，將推播通知傳送給執行 Universal Windows 8.1 應用程式並已驗證的特定使用者。"
    services="mobile-services,notification-hubs"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="glenga"/>


# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

## 概觀

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。 不同於先前 [推播通知開始使用推播通知]][get started with push notifications] 教學課程，本教學課程會變更您的行動服務要求用戶端才能向通知中心註冊推播通知之前，必須驗證使用者。 此外也會修改註冊，以根據指派的使用者識別碼新增標記。 最後會更新伺服器程式碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。

本教學課程支援 Windows 市集和 Windows Phone 市集應用程式。

## 必要條件

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [開始使用驗證]
將登入需求加入至 TodoList 範例應用程式。

+ [開始使用推播通知]
使用通知中心設定推播通知的 TodoList 範例應用程式。

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

## <a name="register"></a>更新服務以要求註冊的驗證

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>更新應用程式，以在註冊前先登入

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

## <a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]






[updating the service to require authentication for registration]: #register 
[updating the app to log in before registration]: #update-app 
[testing the app]: #test 
[next steps]: #next-steps 
[get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md 
[get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md 

