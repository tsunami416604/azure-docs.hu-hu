1. 在您 **應用程式** 專案中，開啟檔案 `AndroidManifest.xml`。 在下兩個步驟中的程式碼，取代 _`**my_app_package**`_ 專案的應用程式套件名稱，即值 `package` 屬性 `manifest` 標記。 

2. 在現有 `uses-permission` 元素中新增下列新權限：

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 在 `application` 起始標籤後新增下列程式碼： 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. 加入這一行底下 *相依性* 中 **build.gradle** 檔案位於應用程式目錄，並重新同步 gradle 與專案: 

        compile(group: 'com.microsoft.azure', name: 'azure-notifications-handler', version: '1.0.1', ext: 'jar')


5. 開啟檔案 *ToDoItemActivity.java*, ，新增下列 import 陳述式:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


6. 新增下列私用變數至類別: 取代 _`<PROJECT_NUMBER>`_ 由 Google 指派給您的應用程式之前程序中的專案編號:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. 變更的定義 *MobileServiceClient* 從 **私人** 至 **公用靜態**, ，因此現在看起來像這樣:

        public static MobileServiceClient mClient;



8. 接下來我們需要加入新類別來處理通知。 在 [專案總管] 中，開啟 **src** = > **主要** = > **java** 節點，然後以滑鼠右鍵按一下封裝名稱節點: 按一下 **新增**, ，然後按一下 [ **Java 類別**。

9. 在 **名稱** 類型 `MyHandler`, ，然後按一下 [ **確定**。 


    ![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


10. 在 MyHandler 檔案中，將類別宣告取代為 

        public class MyHandler extends NotificationsHandler {


11. 為 `MyHandler` 類別新增下列匯入陳述式：

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

    
12. 接著為 `MyHandler` 類別新增下列成員：

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;


13. 在 `MyHandler` 類別中，新增下列程式碼來覆寫 **onRegistered** 方法，行動服務通知中心註冊您的裝置。

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
    
            new AsyncTask<Void, Void, Void>() {
                            
                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
                        return null;
                    }
                    catch(Exception e) { 
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }



14. 在 `MyHandler` 類別中，新增下列程式碼來覆寫 **onReceive** 方法，會使通知被接收到時顯示。

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
    
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);
    
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);
    
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }


15. 回到 TodoActivity.java 檔案中，更新 **onCreate** 方法 *ToDoActivity* 類別，以註冊通知處理常式類別。 請務必將此程式碼之後 *MobileServiceClient* 具現化。


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Your app is now updated to support push notifications.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

