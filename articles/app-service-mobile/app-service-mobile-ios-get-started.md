<properties
    pageTitle="在 App Service Mobile Apps 上建立 iOS 應用程式 | Microsoft Azure"
    description="請遵循此教學課程開始使用 Azure 行動應用程式後端，用於 Objective-C 或 Swift 的 iOS 開發。"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="11/30/2015"
    ms.author="krisragh"/>


# 建立 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本教學課程會示範如何使用雲端型後端服務 ([Azure Mobile Apps](app-service-mobile-value-prop.md)) iOS 行動應用程式。 您將建立新的行動後端，並使用儲存在 Azure 中的應用程式資料的簡單 _Todo list_ iOS 應用程式。

## 必要條件

若要完成此教學課程，您需要下列項目：

* [有效的 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)

* 使用 [Visual Studio Community 2013] 的電腦或更新版本

* 具有 Xcode v7.0 或更新版本的 Mac 電腦

* [Azure 行動 iOS 架構](https://go.microsoft.com/fwLink/?LinkID=529823), ，它會自動包含在您下載的快速入門專案

## 建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## 下載伺服器專案

1. 在您的電腦，請造訪 [Azure 入口網站]。 按一下 [全部瀏覽]**** > [Mobile Apps]****，然後按一下您剛建立的行動 app 後端。

2. 在 [行動應用程式] 刀鋒視窗中，按一下 [設定]****，然後在 [行動應用程式]**** 底下按一下 [快速入門]**** > [iOS (Objective-C)]****。 如果您偏好使用 Swift，請改為按一下 [快速入門]**** > [iOS (Swift)]****。

## 設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## 下載並執行 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]






[azure portal]: https://portal.azure.com/ 
[xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[visual studio community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203 

