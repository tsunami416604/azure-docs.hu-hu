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


# 如何在 Android 上整合 Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)


此程序描述在您的 Android 應用程式中啟動 Engagement 分析和監視功能的最簡單方法。
> [AZURE.IMPORTANT] 最低 Android SDK API 層級必須是 10 或更高版本 (Android 2.3.3 或更高版本)。

下列步驟足以啟動計算使用者、工作階段、活動、當機和技術相關之所有統計資料所需的記錄檔報表。 需要計算事件、 錯誤及工作等其他統計資料的記錄檔報告必須使用 Engagement API 手動完成 (請參閱 [如何使用進階的 Mobile Engagement 標記 API 在 Android](mobile-engagement-android-use-engagement-api.md) 因為這些統計資料與相依的應用程式。

## 將 Engagement SDK 和服務嵌入至您的 Android 專案

下載 Android SDK 從 [這裡](https://aka.ms/vq9mfn)
取得 `行動-engagement-VERSION.jar` 並將它們放入 `libs` Android 專案的資料夾 (如果它尚不存在，請建立程式庫資料夾)。
> [AZURE.IMPORTANT]
> 如果您使用 ProGuard 建立應用程式封裝，您需要保留一些類別。 您可以使用下列組態程式碼片段：
>
>
            -保留公用類別 * 擴充 android.os.IInterface
            -保留類別 com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

在啟動器活動中呼叫下列方法，指定 Engagement 連接字串：

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

您應用程式的連接字串會顯示在 Azure 入口網站。

-   如果遺漏，加入下列 Android 權限 (之前 `< 應用程式 >` 標記):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   新增下列區段 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   變更 `< 您的應用程式名稱 >` 由應用程式的名稱。

> [AZURE.TIP] `Android: 標籤` 屬性可讓您選擇 Engagement 服務的名稱會出現在使用者電話的 「 執行中服務 」 畫面中。建議將此屬性設定為 `"< 您的應用程式名稱 > 服務 」` (例如:`"AcmeFunGameService"`)。

指定 `android: process` 屬性可確保 Engagement 服務將會執行在自己的處理序 (您的應用程式會造成主要 /UI 執行緒可能較無回應，Engagement 執行相同的處理序中)。
> [AZURE.NOTE] 您將放在任何程式碼 `Application.onCreate()` 和其他應用程式回呼將會執行所有應用程式的處理程序，其中包括 Engagement 服務。 可能會產生不必要的副作用 (例如 Engagement 處理程序中不必要的記憶體配置和執行緒、重複的廣播接收器或服務)。

如果您覆寫 `Application.onCreate()`, ，建議您先開頭處新增下列程式碼片段程式 `Application.onCreate()` 函式:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;
    
               ... Your code...
             }

您可以執行相同的動作， `Application.onTerminate()`, ，`Application.onLowMemory()` 和 `Application.onConfigurationChanged(...)`。

您也可以擴充 `EngagementApplication` 改為延伸 `應用程式`: 回呼 `Application.onCreate()` 程序檢查，並呼叫 `Application.onApplicationProcessCreate()` 只有目前的處理序不是裝載 Engagement 服務，如果相同的規則也適用於其他回呼。

## 基本報告

### 建議使用的方法: 多載您 `活動` 類別

為了啟用 Engagement 計算使用者、 工作階段、 活動、 當機和技術的統計資料所需的所有記錄檔報告，您只需要讓所有您 `* 活動` 子類別繼承自對應 `Engagement * 活動` 類別 (例如: 如果您的舊版活動延伸 `ListActivity`, ，使其延伸 `EngagementListActivity`)。

**沒有 Engagement：**

            package com.company.myapp;
    
            import android.app.Activity;
            import android.os.Bundle;
    
            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**有 Engagement：**

            package com.company.myapp;
    
            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;
    
            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] 當使用 `EngagementListActivity` 或 `EngagementExpandableListActivity`, ，務必先呼叫至 `requestWindowFeature(...);` 進行的呼叫之前 `super.onCreate(...);`, ，否則會發生當機。

