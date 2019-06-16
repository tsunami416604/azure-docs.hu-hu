---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141229"
---
1. Az a **alkalmazás** projekt, nyissa meg a fájlt `AndroidManifest.xml`. Az alábbi kód után adja hozzá a `application` nyitó kódcímkét:

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

2. Nyissa meg a fájlt `ToDoActivity.java`, és adja meg a következő módosításokat:

    - Adja hozzá az importálási utasítást:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Definíciójának módosítása `MobileServiceClient` a **privát** való **magánhálózati statikus**, így mostantól néz ki:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Adjon hozzá `registerPush` módszer:

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

    - Frissítés a **onCreate** módszere a `ToDoActivity` osztály. Ügyeljen arra, hogy ez a kód után adja hozzá a `MobileServiceClient` példányosítása.

        ```java
        registerPush();
        ```

3. Adjon hozzá egy új osztályt, értesítések kezeléséhez. A Project Explorer, nyissa meg a **alkalmazás** > **java** > **your project névtér** csomópontokat, és kattintson a jobb gombbal a csomag neve csomópont. Kattintson a **új**, és kattintson a **Java-osztály**. A név, írja be a `ToDoMessagingService`, majd kattintson az OK gombra. Ezután cserélje le az osztálydeklaráció együtt:

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

4. Adjon hozzá egy másik osztály token frissítések kezelésére. Hozzon létre `ToDoInstanceIdService` java osztályt, és cserélje le az osztálydeklaráció együtt:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Az alkalmazás most már frissül, és támogatja a leküldéses értesítéseket.
