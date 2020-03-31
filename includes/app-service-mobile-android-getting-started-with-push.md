---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179633"
---
1. Az **alkalmazásprojektben** nyissa `AndroidManifest.xml`meg a fájlt . A nyitó címke `application` után adja hozzá a következő kódot:

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

2. Nyissa meg `ToDoActivity.java`a fájlt , és hajtsa végre a következő módosításokat:

    - Adja hozzá az importálási utasítást:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Módosítsa a `MobileServiceClient` definícióját **a** **magán-privát statikus**, így most így néz ki:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Módszer `registerPush` hozzáadása:

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

    - Frissítse az `ToDoActivity` osztály **onCreate** metódusát. Győződjön meg róla, `MobileServiceClient` hogy adja hozzá ezt a kódot, miután a példányosított.

        ```java
        registerPush();
        ```

3. Új osztály hozzáadása az értesítések kezeléséhez. A Project Explorerben nyissa meg az **alkalmazást** > **java** > **a projekt-névtér** csomópontokkal, és kattintson a jobb gombbal a csomag névcsomópontjára. Kattintson **az Új**gombra, majd a **Java-osztály parancsra.** A Név `ToDoMessagingService`mezőbe írja be a t, majd kattintson az OK gombra. Ezután cserélje ki az osztálynyilatkozatot a következőre:

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

4. Adjon hozzá egy másik osztályt a tokenfrissítések kezeléséhez. Java-osztály létrehozása, `ToDoInstanceIdService` és az osztálydeklaráció cseréje a következőre:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Az alkalmazás most frissül, hogy támogassa a leküldéses értesítéseket.
