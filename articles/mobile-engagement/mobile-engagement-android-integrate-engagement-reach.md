---
title: Az Azure Mobile Engagement Android SDK-integráció
description: Legújabb frissítések és az Azure Mobile Engagement Android SDK eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Hogyan integrálható az Engagement Reach Android rendszeren
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

> [!IMPORTANT]
> Meg kell követnie a integrációs ismertetett a hogyan integrálhatja Engagement Android dokumentumon Ez az útmutató követése előtt.
> 
> 

## <a name="standard-integration"></a>Standard integráció

Reach erőforrás fájlok másolása a projektben az SDK-ból:

* Fájlok másolása a `res/layout` mappa az SDK-val kézbesíteni a `res/layout` az alkalmazás mappájában.
* Fájlok másolása a `res/drawable` mappa az SDK-val kézbesíteni a `res/drawable` az alkalmazás mappájában.

Szerkessze a `AndroidManifest.xml` fájlt:

* Adja hozzá a következő szakaszt (között a `<application>` és `</application>` címkék):
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* Ezzel az engedéllyel, amely nem rendszerindításkor rendszerértesítéseket visszajátszani van szüksége (más módon lemezen megmarad, de nem fognak többé megjelenni, valóban fel kell vennie a).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Adja meg a másolás, és a következő szakasz szerkesztésével (az alkalmazás és a rendszer megfelelően) értesítésekhez használatos ikon (között a `<application>` és `</application>` címkék):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Ez a szakasz **kötelező** Ha a kíván használni a rendszer értesítéseket Reach-kampányokat létrehozásakor. Android megakadályozza, hogy a rendszer értesítéseket ikonok nélkül látható. Így ebben a szakaszban hagyja el, ha a végfelhasználók pedig nem fogják tudni fogadásukra.
> 
> 

