

1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után kattintson az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) gombra, és kövesse a megjelenő utasításokat.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. A Firebase konzoljában kattintson a projektjéhez tartozó fogaskerékre, majd a **Project Settings** (Projektbeállítások) elemre.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Kattintson a **általános** a Projektbeállítások lap, és töltse le a **google-services.json** fájlt, amely tartalmazza a Server API-kulcs és az ügyfél-azonosítót.
5. Kattintson a **Cloud Messaging** a Projektbeállítások lap, és másolja a értékének a **örökölt kiszolgálókulcs**. Ez az érték használható az értesítési központ hozzáférési házirend konfigurálása.
