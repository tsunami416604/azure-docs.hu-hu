<properties
    pageTitle="開始使用適用於 Android 應用程式的 Azure 通知中樞 | Microsoft Azure"
    description="在本教學課程中，您會了解如何使用 Azure 通知中樞，推播通知到 Android 裝置。"
    services="notification-hubs"
    documentationCenter="android"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="12/15/2015"
    ms.author="wesmc"/>


# 開始使用適用於 Android 應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概觀

本教學課程說明如何使用 Azure 通知中樞傳送推播通知到 Android 應用程式。
您將建立可使用 Google Cloud Messaging (GCM) 接收推播通知的空白 Android app。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

本教學課程示範使用通知中樞的簡單廣播案例。 請確定依照下一個教學課程的步驟進行，以查看如何使用通知中樞來處理特定使用者和裝置群組。


## 開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

完成本教學課程中的程式碼，請參閱 GitHub [這裡](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)。


## 必要條件

本教學課程需要下列各項：

+ Android Studio 中，您可以從下載 <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android 的站台</a>。
+ 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F)。


完成本教學課程是 Android app 所有其他通知中樞教學課程的先決條件。


## 建立支援 Google Cloud Messaging 的專案

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


## 設定新的通知中樞

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. 按一下頂端的 [設定]**** 索引標籤，輸入前一節中取得的 [API Key]**** 值，然後按一下 [儲存]****。

& emsp; & emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

現在已將您的通知中樞設定成使用 GCM，而且您已擁有可用來註冊應用程式以接收通知和傳送推播通知的連接字串。

## <a id="connecting-app"></a>應用程式連接到通知中心

### 建立新的 Android 專案

1. 在 Android Studio 中，啟動新的 Android Studio 專案。

    ![][13]

2. 選擇 [電話和平板電腦]**** 型規格和您要支援的 [Minimum SDK]****。 然後按 [下一步]****。

    ![][14]

3. 為主要活動選擇 [空白活動]****，按 [下一步]****，然後按一下 [完成]****。

### 新增 Google Play 服務至專案

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### 新增程式碼

1. 下載通知-中樞-0.4.jar 檔案從 **檔案** ] 索引標籤的 [通知-中心-Android-SDK 上 Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4)。 在 Android Studio 的 [專案檢視] 視窗中，將檔案直接拖曳到 **libs** 資料夾。 然後在檔案上按一下滑鼠右鍵並按一下 [加入做為程式庫]****。

2. 在**應用程式**的 Build.Gradle 檔案中，在 **dependencies** 一節加入以下這行。

        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

    加入下列儲存機制到 **dependencies** 一節之後。

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

3. 設定應用程式以從 GCM 取得註冊 ID，然後使用此值在通知中樞註冊此 app 執行個體。

 在 AndroidManifest.xml 檔案中新增下列權限，以下  `< / 應用程式 >` 標記。請務必取代 `< 您的封裝 >` AndroidManifest.xml 檔案頂端顯示的封裝名稱 (`com.example.testnotificationhubs` 在此範例中)。

     <uses-permission android:name="android.permission.INTERNET"/>
     <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
     <uses-permission android:name="android.permission.WAKE_LOCK"/>
     <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    
     <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
     <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. 在 **MainActivity** 類別中，新增下列 `匯入` 類別宣告上面的陳述式。

        import android.app.AlertDialog;
        import android.content.DialogInterface;
        import android.os.AsyncTask;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.widget.Toast;

