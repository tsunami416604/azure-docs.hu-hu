<properties 
    pageTitle="Azure Mobile Engagement Android SDK 整合" 
    description="Android SDK for Azure Mobile Engagement 的最新更新和程序"
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
    ms.topic="article" 
    ms.date="08/10/2015" 
    ms.author="piyushjo" />


# 如何在 Android 上整合 Engagement Reach

> [AZURE.IMPORTANT] 您必須遵循＜如何在 Android 上整合＞文件中所述的整合程序，才能接著遵循本指南。

## 標準整合

Reach SDK 需要「Android 支援程式庫 (v4)」。****

最快的方法新增至您的專案中的程式庫 **Eclipse** 是 `右邊，按一下 [專案]-> [Android 工具...]-> [新增支援程式庫`。

如果您未使用 Eclipse，您可以參閱 [這裡] 的指示。

從專案中的 SDK 複製 Reach 資源檔：

-   中的檔案複製 `res/配置` SDK 所附資料夾 `res/配置` 應用程式的資料夾。
-   中的檔案複製 `可繪製 res/` SDK 所附資料夾 `可繪製 res/` 應用程式的資料夾。

編輯您 `AndroidManifest.xml` 檔案:

-   新增下列區段 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

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

-   您需要此權限才能重新執行開機時未按下的系統通知 (否則它們將保留在磁碟上，但不會再顯示，您真的必須加入此區段)。

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   指定用於複製和編輯下列區段 (兩者皆位於應用程式以及系統圖示) 的通知圖示 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />


> [AZURE.IMPORTANT] 如果您打算在建立 Reach 活動時使用系統通知，則「務必」使用此區段。**** Android 禁止顯示沒有圖示的系統通知。 因此如果您省略此區段，使用者將無法接收系統通知。

-   如果您建立活動時，系統通知使用大圖片，您需要新增下列權限 (之後 `< / 應用程式 >` 標籤) 如果遺失:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   在 Android M，而如果您的應用程式的目標 Android API 層級 23 或更大， `WRITE_EXTERNAL_STORAGE` 權限需要使用者核准。 請閱讀 [本節](mobile-engagement-android-integrate-engagement.md#android-m-permissions)。

-   針對系統通知，如果裝置應該響鈴及/或震動，您也可以在 Reach 活動中指定。其正常運作，您必須確定已宣告下列權限 (之後 `< / 應用程式 >` 標記):

            <uses-permission android:name="android.permission.VIBRATE" />

    若無此權限，如果您在 Reach 活動管理員中核取了響鈴或震動的選項，Android 會禁止顯示系統通知。

-   如果您建置應用程式使用 **ProGuard** 且發生與 Android 支援程式庫或 Engagement jar 相關的錯誤，新增下列幾行，您 `proguard.cfg` 檔案:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }


## 原生推送

現在，您會設定 Reach 模組，您需要設定原生推播以便在裝置上接收行銷活動。

我們在 Android 上支援兩種服務：

  - Google Play 裝置: 遵循 [Google Cloud Messaging] 使用 [如何整合 GCM 與 Engagement 指南](mobile-engagement-android-gcm-integrate.md) 指南。
  - Amazon 裝置: 遵循 [Amazon 裝置訊息] 使用 [如何整合 ADM 與 Engagement 指南](mobile-engagement-android-adm-integrate.md) 指南。

如果您的目標想要同時鎖定 Amazon 和 Google Play 裝置，可以將所有東西放在一個 AndroidManifest.xml/APK 進行開發。 但提交給 Amazon 時，如果他們發現 GCM 程式碼可能會拒絕您的應用程式。

您應該在此情況下使用多個 APK。

**現在您的應用程式已準備好接收及顯示 Reach 活動！**

## 如何處理資料推送

### 整合

如果您想要能夠接收 Reach 資料推送的應用程式，您必須建立的子類別 `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` 參考它在 `AndroidManifest.xml` 檔案 (之間 `< 應用程式 >` 及/或 `< / 應用程式 >` 標記):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

然後您可以覆寫 `onDataPushStringReceived` 和 `onDataPushBase64Received` 回呼。 下列是一個範例：

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }
    
              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### 類別

當您建立「資料推送」活動時，類別參數是選用的，且可讓您篩選資料推送。 如果您有數個廣播的接收器在處理不同類型的資料推送，或如果您想要推送不同種類的 `Base64` 資料且想来剖析之前識別其類型。