我們提供的子類別 `FragmentActivity` 和 `MapActivity`, ，以避免使用的應用程式的問題，但 **ProGuard**, ，我們不包含在 `engagement.jar`。

您可以找到這些類別在 `src` 資料夾，並可將其複製到您的專案。 這些類別也會顯示在 JavaDoc 中。****

### 替代方法: 呼叫 `startActivity()` 和 `endActivity()` 手動

如果您無法或不想多載您 `活動` 類別，您可以改為啟動及結束您的活動藉由呼叫 `EngagementAgent`的直接方法。
> [AZURE.IMPORTANT] Android SDK 絕不會呼叫 `endActivity()` 方法，即使在關閉應用程式 (在 Android 上，應用程式其實永遠不會關閉)。 因此，它是 *高* 建議您呼叫 `startActivity()` 方法中的 `onResume` 回呼的 *所有* 您的活動和 `endActivity()` 方法中的 `onPause()` 回呼的 *所有* 您的活動。 這是確保不會遺漏工作階段的唯一方法。 如果遺漏了工作階段，Engagement 服務將永遠不會從 Engagement 後端中斷連線 (因為只要工作階段處於暫止狀態，服務就會保持連線)。

下列是一個範例：

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }
    
              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

這個範例非常類似於 `EngagementActivity` 類別與變種，以提供其原始程式碼 `src` 資料夾。

## 測試

現在請確認您的整合，方法是在模擬器或裝置中執行您的行動應用程式，並確認它會在 [監視] 索引標籤上註冊工作階段。

下一節是選擇性的。

## 位置報告

如果您想要報告位置，您需要加入幾行組態 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記)。

### 延遲區域位置報告

延遲區域位置報告允許報告國家、地區以及與裝置相關聯的位置。 這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。 每個工作階段最多報告一次裝置區域。 絕不會使用 GPS，因此這類位置報告對於電池的影響很小 (但不是沒有)。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。 它們也可用來做為觸達活動的準則。

若要啟用簡易區域位置報告，您可以使用此程序中先前所述的組態執行：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

您可以繼續使用或者 `ACCESS_FINE_LOCATION` 如果已在您的應用程式中使用。

### 即時位置報告

即時位置報告允許報告與裝置相關聯的緯度和經度。 根據預設，這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)，且只會在應用程式於前景中執行 (也就是在工作階段) 時，報告才會為作用中。

即時位置「不會」**用來計算統計資料。 其唯一目的是要允許即時使用
地理柵欄 \<Reach-Audience-geofencing\> 觸達活動的準則。

若要啟用即時位置報告，您可以使用此程序中先前所述的組態執行：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

您可以繼續使用或者 `ACCESS_FINE_LOCATION` 如果已在您的應用程式中使用。

#### GPS 的報告

根據預設，即時位置報告只會使用網路位置。 若要啟用 GPS 位置 (精準度大為提高)，請使用組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您還需要新增下列權限 (如果未有此權限)：

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### 背景報告

根據預設，即時位置報告只在應用程式在前景中執行 (也就是在工作階段) 時才會為作用中。 若也要在背景中啟用報告，請使用組態物件：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] 當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。

如果使用者重新啟動裝置，就會停止背景位置報表，您可以加入以下內容，讓它在開機時自動重新啟動：

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

您還需要新增下列權限 (如果未有此權限)：

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Android M 權限

從 Android M 開始，某些權限會在執行階段管理，而且需要使用者核准。

如果您針對 Android API 層級 23，新的應用程式安裝預設將會關閉執行階段權限。 否則預設將會開啟。

使用者可以從裝置設定功能表啟用/停用這些權限。 從系統功能表關閉權限會刪除應用程式的背景處理程序，這是一種系統行為，對於在背景中接收推播的功能不會有任何影響。

