<properties
    pageTitle="將推播通知新增至 Xamarin Android 應用程式 | Microsoft Azure"
    description="了解如何使用 Google Cloud Messaging，為使用 Azure 行動服務和 Azure 通知中樞的 Xamarin.Android 應用程式設定推播通知。"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="dwrede"
    services="mobile-services"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀
本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.Android 應用程式。 在本教學課程中，您新增推播通知使用 Google 雲端通訊 (GCM) 服務，以開始使用行動服務] 專案。 完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程需要下列各項：

+ 有效的 Google 帳戶。
+ [Google Cloud Messaging Client Component]. 您在教學課程中會新增此元件。

您應該已經有 [Xamarin.Android] 和 [Azure Mobile Services Component] 安裝在您的專案，當您完成 [開始使用行動服務]。

##<a id="register"></a>啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>更新註冊的插入指令碼以傳送通知

>[AZURE.TIP] 下列步驟顯示如何更新 Azure 傳統入口網站的 TodoItem 資料表的插入作業註冊的指令碼。 您也可以直接在 Visual Studio 之伺服器總管的 Azure 節點中，直接存取和編輯此行動服務指令碼。

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>設定用於推播通知的現有專案

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>將推播通知程式碼新增至應用程式

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

您已成功完成此教學課程。

## <a name="next-steps"></a>後續步驟

在下列主題中深入了解行動服務和通知中心：

* [開始使用驗證](mobile-services-android-get-started-users.md)
  <br/>了解如何使用不同帳戶類型，使用行動服務驗證您的應用程式的使用者。

* [什麼是通知中心?](../notification-hubs-overview.md)
  <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得指引疑難排解和偵錯通知中樞解決方案。

* [如何使用行動服務.NET 用戶端程式庫](mobile-services-windows-dotnet-how-to-use-client-library.md)
  <br/>進一步了解如何使用行動服務與 Xamarin C# 程式碼。

* [行動服務伺服器指令碼參考](mobile-services-how-to-use-server-scripts.md)
  <br/>進一步了解如何在您的行動服務中實作商務邏輯。

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