* Kampányok létrehozásakor a rendszer értesítéseket nagy vonalakban tekinti, adja meg a következő engedélyeket kell (után a `</application>` címke) Ha hiányoznak:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Az Android m-alapú, és ha az alkalmazás célozza 23 vagy újabb, Android API-szintet ``WRITE_EXTERNAL_STORAGE`` engedély a felhasználó jóváhagyást igényel. Kérjük, olvassa el [ebben a szakaszban](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* A rendszer értesítések is megadható a Reach-kampány Ha az eszköz fel kell gyűrű és/vagy mozog. Ahhoz, hogy a működik, győződjön meg arról, hogy deklarálva a következő engedély rendelkezik (után a `</application>` címke):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Ez az engedély nélkül Android megakadályozza, hogy a rendszer értesítéseket nem látható, ha bejelölte a gyűrű vagy a vibrate beállítás a Reach-kampány-kezelőben.

## <a name="native-push"></a>Natív leküldés
Most, hogy konfigurálta a Reach-modul, fogadhat az eszközön a kampányok natív leküldéssel konfigurálni kell.

Az Android két szolgáltatás támogatott:

* Google Play-eszközök: használata [Google Cloud Messaging] követve a [GCM integrálása Engagement lépéseihez hogyan](mobile-engagement-android-gcm-integrate.md) útmutató.
* Amazon eszközök: használata [Amazon Device Messaging] követve a [hogyan integrálhatja ADM Engagement lépéseihez](mobile-engagement-android-adm-integrate.md) útmutató.

Ha azt szeretné, hogy a szabályzat Amazon és a Google Play-eszközök esetén a lehetséges, hogy fejlesztési 1 AndroidManifest.xml/APK tartalmát. De Amazon elküldésekor azokat lehet, hogy elutasítja az alkalmazás, ha találnak GCM-kódot.

Több APKs ebben az esetben kell használnia.

**Az alkalmazás fogadni ki és jelenítheti meg a reach-kampányokat készen áll!**

## <a name="how-to-handle-data-push"></a>Az adatleküldés kezelése
### <a name="integration"></a>Integráció
Ha azt szeretné, hogy az alkalmazás fogadhat Reach adatleküldések, kell létrehoznia alosztályának `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` és a vele hivatkozzon a `AndroidManifest.xml` fájl (között a `<application>` és/vagy `</application>` címkék):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Akkor lehet felülbírálni a `onDataPushStringReceived` és `onDataPushBase64Received` visszahívások. Például:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Kategória
A kategória paraméter nem kötelező, amikor adatokat leküldéses kampány létrehozása, és lehetővé teszi, hogy az adatok szűrése leküldéses értesítések. Ez akkor hasznos, ha több szórási fogadók kezelését a különböző típusú adatleküldések, vagy ha azt szeretné, hogy különböző leküldéses a `Base64` adatokat, és azonosítja a őket elemzése előtt szeretné.

### <a name="callbacks-return-parameter"></a>A visszahívások visszatérési paraméter
Az alábbiakban néhány irányelvek megfelelően kezelje a paraméternek `onDataPushStringReceived` és `onDataPushBase64Received`:

* Visszaadja-e a szórásos receiver `null` a visszahívásban, ha nem tudja, hogyan legyen kezelve az adatokat. A kategória segítségével kell meghatározni, hogy a szórásos receiver kezelje az adatleküldés, vagy nem.
* A szórásos receiver valamelyikét kell visszaadnia `true` a visszahívásban, ha az adatok leküldéses fogad.
* A szórásos receiver valamelyikét kell visszaadnia `false` a visszahívásban, ha az adatok leküldéses felismeri, de bármilyen oknál fogva figyelmen kívül hagyja. Például vissza `false` érvénytelen fogadott adatok esetén.
* Ha egy szórási fogadó beolvasása `true` során egy másik egyikét adja vissza `false` azonos adatleküldés viselkedését nincs definiálva, akkor soha ne tegye, hogy.

A visszatérési típusa csak a Reach-statisztikák esetén használja:

* `Replied` értéke akkor növekszik, ha a szóró fogadók egyik adott vissza, vagy `true` vagy `false`.
* `Actioned` értéke akkor nő, csak akkor, ha a szóró fogadók egyik adott vissza `true`.

## <a name="how-to-customize-campaigns"></a>A kampányok testreszabása
Kampányok testreszabásához módosíthatja az elrendezés a Reach SDK-ban megadva.

Kell tartani az elrendezés szerepel az összes azonosítónak és a nézetek különböző típusainak áttekintése az azonosítót, különösen a lemezkép és szöveges nézeteket használó tartsa. Egyes nézetek csak segítségével elrejtése vagy megjelenítése területek, ezért a típus módosítható. Ha a megadott elrendezés nézet típusának módosítása, ellenőrizze a forráskód.

### <a name="notifications"></a>Értesítések
Az értesítések két típusa van: az elrendezés fájlokat használó rendszer és az alkalmazáson belüli értesítések.

#### <a name="system-notifications"></a>Rendszer-értesítések
Rendszer értesítések kell használnia testreszabása a **kategóriák**. Akkor is ugorhat [kategóriák](#categories).

#### <a name="in-app-notifications"></a>Alkalmazáson belüli értesítések
Alapértelmezés szerint egy alkalmazásbeli értesítés az aktuális tevékenység felhasználói felület környezetnek köszönhetően az Android metódus dinamikusan hozzáadott nézet `addContentView()`. Ez a lehetőség egy értesítési területre. Értesítési átfedések nincsenek rendkívül gyors integrációs, mert nincs szükségük az alkalmazás minden elrendezés módosíthatja.

Az értesítési átfedések megjelenésének módosításához egyszerűen módosíthatja a fájlt `engagement_notification_area.xml` az igényeinek megfelelően.

> [!NOTE]
> A fájl `engagement_notification_overlay.xml` van segítségével létrehozhat egy értesítési területre, amelyik tartalmazza a fájl `engagement_notification_area.xml`. Is testreszabhatja úgy, hogy a igényeinek megfelelően (például esetében az értesítési területen belül az átmeneti területre elhelyezéséhez).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Egy tevékenység elrendezés részeként értesítési elrendezés tartalmazza
Átfedések ideális az egy gyors integrációt, de lehet kényelmetlen vagy mellékhatásokkal bizonyos esetekben. Az átmeneti területre rendszer testre szabható tevékenység szinten, így könnyen hatásai különleges tevékenységek megelőzése érdekében.

Az értesítési elrendezés szerepeljenek a meglévő elrendezés környezetnek köszönhetően az Android dönthet úgy is **tartalmaznak** utasítást. Az alábbiakban egy példa egy módosított `ListActivity` elrendezést tartalmazó csak egy `ListView`.

**Mielőtt Engagement-integráció:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Miután Engagement-integráció:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Ebben a példában hozzáadott egy szülőtároló, mert az eredeti elrendezést a listanézet használja, mint a legfelső szintű elem. Is hozzáadtunk `android:layout_weight="1"` szeretne hozzáadni egy listanézeti konfigurált alatt nézetet `android:layout_height="fill_parent"`.

Az Engagement Reach SDK automatikusan észleli, hogy az értesítési elrendezés ezt a tevékenységet tartalmazza, és nem adja hozzá egy átmeneti területre, ehhez a tevékenységhez.

> [!TIP]
> Az alkalmazás egy ListActivity használja, ha egy látható Reach területre nem teszik lehetővé reagál a többé kattintott a lista nézetben. Ez az egy ismert probléma. Ez a probléma javasoljuk, hogy az értesítési elrendezés beágyazása a saját lista tevékenység elrendezés, például az előző minta.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>/ Tevékenység alkalmazás értesítés letiltása
Ha nem szeretné, hogy az átmeneti területre, fel kell venni a tevékenységeket, és ha nem adja meg az értesítési elrendezés saját elrendezésben, letilthatja a felirat a tevékenységnek a `AndroidManifest.xml` hozzáadásával egy `meta-data` szakaszban, például a következő példa:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> Kategóriák
Ha módosítja a megadott elrendezések, az értesítések megjelenésének módosítása. Kategóriák lehetővé teszik a különböző megcélzott keres (valószínűleg viselkedések) megadása az értesítések. A kategória a Reach-kampány létrehozásakor adható meg. Ne feledje, hogy kategóriák is segítségével testre szabható mutató hirdetmények és szavazások, a dokumentum későbbi szakaszában ismertetett.

Az értesítések kategória leíró regisztrálásához kell hozzáadnia a hívás az alkalmazás inicializálásakor.

> [!IMPORTANT]
> Olvassa el a figyelmeztetést jelenít meg az android: folyamat attribútum \<android-sdk-engagement-folyamat\> a bevonási integrálja a folytatás előtt Android témakör című.
> 
> 

Az alábbi példa azt feltételezi, hogy Ön vonatkozik, az előző figyelmeztetés, és alosztályának `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

A `MyNotifier` objektum az értesítéskezelő kategória végrehajtását. Vagy megvalósítása a `EngagementNotifier` illesztőfelület vagy alapértelmezett megvalósított sub osztály: `EngagementDefaultNotifier`.

Vegye figyelembe, hogy az azonos bejelentő kezelni tud a különböző kategóriákba, ilyen regisztrálhatja:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Alapértelmezett kategória végrehajtása cseréjéhez például a megvalósítás regisztrálhatja a következő példa:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

A kezelő szerepel az aktuális kategória felülbírálhatja a legtöbb módszer paraméterként átadott `EngagementDefaultNotifier`.

Átadva akár egy `String` paraméter vagy közvetetten egy `EngagementReachContent` objektum, amely rendelkezik egy `getCategory()` metódus.

Az értesítés létrehozásának folyamatát a legtöbb módosíthatja a módszerek újradefiniálás `EngagementDefaultNotifier`, a Speciális testreszabás nyugodtan tekintse meg a műszaki dokumentáció és a forráskódot.

##### <a name="in-app-notifications"></a>Alkalmazáson belüli értesítések
Ha csak használni kívánt másodlagos elrendezés az egy adott konkrét kategóriával, valósíthatja meg ezt az alábbi példában látható módon:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Példa `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Ahogy látja, átfedő nézet azonosító eltérnek a szabványos. Fontos, hogy minden elrendezés átfedések egy egyedi azonosítót használjon.

**Példa `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Ahogy látja, az értesítési területen nézet azonosító eltérnek a szabványos. Fontos, hogy minden elrendezés egy egyedi azonosítót használ, az értesítési terület.

Ez a kategória egyszerű példa teszi alkalmazás (vagy alkalmazásbeli) értesítés jelenik meg a képernyő tetején. Azt az értesítési területen, maga a szabványos azonosító nem változott.

Ha szeretné módosítani, amely, kell-e újra a `EngagementDefaultNotifier.prepareInAppArea` metódust. A műszaki dokumentáció és annak forráskódját kereséséhez javasoljuk `EngagementNotifier` és `EngagementDefaultNotifier` Ha azt szeretné, a szint speciális.

##### <a name="system-notifications"></a>Rendszer-értesítések
Kiterjesztésével `EngagementDefaultNotifier`, ha szeretné felülbírálni az `onNotificationPrepared` módosítására, az alapértelmezett megvalósítása előkészített értesítés.

Példa:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Ebben a példában a "folyamatban" kategóriát használatakor a folyamatban lévő esemény jeleníti meg tartalom rendszerértesítőként teszi.

Ha a használni kívánt a `Notification` objektum teljesen új, lépjen vissza `false` a metódus és a hívás `notify` a saját kezűleg a `NotificationManager`. Ebben az esetben fontos, hogy maradjon egy `contentIntent`, egy `deleteIntent` , illetve a által használt értesítési azonosító `EngagementReachReceiver`.

Íme egy végrehajtás megfelelő példát:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Csak értesítést tartalmazó hirdetmények
A csak közlemény testre szabható felülbírálásával értesítést kattintson a felügyeleti `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` módosításához az előkészített `Intent`. Ezzel a módszerrel teszi lehetővé a jelzők könnyen hangolására.

Adja hozzá a például a `SINGLE_TOP` jelző:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

A hagyományos Engagement felhasználók számára vegye figyelembe, hogy rendszerüzenetek nélkül művelet URL-CÍMÉT most elindítja az alkalmazást ha háttérben, így ez a metódus meghívható a bejelentés nélkül műveleti URL-cím. Vegye figyelembe, hogy a leképezés testreszabásakor.

Is megvalósíthatja `EngagementNotifier.executeNotifAnnouncementAction` teljesen.

##### <a name="notification-life-cycle"></a>Értesítési életciklusa
Az alapértelmezett kategóriát használatakor egyes életciklus metódusok felhívta a `EngagementReachInteractiveContent` jelentés statisztikákhoz objektumra, és frissíti a kampány állapotát:

* Ha az értesítés jelenik meg az alkalmazás vagy az állapotsor be a `displayNotification` módszer neve (amely statisztikáit) által `EngagementReachAgent` Ha `handleNotification` adja vissza `true`.
* Az értesítés meg van nyitva, ha a `exitNotification` metódus lehívásra kerül, statisztika jelez, és tovább kampányok most már tudja feldolgozni.
* Ha az értesítési kattint, `actionNotification` van nevű statisztika jelez, és a kapcsolódó leképezés lett elindítva.

Ha a végrehajtásának `EngagementNotifier` figyelmen kívül hagyja az alapértelmezett viselkedés önállóan életciklus módszerekhez hívja. A következő példák bemutatják az egyes esetekben, ahol az alapértelmezett viselkedés elmarad:

* Nem terjeszti ki `EngagementDefaultNotifier`, pl. megvalósította a teljesen új kategória kezelését.
* A rendszer értesítések overrode a `onNotificationPrepared` és módosított `contentIntent` vagy `deleteIntent` a a `Notification` objektum.
* Az alkalmazásbeli értesítések overrode `prepareInAppArea`, ügyeljen arra, hogy legalább leképezése `actionNotification` egy a U.I szabályozza.

> [!NOTE]
> Ha `handleNotification` jelez kivételt, a tartalom törlése és `dropContent` nevezik. Ez a statisztika jelez, és tovább kampányok most már tudja feldolgozni.
> 
> 

### <a name="announcements-and-polls"></a>Hirdetmények és szavazások esetén
#### <a name="layouts"></a>Elrendezés
Módosíthatja a `engagement_text_announcement.xml`, `engagement_web_announcement.xml` és `engagement_poll.xml` fájlok szöveg hirdetmények, webes mutató hirdetmények és szavazások testreszabásához.

Ezeket a fájlokat megosztani a címterület és a gomb terület két közös elrendezés. A cím az elrendezés `engagement_content_title.xml` és a háttér városról rajzolható fájlt használja. Az elrendezés a akciógombok és kilépési gombok `engagement_button_bar.xml` és a háttér városról rajzolható fájlt használja.

A szavazás, a kérdés elrendezés és azok lehetőségei vannak dinamikusan növelve használatával több alkalommal a `engagement_question.xml` elrendezésfájlt a kérdés és a `engagement_choice.xml` választási fájlt.

#### <a name="categories"></a>Kategóriák
##### <a name="alternate-layouts"></a>Alternatív elrendezések
Értesítések, például a kampány kategória használható kell rendelkeznie a hirdetmények és szavazások esetén a másik elrendezés.

Például hozzon létre egy szöveges közlemény kategóriát, kiterjesztheti `EngagementTextAnnouncementActivity` és hivatkozzon a `AndroidManifest.xml` fájlt:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Vegye figyelembe, hogy a a leképezést kategóriája szűrésére szolgál a különbség a alapértelmezett közlemény tevékenységet.

A Reach SDK oldja meg a megfelelő tevékenységet egy adott konkrét kategóriával a leképezési rendszert használja, és akkor visszaáll az alapértelmezett kategória, ha a feloldás sikertelen volt.

Akkor el kell megvalósítani `MyCustomTextAnnouncementActivity`, ha szeretné módosítani a elrendezés (de ne azonos nézet azonosítók), csak akkor adja meg az osztályt, például az alábbi példában:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Cserélje le a szöveg közlemények alapértelmezett kategóriáját, egyszerűen cserélje le `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` a megvalósítás.

Webes mutató hirdetmények és szavazások hasonló módon szabhatja testre.

Webes hirdetmények kiterjesztheti `EngagementWebAnnouncementActivity` és a tevékenységeket a `AndroidManifest.xml` , például a következő példa:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

A lekérdezések kiterjesztheti `EngagementPollActivity` majd deklarálja a a a `AndroidManifest.xml` , például a következő példa:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Teljesen új végrehajtása
Megvalósíthat kategóriák a közlemény (és a lekérdezési) tevékenységek nélkül egyik kiterjesztése a `Engagement*Activity` osztályok-a Reach SDK által biztosított. Ez akkor hasznos, például ha kíván-e megadni, amely nem azonos a nézetek használja, mint a szabványos elrendezések elrendezést.

Például a speciális értesítési testreszabáshoz, javasoljuk, hogy tekintse meg a szabványos megvalósítástól forráskódját.

Ne feledje, hogy a következőkkel: Reach elindítja a tevékenység egy adott biztonsági mentés (a leképezési szűrő megfelelő) és egy extra paramétert, amely a tartalom azonosítója.

A tartalom objektum, a webhely a kampány létrehozásakor megadott mezőket tartalmazó beolvasása ehhez:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

A statisztika, jelentse a tartalom jelenik meg a `onResume` esemény:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Majd, ne feledje hívás vagy `actionContent(this)` vagy `exitContent(this)` a tartalom objektum háttér hiányzóra változik, a tevékenység előtt.

Ha nem hívható meg vagy `actionContent` vagy `exitContent`, statisztika nem küldhető el (azaz a kampány nem elemzés) és a fontosabb, a következő kampányok nem kapnak értesítést az alkalmazás folyamatának újraindításáig.

Tájolás vagy más konfigurációs módosításokat végezheti a kód legbonyolultabb annak meghatározásához, hogy a tevékenység kerülnek háttér-e, a szabványos megvalósítástól biztosítja, hogy a tartalmat, ha a felhasználó elhagyja a tevékenység kilépett jelentett (vagy nyomja le az `HOME`vagy `BACK`) kivéve, ha a tájolás változik.

Itt része az érdekes végrehajtására:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Ahogy látja, ha a hívott `actionContent(this)` a tevékenység befejeződött, majd `exitContent(this)` anélkül, hogy bármilyen hatása biztonságosan hívható.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