在 Mobile Engagement 環境中，需要在執行階段核准的權限為：

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE` (只有在目標 Android API 層級 23 的範本)

只有觸達概觀功能才會使用外部儲存體。 如果您發現向使用者要求這個權限會引起混亂，而您只是為了 Mobile Engagement 才使用它但必須停用概觀功能，您可以將它移除。

對於位置功能，您應該使用標準的系統對話方塊向使用者要求權限。 如果使用者核准，您必須告訴 `EngagementAgent` 採取即時變更納入考量 (否則變更會被處理的下次使用者啟動應用程式)。

以下是要在應用程式的活動中用來要求權限，而且如果正以轉寄結果的程式碼範例 `EngagementAgent`:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */
    
      /* Request permissions */
      requestPermissions();
    }
    
    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);
    
        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }
    
    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## 進階報告

此外，如果您想要報告應用程式特定事件、 錯誤和作業，您必須透過方法使用 Engagement API `EngagementAgent` 類別。 這個類別的物件可以藉由呼叫是抓取 `EngagementAgent.getInstance()` 靜態方法。

Engagement API 允許使用所有 Engagement 的進階功能，詳情請見＜如何
在 Android 上的使用 engagement API (以及技術文件 `EngagementAgent` 類別)。

## 進階組態 (在 AndroidManifest.xml 中)

如果您希望在使用 Wifi 或關閉螢幕時仍確保即時傳送統計資料，請新增下列選用權限：

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

如果您想要停用當機報告，加入下列 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

根據預設，Engagement 服務會即時報告記錄檔。 如果應用程式報告記錄檔的頻率很高，建議您緩衝記錄檔，以固定時段一次報告全部的記錄檔 (此稱為「高載模式」)。 若要這樣做，請加入這 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響： 所有工作階段和工作持續時間將調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。 建議使用低於 30000 (30 秒) 的閾值。

根據預設，Engagement 服務在可使用網路時會立即與我們的伺服器建立連線。 如果您想要延遲連線，加入下列 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

根據預設，工作階段會在最後一個活動結束後 10 秒終止 (最後一個活動通常發生在按 [首頁] 或 [上一步] 鍵、將行動電話設定為閒置或跳到另一個應用程式)。 這是為了避免當使用者退出但很快又返回應用程式 (例如使用者挑選影像、檢查通知等等) 時，工作階段產生分割的狀況。 您可以修改這個參數。 若要這樣做，請加入這 (之間 `< 應用程式 >` 和 `< / 應用程式 >` 標記):

            <meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

## 停用記錄報告

### 使用方法呼叫

如果您想要 Engagement 停止傳送記錄檔，可以呼叫：

            EngagementAgent.getInstance(context).setEnabled(false);

這個呼叫是持續性的：它使用共用的喜好設定檔案。

如果當您呼叫此函式時 Engagement 已啟用，停止服務可能需要 1 分鐘時間。 不過，當您下次啟動應用程式時，完全不會啟動服務。

您可以啟用記錄報告一次呼叫相同函式 `true`。

### 在您自己的整合 `PreferenceActivity`

不呼叫此函式，您也可以整合此設定直接在現有 `PreferenceActivity`。

您可以在設定 Engagement 使用您的喜好設定檔案 (搭配所需的模式) `AndroidManifest.xml` 檔案中使用 `應用程式的中繼資料`:

-   `Engagement: 代理程式: 設定: 名稱` 金鑰用來定義共用喜好設定檔案的名稱。
-   `Engagement: 代理程式: 設定: 模式` 金鑰用來定義共用喜好設定檔案的模式，您應該使用相同的模式，如同您 `PreferenceActivity`。 模式必須以數字傳遞：如果您在程式碼中使用常數旗標的組合，請檢查總值。

Engagement 一律使用 `engagement: 索引鍵` 布林管理這項設定的喜好設定檔案中的索引鍵。

下列範例的 `AndroidManifest.xml` 顯示的預設值:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

然後您可以加入 `CheckBoxPreference` 喜好設定配置，如下:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />


[device api]: http://go.microsoft.com/?linkid=9876094 

