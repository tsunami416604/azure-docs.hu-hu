1. Az a **app** projektre, nyissa meg a fájlt `AndroidManifest.xml`. Cserélje le a következő két lépést a kódban  *`**my_app_package**`*  a projekthez az alkalmazáscsomag nevét. Ez az az érték a `package` attribútuma a `manifest` címke.
2. A következő új engedélyek hozzáadása után a meglévő `uses-permission` elem:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Az alábbi kód után adja hozzá a `application` nyitó:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Nyissa meg a fájlt *ToDoActivity.java*, és adja hozzá a következő importálási utasítást:

        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. A titkos változó hozzáadása a osztály. Cserélje le  *`<PROJECT_NUMBER>`*  az alkalmazáshoz az előző eljárás a Google által hozzárendelt projekt számát.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Módosítsa a definíciója *MobileServiceClient* a **titkos** való **nyilvános, statikus**, így most néz ki:

        public static MobileServiceClient mClient;
7. Adjon hozzá egy új osztályt értesítések kezelésére. A Project Explorer nyissa meg a **src** > **fő** > **java** csomópontokat, és kattintson a jobb gombbal a csomag nevét csomópont. Kattintson a **új**, és kattintson a **Java-osztály**.
8. A **neve**, típus `MyHandler`, és kattintson a **OK**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)

9. A MyHandler fájlban cserélje le a osztály meghatározó deklarációban:

        public class MyHandler extends NotificationsHandler {
10. Adja hozzá a következő importálási utasításokat a a `MyHandler` osztály:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. Ezután adja hozzá az ennek a tagnak a `MyHandler` osztály:

        public static final int NOTIFICATION_ID = 1;
12. Az a `MyHandler` osztály, adja hozzá a következő kódot a bírálja felül a **onRegistered** metódus, amely a mobilszolgáltatás értesítési központ regisztrálja az eszközt.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);

           new AsyncTask<Void, Void, Void>() {

               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. Az a `MyHandler` osztály, adja hozzá a következő kódot a bírálja felül a **onReceive** metódus, amely azt eredményezi, az értesítés érkezik megjelenített.

        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");

               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags

               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();

               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. Vissza a TodoActivity.java fájlban frissítse a **onCreate** metódusában a *ToDoActivity* osztály az értesítési kezelő osztály regisztrálni. Ügyeljen arra, hogy ez a kód után adja hozzá a *MobileServiceClient* létrejön.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Az alkalmazás most frissíteni leküldéses értesítések támogatásához használható.