4. 在類別頂端新增下列 Private 成員。

        private String SENDER_ID = "<your project number>";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private String HubName = "<Enter Your Hub Name>";
        private String HubListenConnectionString = "<Your default listen connection string>";
        private static Boolean isVisible = false;

    請務必更新三個預留位置:
    * **SENDER_ID**: 設定 `SENDER_ID` 為您稍早取得從專案中所建立的專案編號 [Google Cloud Console](http://cloud.google.com/console)。
    * **HubListenConnectionString**: 設定 `HubListenConnectionString` 至 **DefaultListenAccessSignature** 中心的連接字串。 您可以複製該連接字串，藉以 **檢視連接字串** 上 **儀表板** 您的中心，在 [Azure 傳統入口網站] 索引標籤。
    * **HubName**: 在 Azure 中頁面頂端會出現在通知中心名稱用於您的中心 (**不** 的完整 URL)。 例如，使用 `"myhub"`。

5. 在 **MainActivity** 類別的 **OnCreate** 方法中，加入下列程式碼以便在活動建立時向通知中樞註冊。

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. 在 **MainActivity.java** 中，針對  **registerWithNotificationHubs()** 方法加入下列程式碼。 向 Google Cloud Messaging 和通知中樞註冊之後，此方法即回報成功。

        @SuppressWarnings("unchecked")
        private void registerWithNotificationHubs() {
            new AsyncTask() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " +
                        hub.register(regid).getRegistrationId());
                    } catch (Exception e) {
                        DialogNotify("Exception",e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

7. 新增 `ToastNotify` 方法來顯示通知，當應用程式正在執行而且顯示的活動。 此外，覆寫 `onStart`, ，`onPause`, ，`onResume` 和 `onStop` 來判斷活動是否可以看到顯示快顯通知。

     @Override
     protected void onStart() {
         super.onStart();
         isVisible = true;
     }
    
     @Override
     protected void onPause() {
         super.onPause();
         isVisible = false;
     }
    
     @Override
     protected void onResume() {
         super.onResume();
         isVisible = true;
     }
    
     @Override
     protected void onStop() {
         super.onStop();
         isVisible = false;
     }
    
     public void ToastNotify(final String notificationMessage)
     {
         if (isVisible == true)
             runOnUiThread(new Runnable() {
                 @Override
                 public void run() {
                     Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                 }
             });
     }

8. 由於 Android 不會顯示通知，因此您必須撰寫自己的接收器。在 **AndroidManifest.xml**, ，新增下列項目內 `< 應用程式 >` 項目。
    > [AZURE.NOTE] 以您的封裝名稱取代預留位置。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>

9. 在 [專案檢視] 中，展開 [應用程式]**** > [src]**** > [主要]**** > [java]****。 以滑鼠右鍵按一下 **java** 底下您的封裝資料夾，按一下 [**New**]，然後按一下 [**Java Class**]。

    ![][6]

10. 在新類別的 [名稱]**** 欄位中輸入 **MyHandler**，然後按一下 [確定]****。

11. 在 **MyHandler.java** 的頂端新增下列 import 陳述式：

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

12. 更新類別宣告，如下所示，以進行 `MyHandler` 的子類別 `com.microsoft.windowsazure.notifications.NotificationsHandler` ，如下所示。

        public class MyHandler extends NotificationsHandler {

13. 加入下列程式碼 `MyHandler` 類別。

此程式碼會覆寫 `OnReceive` 方法，因此，此處理常式就會出現快顯通知，以顯示收到的通知。 此處理常式也會傳送通知給 Android 通知管理員使用 `sendNotification()` 方法。

    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    Context ctx;
    
    static public MainActivity mainActivity;
    
    @Override
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String nhMessage = bundle.getString("message");
    
        sendNotification(nhMessage);
        mainActivity.ToastNotify(nhMessage);
    }
    
    private void sendNotification(String msg) {
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

14. 在 Android Studio 的功能表列上，按一下 [建置]**** > [重新建置專案]****，確保沒有偵測到任何錯誤。

## 傳送通知

您可以測試應用程式中接收通知，如下列畫面所示，[Azure 傳統入口網站] 透過通知中心上的 [偵錯] 索引標籤，以傳送通知。

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (選擇性) 從 App 傳送通知

1. 在 Android Studio 的 [專案檢視] 中展開 [應用程式]**** > [src]**** > [主要]**** > [res]**** > [配置]****。開啟 **activity_main.xml** 配置檔案，然後按一下 [**文字**] 索引標籤以更新檔案的文字內容。更新下列程式碼會將新增新 `按鈕` 和 `EditText` 控制項通知訊息傳送至通知中心。在底部，將這段程式碼才能 `< / RelativeLayout >`。

     <Button
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:text="@string/send_button"
     android:id="@+id/sendbutton"
     android:layout_centerVertical="true"
     android:layout_centerHorizontal="true"
     android:onClick="sendNotificationButtonOnClick" />
    
     <EditText
     android:layout_width="match_parent"
     android:layout_height="wrap_content"
     android:id="@+id/editTextNotificationMessage"
     android:layout_above="@+id/sendbutton"
     android:layout_centerHorizontal="true"
     android:layout_marginBottom="42dp"
     android:hint="@string/notification_message_hint" />

2. 新增此行 **build.gradle** 檔案下 `android`

        useLibrary 'org.apache.http.legacy'

3. 在 Android Studio 的 [專案檢視] 中展開 [應用程式]**** > [src]**** > [主要]**** > [res]**** > [值]****。開啟 **strings.xml** 檔案，並加入新參考的字串值 `按鈕` 和 `EditText` 控制項。之前加入這些檔案的底部 `< / 資源 >`。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>

4. 在您 **MainActivity.java** 檔案中，新增下列 `匯入` 上述陳述式 `MainActivity` 類別。

     import java.net.URLEncoder;
     import javax.crypto.Mac;
     import javax.crypto.spec.SecretKeySpec;
    
     import android.util.Base64;
     import android.view.View;
     import android.widget.EditText;
    
     import org.apache.http.HttpResponse;
     import org.apache.http.client.HttpClient;
     import org.apache.http.client.methods.HttpPost;
     import org.apache.http.entity.StringEntity;
     import org.apache.http.impl.client.DefaultHttpClient;

5. 在您 **MainActivity.java** 檔案頂端新增下列成員的 `MainActivity` 類別。

    更新 `HubFullAccess` 與 **DefaultFullSharedAccessSignature** 中心的連接字串。 此連接字串可以從 [Azure 傳統入口網站]，即可複製 **檢視連接字串** 上 **儀表板** 通知中樞] 索引標籤。

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;
        private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. 您的活動會保留中心名稱以及中心的完整共用存取連接字串。 您必須建立軟體存取簽章 (SaS) 權杖來驗證 POST 要求，以將訊息傳送至您的通知中樞。 這是藉由剖析連接字串中的金鑰資料，然後再建立 SaS 權杖中所述 [一般概念](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API 參考。

 在 **MainActivity.java**, ，將下列方法加入 `MainActivity` 類別，以剖析連接字串。

     /**
      * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
      * to parse the connection string so a SaS authentication token can be
      * constructed.
      *
      * @param connectionString This must be the DefaultFullSharedAccess connection
      *                         string for this example.
      */
     private void ParseConnectionString(String connectionString)
     {
         String[] parts = connectionString.split(";");
         if (parts.length != 3)
             throw new RuntimeException("Error parsing connection string: "
                     + connectionString);
    
         for (int i = 0; i < parts.length; i++) {
             if (parts[i].startsWith("Endpoint")) {
                 this.HubEndpoint = "https" + parts[i].substring(11);
             } else if (parts[i].startsWith("SharedAccessKeyName")) {
                 this.HubSasKeyName = parts[i].substring(20);
             } else if (parts[i].startsWith("SharedAccessKey")) {
                 this.HubSasKeyValue = parts[i].substring(16);
             }
         }
     }

7. 在 **MainActivity.java**, ，將下列方法加入 `MainActivity` 類別來建立 SaS 驗證權杖。

     /**
      * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
      * construct a SaS token from the access key to authenticate a request.
      *
      * @param uri The unencoded resource URI string for this operation. The resource
      *            URI is the full URI of the Service Bus resource to which access is
      *            claimed. For example,
      *            "http://<namespace>.servicebus.windows.net/<hubName>"
      */
     private String generateSasToken(String uri) {
    
         String targetUri;
         try {
             targetUri = URLEncoder
                     .encode(uri.toString().toLowerCase(), "UTF-8")
                     .toLowerCase();
    
             long expiresOnDate = System.currentTimeMillis();
             int expiresInMins = 60; // 1 hour
             expiresOnDate += expiresInMins * 60 * 1000;
             long expires = expiresOnDate / 1000;
             String toSign = targetUri + "\n" + expires;
    
             // Get an hmac_sha1 key from the raw key bytes
             byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
             SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
             // Get an hmac_sha1 Mac instance and initialize with the signing key
             Mac mac = Mac.getInstance("HmacSHA256");
             mac.init(signingKey);
    
             // Compute the hmac on input data bytes
             byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
             // Using android.util.Base64 for Android Studio instead of
             // Apache commons codec
             String signature = URLEncoder.encode(
                     Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
             // Construct authorization string
             String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                     + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
             return token;
         } catch (Exception e) {
             DialogNotify("Exception Generating SaS",e.getMessage().toString());
         }
    
         return null;
     }

8. 在 **MainActivity.java**, ，將下列方法加入 `MainActivity` 類別來處理 **傳送通知** ] 按鈕點選並使用 REST API，將通知訊息傳送至中心。

     /**
      * Send Notification button click handler. This method parses the
      * DefaultFullSharedAccess connection string and generates a SaS token. The
      * token is added to the Authorization header on the POST request to the
      * notification hub. The text in the editTextNotificationMessage control
      * is added as the JSON body for the request to add a GCM message to the hub.
      *
      * @param v
      */
     public void sendNotificationButtonOnClick(View v) {
         EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
         final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
         new Thread()
         {
             public void run()
             {
                 try
                 {
                     HttpClient client = new DefaultHttpClient();
    
                     // Based on reference documentation...
                     // http://msdn.microsoft.com/library/azure/dn223273.aspx
                     ParseConnectionString(HubFullAccess);
                     String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                     HttpPost post = new HttpPost(url);
    
                     // Authenticate the POST request with the SaS token
                     post.setHeader("Authorization", generateSasToken(url));
    
                     // JSON content for GCM
                     post.setHeader("Content-Type", "application/json;charset=utf-8");
    
                     // Notification format should be GCM
                     post.setHeader("ServiceBusNotification-Format", "gcm");
                     post.setEntity(new StringEntity(json));
    
                     HttpResponse response = client.execute(post);
                 }
                 catch(Exception e)
                 {
                     DialogNotify("Exception",e.getMessage().toString());
                 }
             }
         }.start();
     }




## 測試應用程式

#### 模擬器測試

如果您要在模擬器上進行測試，請確定您的模擬器映像支援您為 app 選擇的 Google API 層級。 如果您的映像不支援 Google Api，您會得到 **SERVICE\_NOT\_AVAILABLE** 例外狀況。

此外，請確定已將您的 Google 帳戶加入至執行中模擬器的 [設定]**** > [帳戶]**** 之下。 否則，嘗試向 GCM 註冊可能會導致 **AUTHENTICATION\_FAILED** 例外狀況。

#### 測試應用程式

1. 執行 app，並注意已回報註冊成功的註冊識別碼。

    ![][18]

2. 輸入通知訊息，以傳送給已向中心註冊的所有 Android 裝置。

    ![][19]

3. 按 [**傳送通知**]。 任何已經執行的應用程式的裝置會顯示 `AlertDialog` 含通知訊息。 未執行應用程式但先前已註冊通知的裝置，將會收到已加入至通知管理員的通知。 從左上角往下撥動，即可檢視通知。

    ![][21]

## 後續步驟

在此簡單範例中，您會使用入口網站或主控台應用程式，將廣播通知傳送到您的所有 Windows 裝置。 我們建議 [使用通知中心來推播通知給使用者] 教學課程的下一個步驟。 它會示範如何使用標記以特定使用者為目標，從 ASP.NET 後端傳送通知。

如果您想要按興趣群組分隔使用者，請參閱 [使用通知中心傳送即時新聞]。

若要了解通知中心的詳細資訊，請參閱 [通知中心指引]。







[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png 
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png 
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png 
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png 
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png 
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png 
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png 
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png 
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png 
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png 
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png 
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png 
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png 
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png 
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png 
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png 
[get started with push notifications in mobile services]: ../mobile-services-javascript-backend-android-get-started-push.md 
[mobile services android sdk]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409 
[referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800 
[azure classic portal]: https://manage.windowsazure.com/ 
[notification hubs guidance]: http://msdn.microsoft.com/library/jj927170.aspx 
[use notification hubs to push notifications to users]: notification-hubs-aspnet-backend-android-notify-users.md 
[use notification hubs to send breaking news]: notification-hubs-aspnet-backend-android-breaking-news.md 

