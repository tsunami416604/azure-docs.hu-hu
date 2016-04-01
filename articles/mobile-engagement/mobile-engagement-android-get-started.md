<properties
    pageTitle="開始使用 Azure Mobile Engagement"
    description="了解如何使用 Android 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="12/02/2015"
    ms.author="piyushjo" />

# 開始使用適用於 Android 應用程式的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及如何傳送推播通知給 Android 應用程式的分段使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 在此案例中，您先建立空白的 Android 應用程式，使用 Google 雲端通訊 (GCM) 來收集基本資料並接收推播通知。

本教學課程需要下列各項：

+ Android SDK （假設您將使用 Android Studio），您可以下載 [這裡](http://go.microsoft.com/fwlink/?LinkId=389797)
+ 的 [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT] 完成本教學課程是 Android 應用程式，所有其他 Mobile Engagement 教學課程的先決條件，若要完成此作業，您必須具有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>為您的 Android 應用程式設定 Mobile Engagement

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 完整的整合文件位於 [Mobile Engagement Android SDK 整合](../mobile-engagement-android-sdk-overview/)

我們將使用 Android Studio 建立一個基本應用程式來示範整合。

###建立新的 Android 專案

1. 啟動 **Android Studio**, ，並在快顯視窗中，選取 **開始新的 Android Studio 專案**。

    ![][1]

2. 提供 App 名稱與公司網域。 記下您填入的內容，因為您將會在稍後用到。 按一下 [ **下一步**。

    ![][2]

3. 選取目標尺寸和 API 層級，然後按一下 [ **下一步**。

    >[AZURE.NOTE] Mobile Engagement 需要 API 層級 10 的最小 (Android 2.3.3)。

    ![][3]

4. 選取 **空白活動** ，做為此應用程式，然後按一下 [只有畫面這裡 **下一步**。

    ![][4]

5. 最後，保留預設值，並按一下 **完成**。

    ![][5]

Android Studio 現在將建立要用於整合 Mobile Engagement 的示範應用程式。

###在您的專案中包含 SDK 程式庫

下載及整合 SDK 程式庫

1. 下載 [Mobile Engagement Android SDK]。
2. 將封存檔案解壓縮至電腦中的資料夾。
3. 找出此 SDK 目前版本的 .jar 程式庫，並將它複製到剪貼簿。

      ![][6]

4. 瀏覽至 **專案** 區段 （1） 和.jar 貼到 libs 資料夾 (2)。

      ![][7]

5. 同步專案以載入程式庫。

      ![][8]

###使用「連線字串」將您的應用程式連線到 Mobile Engagement 後端

1. 將下列程式碼行複製到活動建立中 (必須只在應用程式中的一個位置完成，通常是主要活動)。 針對此範例 App，開啟 src -> main -> java 資料夾下方的 MainActivity，然後新增下列內容：

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. 按 Alt + Enter 鍵或新增下列匯入陳述式來解析參考：

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. 回到 Azure 傳統入口網站應用程式中 **連線資訊** 頁面並複製 **連接字串**。

      ![][9]

4. 將此字串貼到 `setConnectionString` 參數以取代所提供的範例，如下所示：

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###新增權限和服務宣告

1. 將這些權限加入至您專案的 Manifest.xml 中，緊接在 `<application>` 標記之前或之後：

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. 在 `<application>` 和 `</application>` 標記之間加入下列內容，宣告代理程式服務：

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. 在您剛貼上的程式碼中，更換標籤中的 `"<Your application name>"`。 這會顯示在 **設定** ] 功能表上，使用者可以查看裝置上執行的服務的位置。 例如，您可以在該標籤中加入「服務」這個字。

###傳送畫面到 Mobile Engagement

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

移至 **MainActivity.java** 並加入下列內容來取代的基底類別 **MainActivity** 至 **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

您應該針對此簡單範例案例註解掉 (排除) 下列一行：

    // setSupportActionBar(toolbar);

如果您想要保留這周圍，則您應該查看 < 基本報告 > 案例中我們 [Advanced Android Integration]

##<a id="monitor"></a>將應用程式與即時監視連接

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與 App 內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和「觸達」。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的應用程式來接收它們。

### 啟用應用程式內傳訊

1. 將下列應用程式內傳訊資源複製到您 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

2. 透過下列步驟，將資源複製到您的專案：
    1. 瀏覽回到您的 SDK 下載內容，並且複製 'res' 資料夾。

         ![][13]

    2. 返回 Android Studio，選取專案檔案的 'main' 目錄，然後貼上以將資源加入您的專案。

         ![][14]

###指定通知的圖示

請將下列 XML 程式碼片段貼到您 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

這會定義顯示於系統和 App 內通知中的圖示。 這是 App 內通知的選用功能，但對系統通知是必要功能。 Android 將會拒絕具有無效圖示的系統通知。

請確定您使用存在於其中一個圖示 **可繪製** 資料夾 (例如 ``engagement_close.png``)。 **mipmap** 資料夾不支援。

>[AZURE.NOTE] 您不應該使用 **啟動器** 圖示。 它的解析度不同，而且通常是在 mipmap 資料夾中，我們不支援該資料夾。

為真實的應用程式，您可以使用適用於每個通知圖示 [Android 設計指導方針](http://developer.android.com/design/patterns/notifications.html)。

>[AZURE.TIP] 為了確保使用正確的圖示解析度，您可以查看 [這些範例](https://www.google.com/design/icons)。
向下捲動至 **通知** 區段、 按一下圖示，然後按一下 `PNGS` 下載圖示可繪製集合。 您可看到對於每個版本的圖示要使用哪種解析度的可繪製資料夾。

##利用 API 金鑰建立 Google 雲端通訊專案

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###啟用應用程式接收 GCM 推播通知

1. 請先用得自 Google Play 控制台的 `project number` 取代下列內容的星號之後，再把下列內容貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。 請務必在專案編號後面加上 \n。

        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 將下列程式碼貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。 取代封裝名稱 <Your package name>.

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>

3. 將反白顯示的最後一組權限集新增到 `<application>` 標記之前。 請使用應用程式的實際封裝名稱來取代 `<Your package name>`。

        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###授與 Mobile Engagement 的存取權給 GCM API 金鑰

若要讓 Mobile Engagement 以您的名義傳送推播通知，您必須授與 API 金鑰的存取權。 完成此項作業的方法為在 Mobile Engagement 入口網站中設定並輸入您的金鑰。

1. 瀏覽至您的 Mobile Engagement 入口網站

    在 Azure 傳統入口網站，確定您已在我們的應用程式使用此專案，然後按一下 [ **Engage** 底部的按鈕 ︰

    ![][15]

2. 然後按一下 [ **設定** ]-> [ **原生推送** 區段以輸入 GCM 金鑰 ︰

    ![][16]

3. 按一下 [ **編輯** 前面圖示 **API 金鑰** 中 **GCM 設定** 區段如下所示 ︰

    ![][17]

4. 在快顯視窗中，貼上您之前取得的 GCM 伺服器金鑰，然後按一下 [ **確定**。

    ![][18]

##<a id="send"></a>傳送通知至應用程式

我們將建立一個簡單的推播通知活動，它會將推播通知傳送至我們的應用程式。

1. 瀏覽至 **到達** 中您的 Mobile Engagement 入口網站] 索引標籤。

2. 按一下 [ **新公告** 建立推送通知活動。

    ![][20]

3. 透過下列步驟來設定活動的第一個欄位：

    ![][21]

    a. 為您的活動命名。

    b. 選取 **傳遞類型** 為 *系統通知]-> [簡單*︰ 這是 Android 簡易推播通知類型，具備一個標題和一小行文字。

    c. 選取 **傳遞時間** 為 *隨時* 以允許應用程式以應用程式啟動或不能接收通知。

    d. 在通知文字類型 **標題** ，將會在發送以粗體顯示。

    e. 然後輸入您 **訊息**

4. 向下的捲動，在 **內容** 區段中，選取 **僅通知**。

    ![][22]

5. 您已完成能做的最基本活動設定。 現在向下捲動並按一下 **建立** ] 按鈕以儲存您的活動。

6. 最後一個步驟 ︰ 按一下 **啟動** 啟動您的活動以傳送推播通知。

    ![][24]

<!-- URLs. -->
[Mobile Engagement Android SDK]: https://aka.ms/vq9mfn
[Mobile Engagement Android SDK documentation]: https://aka.ms/tujlkm
[Advanced Android Integration]: https://azure.microsoft.com/en-us/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png