### 回呼的傳回參數

以下是一些可正確處理傳回的參數指示 `onDataPushStringReceived` 和 `onDataPushBase64Received`:

-   廣播的接收器應會傳回 `null` 在回呼中，如果不知道如何處理資料推送。 您應該使用類別目錄來判斷廣播接收器是否應處理資料推送。
-   其中一個廣播接收器應會傳回 `true` 在回呼中，如果接受資料推送。
-   其中一個廣播接收器應會傳回 `false` 在回呼中，如果可辨識資料推送，但因故捨棄它。 例如，傳回 `false` 接收的資料無效。
-   如果一個廣播接收器傳回 `true` 而另一個會傳回 `false` 針對相同的資料推送，行為是未定義，您應該永遠不會這麼做。

傳回類型只用於 Reach 統計資料：

-   `已回覆` 如果其中一個廣播接收器傳回可能就會遞增 `true` 或 `false`。
-   `已採取動作` 其中一個廣播接收器傳回時，才會遞增 `true`。

## 如何自訂活動

若要自訂活動，您可以修改 Reach SDK 中提供的配置。

您應該保留配置中使用的所有識別碼，並保留使用識別碼的檢視類型，尤其是文字檢視和影像檢視。 有些檢視只用來隱藏或顯示區域，所以可能會變更其類型。 如果您想要變更所提供配置中的檢視類型，請檢查原始碼。

### 通知

有兩種類型的通知：系統和應用程式內通知，它們使用不同的配置檔。

#### 系統通知

若要自訂系統通知，您需要使用 [類別]****。 您可以跳到 [類別](#categories)。

#### 應用程式內通知

根據預設，應用程式內通知是以動態方式加入至目前活動使用者介面，採用 Android 方法檢視 `addContentView()`。 這稱為通知重疊。 通知重疊非常適合快速整合，因為它們不需要您修改應用程式中的任何配置。

若要修改通知重疊的外觀，您可以直接修改檔案 `engagement_notification_area.xml` 您的需求。
> [AZURE.NOTE] 檔案 `engagement_notification_overlay.xml` 是用來建立通知重疊，其中也包含檔案 `engagement_notification_area.xml`。 您也可以自訂以配合您的需求 (例如在覆疊內定位通知區域)。

##### 包含通知配置做為活動配置的一部分

覆疊非常適合快速整合，但可能在特殊情況下造成不便或有副作用。 可以在活動層級自訂覆疊系統，以便輕鬆避免特殊活動的副作用。

您可以決定使用 Android **include** 陳述式，將通知配置納入您現有的配置。 以下是修改過的範例 `ListActivity` 配置其中只包含 `ListView`。

**Engagement 整合之前：**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Engagement 整合之後：**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">
    
              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />
    
              <include layout="@layout/engagement_notification_area" />
    
            </LinearLayout>

在此範例中我們加入了父容器，因為原始配置使用清單檢視做為最上層項目。 我們也加入 `android: layout_weight ="1"` 能夠與設定的清單檢視下加入檢視 `android: layout_height ="fill_parent"`。

Engagement Reach SDK 會自動偵測到通知配置已包含在此活動中，且不會加入此活動的覆疊。
> [AZURE.TIP] 如果您在應用程式中使用 ListActivity，可見的 Reach 覆疊會使您無法再對清單檢視中的被按項目做出反應。 這是已知的問題。 若要暫時解決這個問題，我們建議您在自己的清單活動配置中內嵌通知配置，就像在先前範例那樣。

##### 停用關於活動的應用程式通知

如果您不想覆疊加入至您的活動，而且如果您自己的配置中，也未包含通知配置，您可以停用此活動的覆疊 `AndroidManifest.xml` 加 `中繼資料` 區段如同下列範例所示:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> 類別目錄

當您修改提供的配置時，您會修改所有通知的外觀。 類別允許您定義通知的各種目標外觀 (也可能是行為)。 當您建立觸達活動時可以指定類別。 請記住，類別也可讓您自訂宣告與輪詢，本文件稍後將會說明。

若要登記通知的類別處理常式，您需要在應用程式初始化時加入呼叫。
> [AZURE.IMPORTANT] 請閱讀關於 android: process 屬性的警告 \<android-sdk-engagement-process\> Android 之前的主題上整合 Engagement 的作法。

下列範例假設您已知悉先前的警告，並使用的子類別 `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` 物件是通知類別處理常式的實作。 其中一個是實作 `EngagementNotifier` 介面或預設實作的子類別: `EngagementDefaultNotifier`。

請注意，相同通知程式能夠處理數種類別，您可以像這樣登記它們：

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

若要取代預設類別實作，您可以如下列範例中地登記您的實作：

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

目前使用的處理常式的類別做為參數，您可以覆寫中的大部分方法中傳遞 `EngagementDefaultNotifier`。

它會傳遞為 `字串` 參數或是間接在 `EngagementReachContent` 該物件具有 `getCategory()` 方法。

您可以變更大部分的通知建立程序在重新方法定義 `EngagementDefaultNotifier`, ，如需更進一步自訂歡迎查看技術文件和原始程式碼。

##### 應用程式內通知

如果您只想要針對特定類別使用替代配置，可以如下列範例所示實作：

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }
    
              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }
    
    
              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }
    
              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

