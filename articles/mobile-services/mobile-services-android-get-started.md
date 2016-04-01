<properties
    pageTitle="開始使用適用於 Android 應用程式的 Azure 行動服務 (JavaScript 後端)"
    description="遵循此教學課程，開始使用 Azure 行動服務進行 Android 開發 (JavaScript 後端)。"
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
    ms.topic="hero-article"
    ms.date="11/05/2015"
    ms.author="ricksal"/>

# 開始使用行動服務 android （JavaScript 後端）

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單**待辦事項清單**應用程式。

> [AZURE.VIDEO mobile-get-started-android]

以下是完成應用程式的螢幕擷取畫面：

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## 必要條件

完成本教學課程，您需要 [Android Developer Tools](https://developer.android.com/sdk/index.html))，其中包括 Android Studio 整合式開發環境，以及最新版 Android 平台。 需要 Android 4.2 或以上的版本。

下載的快速入門專案包含 Azure Mobile Services SDK for Android。

> [AZURE.IMPORTANT] 若要完成本教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)。


## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Android 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在 Azure 傳統入口網站中，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [ **Android** 下 **選擇平台** 展開 **建立新的 Android 應用程式**。

    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

    此處顯示建立與行動服務連線的 Android 應用程式的 3 個簡單步驟。

    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. 如果您尚未這樣做，請下載並安裝 [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125) 在您本機電腦或虛擬機器。

4. 按一下 [ **Create TodoItem table** 來建立儲存應用程式資料的資料表。


5. 立即下載您的應用程式，按下 **下載** ] 按鈕。

## 執行您的 Android 應用程式

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [Get started with data]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [Get started with authentication]
  <br/>了解如何透過身分識別提供者來驗證您的應用程式使用者。

* [Get started with push notifications]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-ec.md
[Get started with data]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533



