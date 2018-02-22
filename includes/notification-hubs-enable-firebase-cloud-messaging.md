

1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza ki a **az Android-alkalmazás hozzáadása Firebase**. Kövesse az utasításokat.

    ![Androidos Firebase hozzáadása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. A Firebase konzolban válassza a projekthez ikonjára. Válassza ki **Projektbeállítások**.

    ![Válassza ki a Projektbeállítások](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Válassza ki a **általános** a Projektbeállítások lapja. Töltse le a **google-services.json** fájlt, amely tartalmazza a Server API-kulcs és az ügyfél-azonosítót.
5. Válassza ki a **Cloud Messaging** a Projektbeállítások lap, és másolja a értékének a **örökölt kiszolgálókulcs**. Ez az érték a notification hub hozzáférési házirend konfigurálására használt.