* * 的範例 `my_notification_overlay.xml` : * *

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">
    
              <include layout="@layout/my_notification_area" />
    
            </RelativeLayout>

如您所見，覆疊檢視識別碼與標準不同。 很重要的是，每個配置要針對覆疊使用唯一識別碼。

* * 的範例 `my_notification_area.xml` : * *

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">
    
              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">
    
                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">
    
                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />
    
                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">
    
                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />
    
                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />
    
                  </LinearLayout>
    
                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />
    
                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />
    
                </LinearLayout>
    
                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />
    
              </RelativeLayout>
    
            </merge>

如您所見，通知區域檢視識別碼與標準不同。 很重要的是，每個配置要針對通知區域使用唯一識別碼。

這個簡單的類別範例會讓應用程式 (或應用程式內) 通知顯示在畫面頂端。 我們並未變更在通知區域本身所使用的標準識別碼。

如果您想要的變更，您必須重新定義 `EngagementDefaultNotifier.prepareInAppArea` 方法。 建議您先查看技術文件和原始程式碼的 `EngagementNotifier` 和 `EngagementDefaultNotifier` 如果想要這個層級的進階自訂。

##### 系統通知

藉由擴充 `EngagementDefaultNotifier`, ，您可以覆寫 `onNotificationPrepared` 以改變預設實作所準備的通知。

例如：

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

此範例在使用「進行中」類別時，針對正在顯示為進行中事件的內容產生系統通知。

如果您想要建置 `通知` 物件從頭情況下，您可以傳回 `false` 給方法，然後呼叫 `通知` 自行在 `NotificationManager`。 在此情況下很重要，保留 `contentIntent`, 、 `deleteIntent` 和所使用的通知識別碼 `EngagementReachReceiver`。

以下是這類實作的正確範例：

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent
    
              /* Your customization */
              // builder.set...
    
              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;
    
              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier
    
              /* Return false, we notify ourselves */
              return false;
            }

##### 僅通知的公告

按一下通知，可以藉由覆寫自訂唯一公告管理 `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` 修改已備妥 `意圖`。 使用此方法可讓您輕鬆地調整旗標。

例如若要新增 `SINGLE_TOP` 旗標:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

對於舊版的 Engagement 使用者，請注意，沒有動作 URL 的系統通知現在會啟動在背景中的應用程式，所以可以用沒有動作 URL 的公告來呼叫這個方法。 自訂意圖時，您應該考慮它。

您也可以實作 `EngagementNotifier.executeNotifAnnouncementAction` 從頭。

##### 通知生命週期

使用預設類別時，會在上呼叫部分生命週期方法 `EngagementReachInteractiveContent` 來報告統計資料物件，並更新活動狀態:

-   當通知顯示在應用程式中或放在狀態列中， `displayNotification` 方法呼叫 (它會報告統計資料) 的 `EngagementReachAgent` 如果 `handleNotification` 傳回 `true`。
-   如果關閉通知，則 `exitNotification` 呼叫方法時，會報告統計資料，並可以立即處理下一個活動。
-   如果按一下通知，則 `actionNotification` 是呼叫、 報告統計資料，並啟動相關聯的意圖。

如果您實作的 `EngagementNotifier` 略過預設行為，您必須自行呼叫這些生命週期方法。 以下範例說明一些會略過預設行為的情況：

