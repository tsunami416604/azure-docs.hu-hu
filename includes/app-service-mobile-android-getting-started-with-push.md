1. Az a **app** projektre, nyissa meg a fájlt `AndroidManifest.xml`. Az alábbi kód után adja hozzá a `application` nyitó:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Nyissa meg a fájlt `ToDoActivity.java`, és ellenőrizze a következő módosításokat:

    - Adja hozzá az importálási utasítást:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Módosítsa a definíciója `MobileServiceClient` a **titkos** való **magánhálózati statikus**, így most néz ki:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Adja hozzá `registerPush` módszert:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Frissítés a **onCreate** metódusában a `ToDoActivity` osztály. Ügyeljen arra, hogy ez a kód után adja hozzá a `MobileServiceClient` létrejön.

        ```java
        registerPush();
        ```

3. Adjon hozzá egy új osztályt értesítések kezelésére. A Project Explorer nyissa meg a **app** > **java** > **a projekt névtér** csomópontokat, és kattintson a jobb gombbal a csomag nevét csomópont. Kattintson a **új**, és kattintson a **Java-osztály**. A név, írja be a `ToDoMessagingService`, majd kattintson az OK gombra. Ezután cserélje le a osztály meghatározó deklarációban:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Adjon hozzá egy másik osztály kezeli a token frissítéseket. Hozzon létre `ToDoInstanceIdService` java osztály, és cserélje le a osztály meghatározó deklarációban:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Az alkalmazás most frissíteni leküldéses értesítések támogatásához használható.
