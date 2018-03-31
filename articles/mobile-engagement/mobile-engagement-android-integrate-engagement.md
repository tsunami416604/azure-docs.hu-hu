---
title: Az Azure Mobile Engagement Android SDK-integráció
description: Legújabb frissítések és az Azure Mobile Engagement Android SDK eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Hogyan integrálható az Engagement Android rendszeren
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Ez az eljárás ismerteti a legegyszerűbben úgy Engagement elemzés és Monitorozási funkciók az Android alkalmazás aktiválása.

> [!IMPORTANT]
> A minimális Android SDK API-szintet kell lennie, 10 vagy újabb rendszer (Android 2.3.3 vagy újabb).
> 
> 

Az alábbi lépéseket kell elegendő a aktiválja a jelentés az összes statisztikai adatok felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a Technicals kiszámításához szükséges naplók. A jelentés más hasonló eseményeket, hibákat és feladatok statisztika kiszámításához szükséges naplók az Engagement API segítségével manuálisan hajtható végre (lásd: [használata a speciális a Mobile Engagement az Android API szerinti címkézését](mobile-engagement-android-use-engagement-api.md) mivel ezek a statisztikák a függő alkalmazást.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Az Engagement SDK-t és a szolgáltatás beágyazása az Android-projekt
Az Android SDK letöltése [Itt](https://aka.ms/vq9mfn) beolvasása `mobile-engagement-VERSION.jar` , és be azokat a `libs` az Android-projekt mappájából (hozza létre a függvénytárak mappát, ha még nem létezik).

> [!IMPORTANT]
> Ha a ProGuard alkalmazáscsomag, szeretne rögzíteni bizonyos osztályokat. A következő konfigurációs részlet használhatja:
> 
> -nyilvános osztály tartsa * android.os.IInterface bővíti-osztály com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {megtartása
> 
> <methods>; }
> 
> 

Adja meg a bevonási kapcsolati karakterlánc a következő metódus meghívásával indítója tevékenységben:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás Azure-portálon jelenik meg.

* Ha hiányoznak, adja meg a következő Android engedélyeket (előtt a `<application>` címke):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Adja hozzá a következő szakaszt (között a `<application>` és `</application>` címkék):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Változás `<Your application name>` az alkalmazás nevével.

> [!TIP]
> A `android:label` attribútum lehetővé teszi, hogy a szolgáltatás neve a bevonási kiválaszthatja azt a telefont "Szolgáltatásokat futtató" képernyőjén a végfelhasználók számára megjelenik. Javasoljuk, hogy ez az attribútum beállítása `"<Your application name>Service"` (pl. `"AcmeFunGameService"`).
> 
> 

Adja meg a `android:process` attribútum biztosítja, hogy a bevonási szolgáltatás a saját fájlleírókból (Engagement ugyanabban a folyamatban, az alkalmazás biztosítják a fő/felhasználói felület szálán potenciálisan kevésbé rugalmas) fog futni.

> [!NOTE]
> A kód helyez `Application.onCreate()` és más alkalmazás visszahívások fog futni az alkalmazás folyamatok, többek között a bevonási szolgáltatás. (Például nem szükséges memória-kiosztásokat és a bevonási folyamat, ismétlődő szórási fogadók vagy szolgáltatások) nemkívánatos hatásai lehetnek.
> 
> 

Felülbírálásakor `Application.onCreate()`, a következő kódrészletet hozzáadása elején javasoljuk a `Application.onCreate()` függvény:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Ugyanezt megteheti `Application.onTerminate()`, `Application.onLowMemory()` és `Application.onConfigurationChanged(...)`.

Emellett kibővítheti `EngagementApplication` helyett kiterjesztése `Application`: a visszahívás `Application.onCreate()` a folyamat ellenőrzés és hívások `Application.onApplicationProcessCreate()` csak ha az aktuális folyamat nem azt az Engagement szolgáltatást tartalmazó, ugyanazok a szabályok vonatkoznak a más visszahívások.

## <a name="basic-reporting"></a>Alapvető jelentéskészítési
### <a name="recommended-method-overload-your-activity-classes"></a>Ajánlott módszer: túlterhelés a `Activity` osztályok
Ahhoz, hogy aktiválja a jelentés minden, a felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a műszaki statisztika számítási Engagement által igényelt naplók, akkor csak kell végeznie az összes a `*Activity` alosztályokat öröklik a megfelelő `Engagement*Activity` (pl. osztályok Ha kiterjeszti az örökölt tevékenység `ListActivity`, ellenőrizze az általa bővített `EngagementListActivity`).

**Nélkül Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Az Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Használata esetén `EngagementListActivity` vagy `EngagementExpandableListActivity`, győződjön meg arról, hogy semmilyen hívásban való `requestWindowFeature(...);` hívása előtt történik `super.onCreate(...);`, ellenkező esetben a összeomlási történik.
> 
> 

Ezeket az osztályokat is megtalálhatja a `src` mappa, és a projektben másolja őket. Az osztályok szerepelnek a **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Másodlagos módszer: hívja `startActivity()` és `endActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `Activity` osztályok, ehelyett elindításához és a tevékenységek befejezése meghívásával `EngagementAgent`tartozó módszerek közvetlenül.

> [!IMPORTANT]
> Az Android SDK soha nem hívja a `endActivity()` metódust, akkor is, ha az alkalmazás bezárása (Android, az alkalmazások valójában soha nem Lezáródnak). Tehát, *magas* hívására ajánlott a `startActivity()` metódust a `onResume` a visszahívási *összes* a tevékenységeket, és a `endActivity()` metódus a a `onPause()` a visszahívási *összes* a tevékenységek. Ez az az egyetlen lehetőség, hogy a munkamenetek szivárgását nem lehet. Munkamenet kiszivárgott, ha a bevonási szolgáltatás soha nem leválasztja az Engagement háttérrendszeréhez a (mivel a szolgáltatás továbbra is csatlakoztatva marad mindaddig, amíg függőben egy munkamenet).
> 
> 

Például:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

Ez a Példa nagyon hasonlít a `EngagementActivity` osztály és annak változataihoz, amelynek forráskódban szerepel a `src` mappát.

## <a name="test"></a>Tesztelés
Most ellenőrizze, hogy az integráció a mobilalkalmazás emulátor vagy eszköz a és ellenőrzése, hogy a munkamenet a Monitor lapon történő regisztrálást.

A következő szakaszokban opcionálisak.

## <a name="location-reporting"></a>Helyi jelentéskészítés
Ha azt szeretné, hogy a helyek jelentendő, kell hozzáadnia a konfigurációs néhány sornyi (között a `<application>` és `</application>` címkék).

### <a name="lazy-area-location-reporting"></a>Helymeghatározást
Helymeghatározást lehetővé teszi, hogy az ország, valamint régió és Helység szerint társított eszközök jelentését. A hely jelentéskészítési típusú csak használ a hálózati helyek (cella azonosító vagy Wi-Fi alapján). Az eszköz területen munkamenetenként legfeljebb egyszer jelenti. A rendszer nem használja a GPS, és ezáltal helye a jelentés az ilyen típusú (nem a fel nem) nagyon kevés az akkumulátor hatással.

Jelentett területek segítségével számítási felhasználók, munkamenetek, kapcsolódó események és hibák földrajzi statisztikája. Ezek használhatók a Reach-kampányokat feltételként.

Helymeghatározást engedélyezéséhez ezt megteheti a korábban az itt ismertetett eljárás konfigurációval:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Szükség vegye fel a következő engedélyt, ha hiányoznak:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Tovább használhatja vagy ``ACCESS_FINE_LOCATION`` Ha már használja az alkalmazásban.

### <a name="real-time-location-reporting"></a>Valós idejű hely jelentéskészítési
Valós idejű hely jelentéskészítési lehetővé teszi, hogy a szélességi és hosszúsági társított eszközök jelentését. Alapértelmezés szerint a hely jelentéskészítési csak használja a hálózati helyek (cella azonosító vagy Wi-Fi alapján), és a jelentéskészítési csak aktív futtatásakor az alkalmazás az előtérben (azaz egy munkamenetben).

Valós idejű helyek *nem* statisztika kiszámítására használt. Az egyetlen célja, hogy a geokerítések valós idejű használatának engedélyezése \<Reach-célközönségre-geokerítések\> Reach-kampányokat a feltételnek.

Ahhoz, hogy a reporting valós idejű helyét, ezt megteheti a korábban az itt ismertetett eljárás konfigurációval:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Szükség vegye fel a következő engedélyt, ha hiányoznak:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Tovább használhatja vagy ``ACCESS_FINE_LOCATION`` Ha már használja az alkalmazásban.

#### <a name="gps-based-reporting"></a>GPS-alapú jelentés
Alapértelmezés szerint valós idejű helyét jelentő csak funkció alapú hálózati helyek. Alapú GPS-helyek (amelyek pontos sokkal) használatának engedélyezése a konfigurációs objektum használja:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Szükség vegye fel a következő engedélyt, ha hiányoznak:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Háttér-jelentés
Alapértelmezés szerint valós idejű hely jelentéskészítési akkor csak aktív, ha az alkalmazás futtatása az előtérben (azaz egy munkamenetben). A jelentések engedélyezéséhez is háttérben a konfigurációs objektum használja:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Az alkalmazás futtatásakor a háttérben, csak a hálózati helyek alapú jelenti, még akkor is, ha engedélyezte a GPS.
> 
> 

A háttérben hely jelentés le lesz állítva, ha a felhasználó az eszköz újraindul, felvétele abba, hogy a rendszer indításakor automatikusan újraindul:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Szükség vegye fel a következő engedélyt, ha hiányoznak:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M-engedélyek
Az Android M-től kezdődően egyes engedélyek futásidőben felügyelt, és felhasználói jóváhagyás szükséges.

A futásidejű engedélyek ki lesz kapcsolva új alkalmazástelepítések alapértelmezés szerint ha Android API-szintet 23 célozhat meg. Ellenkező esetben azt lesz kapcsolva alapértelmezés szerint.

A felhasználó is engedélyezi/letiltja ezeket az engedélyeket az eszköz beállítások menüjében. Engedélyek rendszer menüből kikapcsolása használhatatlanná teszi az alkalmazás háttérfolyamatot, ez a rendszer viselkedését, és nincs hatással van a háttérben leküldéses fogadni.

A Mobile Engagement keretében a futási időben jóváhagyás szükséges engedélyeket a következők:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (csak akkor, ha ez egy 23 Android API-szintet célzó)

A külső tárolóeszközről csak Reach nagy vonalakban tekinti a szolgáltatás használatos. Ha talál kérni a felhasználókat, amely zavart, eltávolíthatja, ha azt csak a Mobile Engagement de nagy vonalakban tekinti funkció letiltása.

Hely szolgáltatásainak igényeljen egy szabványos rendszer párbeszédpanelen felhasználói jogosultsága. Ha a felhasználónak jóvá, kell-e adnia ``EngagementAgent`` figyelembe, hogy módosítás életbe valós időben (ellenkező esetben a módosítás dolgozza fel a rendszer a következő alkalommal a felhasználó elindítja az alkalmazást).

Ez a kódminta használata egy tevékenységben, az alkalmazás engedélyek kéréséhez és továbbítsa az eredmény, ha pozitív ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
Szükség esetén szeretne jelentést készíteni az adott alkalmazásesemények, a hibák és a feladatok, ha szeretné használni az Engagement API-t a a módszerrel a `EngagementAgent` osztály. Ez az osztály egy objektumának lehet retreived meghívásával a `EngagementAgent.getInstance()` statikus metódus.

A bevonási API lehetővé teszi, hogy Engagement speciális funkciók használatára, és az útmutató részletesen az Engagement API-t használni az Android (valamint a műszaki dokumentációját az a `EngagementAgent` osztály).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Speciális konfigurációs (az AndroidManifest.xml)
### <a name="wake-locks"></a>Ébresztési zárolások feloldása
Ha azt szeretné, és ellenőrizze, hogy statisztika küldött Wi-Fi használata esetén valós időben, vagy le van tiltva a képernyő, adja meg a következő opcionális engedély:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Összeomlási jelentést
Ha le szeretné tiltani a összeomlási jelentések, adja hozzá ezt a (között a `<application>` és `</application>` címkék):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Küszöbérték-kapacitásnövelés
Alapértelmezés szerint a bevonási service-jelentéseken naplók valós időben. Ha az alkalmazás naplók nagyon gyakran, érdemes meghajtóin a naplókat, és jelentést őket egyszerre egy rendszeres időközönként alap (Ez a "kapacitásnövelés mód" nevezzük). Ehhez adja hozzá ezt a (között a `<application>` és `</application>` címkék):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

A kapacitásnövelés módja kissé az eszközakkumulátor élettartamának növelhető, de hatással van a bevonási figyelő: minden munkamenetek és feladatok időtartama a rendszer kerekíti a kapacitásnövelés küszöbértéket (így munkamenetek és feladatok rövidebb, mint a kapacitásnövelés küszöbértéke nem lehet látható). Javasoljuk, hogy egy mint 30000 (30s) kapacitásnövelés küszöbértékkel.

### <a name="session-timeout"></a>Munkamenet időkorlátja
Alapértelmezés szerint a munkamenet befejeződött a 10 egység az utolsó tevékenység (amely általában akkor fordul elő, a Home vagy biztonsági billentyű lenyomásával, úgy, hogy a telefon üresjárati vagy egy másik alkalmazásban átugorja) vége után. Ez a munkamenet valószínűségét minden egyes alkalommal a felhasználó kilépési, és térjen vissza az alkalmazás nagyon gyorsan (amely a hiba akkor fordulhat elő ő átvételéhez egy kép, amikor ellenőrizheti egy értesítés, stb.) elkerülése érdekében. Érdemes lehet módosíthatják ezt a paramétert. Ehhez adja hozzá ezt a (között a `<application>` és `</application>` címkék):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Naplózási jelentések letiltásához
### <a name="using-a-method-call"></a>Metódushívások használatával
Ha azt szeretné, hogy leállítja a naplók küldése Engagement, hívása:

            EngagementAgent.getInstance(context).setEnabled(false);

Ez a hívás az állandó: egy megosztott beállításokat tartalmazó fájlt használja.

Ha Engagement aktív Ez a függvény hívásakor, 1 perc, a szolgáltatás leállítása eltarthat. Azonban ez nem indítsa el a szolgáltatás minden legközelebb indítani az alkalmazást.

Engedélyezheti a reporting újra működik meghívásával napló `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integráció saját `PreferenceActivity`
Helyett ezt a funkciót, integrálhatja ezt a beállítást a közvetlenül a meglévő `PreferenceActivity`.

Konfigurálhatja a bevonási a beállításokat tartalmazó fájlt (a kívánt mód) a használatára a `AndroidManifest.xml` fájlt `application meta-data`:

* A `engagement:agent:settings:name` kulcs használatával a közös beállításokat fájl nevének meghatározásához.
* A `engagement:agent:settings:mode` kulcs használatával a közös beállításokat fájl módját határozza meg, mint a azonos módot kell használnia a `PreferenceActivity`. Egy számot a módot kell átadni: használatakor a konstans jelzők kombinációja a kódban, ellenőrizze a teljes értékét.

Bevonási mindig használja a `engagement:key` logikai kulcs ezzel a beállítással kezelésére szolgáló beállítások fájlon belül.

A következő példa `AndroidManifest.xml` megjeleníti az alapértelmezett értékeket:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Ezután hozzáadhatja a `CheckBoxPreference` a preferencia elrendezésben hasonló a következő:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
