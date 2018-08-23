---
title: Leküldéses értesítések küldése adott Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával | Microsoft Azure
description: Megtanulhatja, hogyan küldhet leküldéses értesítéseket adott Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával.
services: notification-hubs
documentationcenter: android
author: dimazaid
manager: kpiteira
editor: spelluru'
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/06/2018
ms.author: dimazaid
ms.openlocfilehash: 1751071aa37665b5cea51f7be76990020ad569ab
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919400"
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-cloud-messaging"></a>Oktatóanyag: Leküldéses értesítések küldése adott Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt mutatja be, hogy hogyan továbbíthat friss híreket az Android-alkalmazásokba az Azure Notification Hubs használatával. Az oktatóanyag végére regisztrálni tud majd az Önt érdeklő hírkategóriákra, és csak az ezekre a kategóriákra vonatkozó leküldéses értesítéseket kapni. Ezt a forgatókönyvet gyakran használják olyan alkalmazásokban, ahol az értesítéseket egy adott téma iránt korábban érdeklődő felhasználók csoportjainak kell elküldeni. Ilyen lehet például egy RSS-olvasó, a zenerajongóknak készült alkalmazások stb.

A közvetítési forgatókönyveket úgy lehet engedélyezni, ha az értesítési központban a regisztráció létrehozásakor hozzáad egy vagy több *címkét*. Ha az értesítések küldése egy címkére történik, az adott címkére regisztrált összes eszköz megkapja az értesítést. A címkék egyszerű sztringek, nem szükséges őket előzetesen kiosztani. További információ a címkékről: [Útválasztás és címkekifejezések az értesítési központokban](notification-hubs-tags-segment-push-message.md).

Az oktatóanyag során a következő lépéseket hajtja végre: 

> [!div class="checklist"]
> * Kategóriaválasztást ad a mobilalkalmazáshoz.
> * Értesítésekre regisztrál címkékkel. 
> * Címkés értesítéseket küld. 
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag arra az alkalmazásra épül, amelyet az [Oktatóanyag: Leküldéses értesítések küldése Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával][get-started] című részben hozott létre. Mielőtt hozzákezdene ehhez az oktatóanyaghoz, végezze el az [Oktatóanyag: Leküldéses értesítések küldése Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával] című feladatot[get-started].

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz
Az első lépésben hozzá kell adni a felhasználói felületi elemeket a meglévő fő tevékenységhez, amelyekkel a felhasználó kiválaszthatja a regisztrálni kívánt kategóriákat. A felhasználó által kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. Nyissa meg a res/layout/activity_main.xml fájlt, és cserélje le a tartalmát a következőre:
   
    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Nyissa meg a res/values/strings.xml fájlt és adja hozzá az alábbi sorokat:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    A main_activity.xml grafikus elrendezésének az alábbi képhez hasonlóan kell kinéznie:
   
    ![][A1]
3. Hozzon létre egy osztályt `Notifications` ugyanabban a csomagban, ahol a **MainActivity** osztály található.

    ```java   
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName, 
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```
       
    Ez az osztály a helyi tárolóban tárolja a hírkategóriákat, amelyeket ennek az eszköznek meg kell kapnia. Ezenkívül olyan metódusokat is tartalmaz, amelyek az adott kategóriákra való regisztrációra szolgálnak.
4. A **MainActivity** osztályban távolítsa el a**NotificationHub** és a **GoogleCloudMessaging** privát mezőit, és adjon hozzá egy **Értesítések** mezőt:

    ```java   
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Ezután az **OnCreate** metódusban távolítsa el a **hub** mező inicializálását és a **registerWithNotificationHubs** metódust. Majd adja hozzá az alábbi sorokat, amelyek a **Notifications** osztály egy példányát hozzák létre. 

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Győződjön meg arról, hogy a központ neve és a kapcsolati sztring megfelelően be van állítva a NotificationSettings osztályban.

    > [AZURE.NOTE] Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a figyelési hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.
1. Adja hozzá a következő importálásokat:

    ```java   
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
1. Adja hozzá a következő `subscribe` metódust, amely a feliratkozási gombra való kattintás eseményét kezeli:        
   
    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```
       
    Ez a módszer egy kategórialistát hoz létre, és a **Notifications** osztályt használja a lista tárolásához a helyi tárterületen, illetve a megfelelő címkék regisztrációjához az értesítési központban. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

Az alkalmazás most már képes egy kategóriakészlet tárolására az eszköz helyi tárterületén, és az értesítési központban is regisztrálhat, ha a felhasználó módosítja a kiválasztott kategóriákat.

## <a name="register-for-notifications"></a>Regisztráció értesítésekre
Ezek a lépések végzik az értesítési központban való regisztrációt az indításkor, a helyi tárterületen tárolt kategóriák használatával.

> [!NOTE]
> Mivel a Google Cloud Messaging (GCM) által hozzárendelt regisztrációs azonosító bármikor megváltozhat, érdemes rendszeresen regisztrálni az értesítésekre, hogy elkerülhesse az értesítési hibákat. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran, naponta egynél többször futtatott alkalmazások esetén a sávszélesség megőrzése érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.
> 
> 

1. Adja hozzá a következő kódot az **OnCreate** metódus végéhez a **MainActivity** osztályban:
   
    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```
   
    Ez a kód biztosítja, hogy az alkalmazás minden egyes indításkor lekéri a kategóriákat a helyi tárterületből, és regisztrációt kérelmez ezekre a kategóriákra vonatkozóan. 
2. Ezután frissítse a `onStart()` osztály `MainActivity` metódusát az alábbiak szerint:
   
    ```java
    @Override
    protected void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```
   
    Ez a kód frissíti a fő tevékenységet a korábban mentett kategóriák állapota alapján.

Az alkalmazás ezzel elkészült, és képes egy kategóriakészlet tárolására az eszköz helyi tárterületén, illetve az értesítési központban való regisztrálásra, ha a felhasználó módosítja a választott kategóriákat. A következő lépésben határozzon meg egy háttérrendszert, amely kategóriaértesítéseket küldhet ennek az alkalmazásnak.

## <a name="send-tagged-notifications"></a>Címkézett értesítések küldése
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Az alkalmazás tesztelése
1. Futtassa az alkalmazást az Android-eszközön vagy emulátoron az Android Studióban. Az alkalmazás felhasználói felületén váltógombok segítségével választhatja ki, hogy mely kategóriákra szeretne feliratkozni.
2. Engedélyezzen egy vagy több kategóriát, majd kattintson a **Subscribe** (Feliratkozás) gombra. Az alkalmazás címkékké alakítja át a kiválasztott kategóriákat, és új eszközregisztrációt kezdeményez az értesítési központban a kiválasztott címkékre vonatkozóan. A rendszer visszaadja, és egy bejelentési értesítésben jeleníti meg a regisztrált kategóriákat.

    ![Feliratkozás kategóriákra](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
1. Futtassa a .NET-konzolalkalmazást, amely értesítéseket küld minden egyes kategóriáról. A kijelölt kategóriák értesítései bejelentési értesítésként jelennek meg.

    ![Technológiai hírekről szóló értesítések](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban szórásos értesítéseket küldött ki adott Android-eszközökre, amelyeket regisztráltak a kategóriákra. Ha szeretné megtudni, hogyan küldhet leküldéses értesítéseket adott felhasználóknak, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
