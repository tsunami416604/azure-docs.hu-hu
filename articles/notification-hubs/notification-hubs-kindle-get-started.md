<properties
    pageTitle="開始使用適用於 Kindle 應用程式的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Kindle 應用程式。"
    services="notification-hubs"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="11/04/2015"
    ms.author="wesmc"/>


# 開始使用適用於 Kindle 應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概觀

本教學課程將說明如何使用 Azure 通知中樞將推播通知傳送至 Kindle 應用程式。
您將使用 Amazon 裝置傳訊 (ADM)，建立可接收推播通知的空白 Kindle app。

## 先決條件

本教學課程需要下列各項：

+ 取得從 Android SDK (我們假設您將使用 Eclipse) <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android 站台</a>。
+ 請依照 <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">您開發環境上設定</a> ，針對 Kindle 設定您的開發環境。

## 將新的應用程式新增至開發人員入口網站

1. 首先，在 [Amazon 開發人員入口網站] 建立應用程式。

    ![][0]

2. 複製 [應用程式金鑰]****。

    ![][1]

3. 在入口網站中，按一下您的 app 名稱，然後按一下 [Device Messaging]**** 索引標籤。

    ![][2]

4. 按一下 [Create a New Security Profile]****，然後建立新的安全性設定檔 (例如 **TestAdm security profile**)。 然後按一下 [儲存]****。

    ![][3]

5. 按一下 [Security Profiles]**** 以檢視您剛建立的安全性設定檔。 複製 [用戶端 ID]**** 和 [用戶端密碼]**** 值，以供後續使用。

    ![][4]

## 建立 API 金鑰

1. 以系統管理員權限開啟命令提示字元。
2. 導覽至 Android SDK 資料夾。
3. 輸入下列命令：

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  輸入 **android** 做為 [金鑰存放區]**** 密碼。

5.  複製 **MD5** 指模。
6.  返回開發人員入口網站，在 [Messaging]**** 索引標籤中按一下 [Android/Kindle]****，然後輸入您的 app 的封裝名稱 (例如 **com.sample.notificationhubtest**)、[MD5]**** 值，然後按一下 [Generate API Key]****。

## 將認證新增至中樞

在入口網站中，將用戶端密碼和用戶端 ID 新增至通知中樞的 [設定]**** 索引標籤。

## 設定您的應用程式

> [AZURE.NOTE] 建立應用程式時，至少應使用 API 層級 17。

將 ADM 程式庫新增至您的 Eclipse 專案：

1. 若要取得 ADM 程式庫，[下載 SDK]。 將 SDK zip 檔案解壓縮。
2. 在 Eclipse 中，以滑鼠右鍵按一下您的專案，然後按一下 [屬性]****。 選取 **Java 組建路徑** 在左邊，然後選取 ** 程式庫 **頂端的索引標籤。 按一下 [ **新增外部 Jar**, ，然後選取檔案 `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` 從中您解壓縮 Amazon SDK 的目錄。
3. 下載 NotificationHubs Android SDK (連結)。
4. 解壓縮封裝，並接著將檔案拖曳 `通知-中樞-sdk.jar` 到 `libs` Eclipse 資料夾中的。

編輯您支援 ADM 的應用程式資訊清單：

1. 在根資訊清單元素中新增 Amazon 命名空間：

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. 在資訊清單元素下，將權限新增為第一個元素。 將 **[YOUR PACKAGE NAME]** 取代為您用來建立 app 的封裝。

     <permission
      android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
      android:protectionLevel="signature" />
    
     <uses-permission android:name="android.permission.INTERNET"/>
    
     <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
    
     
     <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
    
     
     <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 插入下列元素，做為應用程式元素的第一個子項。 請務必將 **[YOUR SERVICE NAME]** 取代為您在下一節中建立的 ADM 訊息處理常式的名稱 (包括封裝)，並將 **[YOUR PACKAGE NAME]** 取代為您用來建立應用程式的封裝名稱。

     <amazon:enable-feature
           android:name="com.amazon.device.messaging"
                  android:required="true"/>
     <service
         android:name="[YOUR SERVICE NAME]"
         android:exported="false" />
    
     <receiver
         android:name="[YOUR SERVICE NAME]$Receiver" />
    
         
         android:permission="com.amazon.device.messaging.permission.SEND" >
    
         
         <intent-filter>
       <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
       <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
    
       
       <category android:name="[YOUR PACKAGE NAME]" />
         </intent-filter>
     </receiver>


## 建立您的 ADM 訊息處理常式

1. 建立繼承自 `com.amazon.device.messaging.ADMMessageHandlerBase` 的新類別，並將其命名為 `MyADMMessageHandler`，如下圖所示：

    ![][6]

2. 新增下列 `import` 陳述式：

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. 在您建立的類別中新增下列程式碼。 請務必取代中心名稱和連接字串 (接聽)：

     public static final int NOTIFICATION_ID = 1;
     private NotificationManager mNotificationManager;
     NotificationCompat.Builder builder;
     private static NotificationHub hub;
     public static NotificationHub getNotificationHub(Context context) {
         Log.v("com.wa.hellokindlefire", "getNotificationHub");
         if (hub == null) {
             hub = new NotificationHub("[hub name]", "[listen connection string]", context);
         }
         return hub;
     }
    
     public MyADMMessageHandler() {
             super("MyADMMessageHandler");
         }
    
         public static class Receiver extends ADMMessageReceiver
         {
             public Receiver()
             {
                 super(MyADMMessageHandler.class);
             }
         }
    
         private void sendNotification(String msg) {
             Context ctx = getApplicationContext();
    
          mNotificationManager = (NotificationManager)
                 ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
         PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
             new Intent(ctx, MainActivity.class), 0);
    
         NotificationCompat.Builder mBuilder =
             new NotificationCompat.Builder(ctx)
             .setSmallIcon(R.mipmap.ic_launcher)
             .setContentTitle("Notification Hub Demo")
             .setStyle(new NotificationCompat.BigTextStyle()
                      .bigText(msg))
             .setContentText(msg);
    
         mBuilder.setContentIntent(contentIntent);
         mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
     }

4. 在 `OnMessage()` 方法中新增下列程式碼：

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Add the following code to the `OnRegistered` method:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  在 `OnUnregistered` 方法中新增下列程式碼：

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. 在 `MainActivity` 方法中，新增下列 import 陳述式:

        import com.amazon.device.messaging.ADM;

8. 在結尾處新增下列程式碼 `OnCreate` 方法:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }


## 將您的 API 金鑰新增至 app

1. 在 Eclipse 中，在您專案的目錄資產中建立名為 **api_key.txt** 的新檔案。
2. 開啟該檔案，並複製您在 Amazon 開發人員入口網站中產生的 API 金鑰。

## 執行應用程式

1. 啟動模擬器。
2. 在模擬器中從頂端撥動，然後按一下 [Settings]****，再按一下 [My account]****，然後使用有效的 Amazon 帳戶進行註冊。
3. 在 Eclipse 中執行應用程式。

> [AZURE.NOTE] 如果發生問題，請檢查模擬器 (或裝置) 的時間。 時間值必須是正確的。 若要變更 Kindle 模擬器的時間，您可以從 Android SDK platform-tools 目錄執行下列命令：

        adb shell  date -s "yyyymmdd.hhmmss"

## 傳送訊息

使用 .NET 傳送訊息：

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");
    
            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]



[amazon developer portal]: https://developer.amazon.com/home.html 
[download the sdk]: https://developer.amazon.com/public/resources/development-tools/sdk 
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png 
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png 
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png 
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png 
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png 
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png 
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png 
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png 