-   您不需延伸 `EngagementDefaultNotifier`, ，例如實作全新的類別處理。
-   系統通知，您已覆寫 `onNotificationPrepared` 並修改 `contentIntent` 或 `deleteIntent` 中 `通知` 物件。
-   應用程式內通知，您已覆寫 `prepareInAppArea`, ，請務必將至少對應 `actionNotification` 到其中一個 U.I 控制項。

> [AZURE.NOTE] 如果 `handleNotification` 擲回例外狀況，內容會刪除與 `dropContent` 呼叫。 這報告在統計資料中，並且立即可以處理接下來的活動。

### 宣告和輪詢

#### 版面配置

您可以修改 `engagement_text_announcement.xml`, ，`engagement_web_announcement.xml` 和 `engagement_poll.xml` 檔案，以自訂文字公告、 web 公告和輪詢。

這些檔案的標題區域和按鈕區域共用兩個共同的配置。 標題的配置是 `engagement_content_title.xml` 並針對背景使用具名的可繪製檔案。 動作和結束按鈕的配置是 `engagement_button_bar.xml` 並針對背景使用具名的可繪製檔案。

在輪詢中，問題配置和選項會動態地膨脹使用多次 `engagement_question.xml` 配置檔案的問題和 `engagement_choice.xml` 選項的檔案。

#### 類別

##### 替代版面配置

類似通知，活動類別可以用來做為宣告和輪詢的替代版片配置。

例如，若要建立文字公告的類別，您可以擴充 `EngagementTextAnnouncementActivity` 參照該 `AndroidManifest.xml` 檔案:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

請注意，意圖篩選中的類別會用來產生與預設公告活動的差異。

Reach SDK 使用意圖系統來解析特定類別的正確活動，如果解析失敗便會回到預設類別。

然後您必須實作 `MyCustomTextAnnouncementActivity`, ，如果您只想要變更配置 (但保留相同的檢視識別碼)，您只需要在下列範例中定義的類別，例如:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

若要取代文字公告的預設類別，只要取代 `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` 您的實作。

可以以類似的方式自訂 web 公告與輪詢。

您可以針對 web 公告擴充 `EngagementWebAnnouncementActivity` 中宣告活動和 `AndroidManifest.xml` 如下列範例所示:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    
              </intent-filter>
            </activity>

針對輪詢，您可以擴充 `EngagementPollActivity` ，宣告您在 `AndroidManifest.xml` 如下列範例所示:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### 從頭實作

您可以實作公告 (和輪詢) 活動而不延伸的其中一個 `Engagement * 活動` Reach SDK 所提供的類別。 這非常適用於您想要定義不會使用與標準配置相同之檢視的配置。

就像對進階通知自訂一樣，也建議您查看標準實作的原始程式碼。

請記住以下幾點：Reach 將會以特定的意圖 (相對應於意圖篩選)，加上額外的參數，也就是內容識別碼來啟動活動。

若要擷取包含您在網站上建立活動時指定的內容物件，您可以這樣做：

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;
    
              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
    
                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }
    
                setContentView(R.layout.my_text_announcement);
    
                /* Configure views by querying fields on mContent */
                // ...
              }
            }

統計資料，您應該報告內容會顯示在 `onResume` 事件:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

接著，別忘了呼叫 `actionContent(this)` 或 `exitContent(this)` 活動進入背景之前，在內容物件上。

如果您不呼叫 `actionContent` 或 `exitContent`, ，將不會傳送統計資料 (亦即無法分析活動)，更重要的是，接下來的活動將不會通知應用程式處理序重新啟動之前。

方向或其他組態變更可以會使程式碼難以判定是否在活動進入背景，標準的實作可確保使用者離開活動時結束當下報告內容 (按下 `首頁` 或 `回`) 但不是方向變更時。

以下是實作的有趣部分：

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }
    
            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

您可以看到，如果您呼叫 `actionContent(this)` 然後完成活動， `exitContent(this)` 可安全地呼叫而有任何影響。

## 測試

現在請閱讀＜如何在 Android 上測試 Engagement 整合＞，確認您的整合。


[here]: http://developer.android.com/tools/extras/support-library.html#Downloading 
[google cloud messaging]: http://developer.android.com/guide/google/gcm/index.html 
[amazon device messaging]: https://developer.amazon.com/sdk/adm.html 

