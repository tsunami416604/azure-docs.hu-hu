---
title: "Notification Hubs – legfrissebb hírek oktatóanyag - Android"
description: "Útmutató: Azure Service Bus Notification Hubs használatával legfrissebb híreket tartalmazó értesítések küldése Android-eszközök."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 3b3fc05cfec2b20501a28f3d76f474ccd49e27e8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>A legfrissebb hírek elküldése a Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan szórási legfrissebb híreket tartalmazó értesítések Android-alkalmazás az Azure Notification Hubs használatával. Amikor végzett, akkor fog számára megtörje érdekli hírek kategóriák regisztrálni, és ezen kategóriák csak leküldéses értesítések fogadásához. Ebben a forgatókönyvben számos alkalmazás általános felépítését, ahol értesítések kell őket, például az RSS-olvasóval, az alkalmazások zene ventilátorok stb érdeklődik elemnek már deklarálva felhasználói csoportokat kell küldeni.

Szórási forgatókönyvek engedélyezve vannak, beleértve a következőket egy vagy több *címkék* regisztráció létrehozásakor az értesítési központban. Amikor a rendszer értesítéseket küld egy címkét, akkor a címke regisztrált minden eszköz a értesítést kap. Mivel a címkékkel egyszerűen csak karakterláncok, nem rendelkeznek előre kell építeni. Címkékkel kapcsolatos további információkért tekintse meg [Notification Hubs útválasztási és címke kifejezések](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör a létrehozott alkalmazás épül [Ismerkedés a Notification Hubs][get-started]. Az oktatóanyag elindítása előtt már végrehajtotta [Ismerkedés a Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Kategória kiválasztása hozzáadása az alkalmazáshoz
Az első lépés a felhasználói felületi elemek hozzáadása a meglévő fő tevékenységeket, amelyek lehetővé teszik a felhasználó számára a kategóriák regisztrálni kívánt. A felhasználó által kiválasztott kategóriák tárolódnak az eszközön. Az alkalmazás indításakor a eszközregisztráció címkeként az értesítési központ, a kiválasztott kategóriákra jön létre.

1. Nyissa meg a res/layout/activity_main.xml fájlt, és helyettesítse be a tartalmat a következő:
   
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
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
        </LinearLayout>
2. Nyissa meg a res/values/strings.xml fájlt, és adja hozzá a következő sorokat:
   
        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>
   
    A main_activity.xml grafikus elrendezés most példához hasonló:
   
    ![][A1]
3. Most hozzon létre egy osztályt **értesítések** azonos csomagban található a **MainActivity** osztály.
   
        import java.util.HashSet;
        import java.util.Set;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;
   
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
   
    Ez az osztály a helyi tároló, amely az eszköz rendelkezik fogadásához hírek kategóriáinak tárolására használja. Ezen kategóriák regisztrálásához módszerek is tartalmaz.
4. Az a **MainActivity** osztály, távolítsa el a privát mezőinek **NotificationHub** és **GoogleCloudMessaging**, és a mező felvétele **értesítések**:
   
        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;
5. Ezt követően a a **onCreate** módszer, távolítsa el a inicializálása a **hub** mező és a **registerWithNotificationHubs** metódust. Majd adja hozzá a következő sort, amelyben a példányának inicializálása a **értesítések** osztály. 

        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);

            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`és `HubListenConnectionString` kell már lehet beállítani a `<hub name>` és `<connection string with listen access>` helyőrzőket az értesítési központ nevére és a kapcsolati karakterláncot *DefaultListenSharedAccessSignature* korábban beszerzett.

    > [AZURE.NOTE] Eszközzel együtt egy ügyfélalkalmazás hitelesítő adatok nem általában biztonságos, mert csak kell terjeszteni a figyelési hozzáférési kulcs ügyfél alkalmazása. Figyeljen hozzáférés lehetővé teszi, hogy az alkalmazás regisztrálásához értesítések, de a meglévő regisztrációk nem módosítható, és értesítések nem küldhető el. A teljes körű hozzáférési kulcs értesítések küldését, és meglévő regisztrációk módosítása védett háttérszolgáltatás használatban.


1. Ezt követően adja hozzá az alábbi importálásokat és `subscribe` metódust az előfizetés gombra kattintson esemény:
   
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;
   
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
   
    Ezzel a módszerrel hoz létre a kategóriák és a használja listáját a **értesítések** osztályra, hogy a lista a helyi tároló tárolja, és regisztrálja a megfelelő címkéket az értesítési központban. Kategóriák módosításakor a regisztrációs újra létrejön az új kategóriák.

Az alkalmazás már kategóriák készlete tárolja az eszköz helyi tárolóhoz, és regisztrálhatja az értesítési központban, amikor a felhasználó módosítja a kiválasztott kategóriák.

## <a name="register-for-notifications"></a>Az értesítések regisztrálása
Ezeket a lépéseket az értesítési központ indításakor a helyi tárolóban tárolt kategóriák segítségével regisztrálja.

> [!NOTE]
> A hozzárendelt által Google Cloud Messaging (GCM) registrationId bármikor módosíthatja, mert az értesítések a notification hibák elkerülése érdekében gyakran kell regisztrálni. Ebben a példában regisztrál az értesítési minden alkalommal, az alkalmazás indítása. Gyakran futtatott alkalmazások esetén naponta csak egyszer, valószínűleg kihagyhatja regisztrációt, hogy a sávszélesség megőrzése, ha az előző regisztráció óta eltelt egy napnál.
> 
> 

1. Adja hozzá a következő kódot végén a **onCreate** metódust a **MainActivity** osztály:
   
        notifications.subscribeToCategories(notifications.retrieveCategories());
   
    Ez biztosítja, hogy az alkalmazás minden indításakor azt a kategóriák átmásolja a helyi tároló, és kéri a regisztrálás ezen kategóriák. 
2. Ezután frissítse a `onStart()` metódusában a `MainActivity` osztály az alábbiak szerint:
   
    @Overridevédett "void" onStart() {
   
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
   
    Ekkor frissül, a fő tevékenységnél a korábban mentett kategóriák állapota alapján.

Az alkalmazás most már befejeződött, és képes tárolni a kategóriák az eszköz helyi tárolójára való regisztrálásához az értesítési központnak a felhasználói kategóriák kiválasztott megváltozásakor használt. A következő azt határozza meg, amely ennek az alkalmazásnak kategória értesítéseket küldhet háttérkiszolgálón.

## <a name="sending-tagged-notifications"></a>Címkézett értesítések küldése
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Futtassa az alkalmazást, és értesítések
1. Az Android Studióban az alkalmazás elkészítésére, és indítsa el a egy eszközt vagy emulátort.
   
    Vegye figyelembe, hogy az alkalmazás felhasználói felület számos váltógombok, amely lehetővé teszi, hogy válassza ki a előfizetni.
2. Egy vagy több kategóriák váltógombok engedélyezése, majd kattintson az **előfizetés**.
   
    Az app alakítja át a kiválasztott kategóriákra címkék, és egy új eszköz regisztrálása a kijelölt címkék kéri le az értesítési központban. A regisztrált kategóriák tér vissza, és egy bejelentési értesítés jelenik meg.
3. Egy új értesítés küldése a .NET-Konzolalkalmazás futtatásával.  Másik lehetőségként az értesítési központ hibakeresési lapján címkézett sablon értesítéseket küldhet a [Azure-portálon].
   
    A kiválasztott kategóriákra értesítések bejelentési értesítések jelennek meg.

## <a name="next-steps"></a>Következő lépések
Az oktatóanyag azt megtudta, hogyan kell közvetíteni legfrissebb hírek kategória szerint. Vegye figyelembe az alábbi oktatóanyagok számára más speciális Notification Hubs-forgatókönyvek közül befejezése:

* [Honosított legfrissebb hírek szórási a Notification Hubs használatával]
  
    Megtudhatja, hogyan bontsa ki a legfrissebb hírek app küldő honosított értesítések engedélyezéséhez.

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Honosított legfrissebb hírek szórási a Notification Hubs használatával]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure-portálon]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
