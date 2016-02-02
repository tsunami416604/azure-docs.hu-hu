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

## 概觀

本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.Android 應用程式。  完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程需要下列各項：

+ 有效的 Google 帳戶。
+ 您在教學課程中會新增此元件。



## 

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## 

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## 

>[AZURE.TIP] 下列步驟示範如何在 Azure 傳統入口網站中對 TodoItem 資料表上的插入作業更新註冊的指令碼。 您也可以直接在 Visual Studio 之伺服器總管的 Azure 節點中，直接存取和編輯此行動服務指令碼。

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## 

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## 

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

## 

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

您已成功完成此教學課程。

## 

在下列主題中深入了解行動服務和通知中心：

* 


* 


* 


* 


* 




[get started with mobile services]: mobile-services-ios-get-started.md 
[google cloud messaging client component]: http://components.xamarin.com/view/GCMClient/ 
[xamarin.android]: http://xamarin.com/download/ 
[azure mobile services component]: http://components.xamarin.com/view/azure-mobile-services/ 

