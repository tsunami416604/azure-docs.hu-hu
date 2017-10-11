---
title: "Az Azure Mobile Engagement Android SDK-integráció"
description: "Legújabb frissítések és az Azure Mobile Engagement Android SDK eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1f047f93fa8bc852b28c86e91d0c007a94fb4299
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-procedures"></a>Frissítési eljárások
Ha Ön már rendelkezik integrált egy régebbi verzióját az SDK az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Előfordulhat, hogy kell eljárnia számos eljárást, ha nem fogadta az SDK a különböző verzióiban. Például ha áttelepít 1.4.0 1.6.0 először hajtsa végre a "from a 1.5.0 1.4.0" eljárást kell majd a "from a 1.6.0 1.5.0" eljárást.

Frissít, függetlenül a verziót, hogy cserélje le a `mobile-engagement-VERSION.jar` az újjal.

## <a name="from-420-to-421"></a>A 4.2.0 való 4.2.1.
Ez a lépés ténylegesen végezhető az SDK-t bármely verzióját, hogy a biztonság fokozása Reach tevékenységek integrálásakor.

Most adja hozzá `exported="false"` összes Reach tevékenységben.

Reach tevékenységek most példához hasonló a `AndroidManifest.xml`:

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

## <a name="from-400-to-410"></a>A 4.0.0 4.1.0 számára
Az SDK most leíró új engedély modellben Android m-alapú.

Ha hely használatát, vagy olvassa el nagy vonalakban tekinti értesítések [ebben a szakaszban](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Az új engedély modell mellett mostantól támogatjuk konfigurálása hely szolgáltatások futási időben.
Dolgozunk továbbra is kompatibilis helyhez jegyzék paraméterekkel rendelkező, de most már elavult. Futásidejű konfigurálását használatához távolítsa el a következő szakaszok a ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

olvassa el és [a frissíti, az eljárás](mobile-engagement-android-integrate-engagement.md#location-reporting) használja helyette a futásidejű konfigurálását.

## <a name="from-300-to-400"></a>A 3.0.0 4.0.0 számára
### <a name="native-push"></a>Natív leküldéssel
Natív leküldéses (GCM/ADM) most is szolgál az alkalmazáson belüli értesítések, konfigurálnia kell a hitelesítő adatokat a natív leküldéses kampány bármilyen típusú.

Ha nem már kövesse [ezzel az eljárással](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Reach integrációs módosítottuk ``AndroidManifest.xml``.

Cserélje le ezt:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

–

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

Már létezik valószínűleg betöltése képernyő kattintva (a szöveges vagy webes tartalom) bejelentés vagy szavazásként.
Fel kell vennie ezeket a kampányok 4.0.0 munkát ez:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Erőforrások
Az új beágyazása `res/layout/engagement_loading.xml` fájlt a projektben.

## <a name="from-240-to-300"></a>A 2.4.0 3.0.0 számára
A következő ismerteti, hogyan telepíthetők át az SDK-integráció az alkalmazásba az Azure Mobile Engagement technológiával Capptain SAS által kínált Capptain szolgáltatás. Ha egy korábbi verziójáról telepít, tekintse át a Capptain webhely 2.4.0 először át, és ezután alkalmazza az alábbi eljárást.

> [!IMPORTANT]
> Capptain és a Mobile Engagement nem ugyanazok a szolgáltatások, és az alábbi eljárás csak emel ki, hogyan telepítheti át az ügyfélalkalmazás. Az SDK-t az alkalmazás áttelepítése rendszer nem telepíti át az adatok a Capptain kiszolgálókról a Mobile Engagement-kiszolgálókra.
> 
> 

### <a name="jar-file"></a>JAR-fájlra
Cserélje le `capptain.jar` által `mobile-engagement-VERSION.jar` a a `libs` mappát.

### <a name="resource-files"></a>Erőforrás-fájlok
A Microsoft által biztosított minden erőforrásfájl (előtagjaként `capptain_`) helyébe a újakat kell (előtagként `engagement_`).

Ha testreszabta azokat a fájlokat, rendelkezik-e alkalmazza újra az új fájlok, a testreszabást **az erőforrás-fájlokban szereplő összes azonosító is át lett nevezve**.

### <a name="application-id"></a>Alkalmazásazonosító
Most Engagement használ egy kapcsolati karakterlánc konfigurálásához, az SDK-azonosítókat, például az alkalmazás azonosítója.

Szükség van `EngagementAgent.init` módszer a indítója tevékenységi ehhez hasonló:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A kapcsolati karakterlánc az alkalmazás Azure-portálon jelenik meg.

Távolítsa el az összes hívása `CapptainAgent.configure` , `EngagementAgent.init` Ez a módszer váltja fel.

A `appId` már nem konfigurálható segítségével `AndroidManifest.xml`.

Távolítsa el az ebben a szakaszban a `AndroidManifest.xml` Ha azt:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API
Az SDK minden Java-osztály minden hívás tartalmaz, át kell nevezni; például `CapptainAgent.getInstance(this)` kell átnevezni `EngagementAgent.getInstance(this)`, `extends CapptainActivity` kell átnevezni `extends EngagementActivity` stb...

Alapértelmezett ügynök preferencia fájlok lettek integrálva, ha a fájl alapértelmezett neve mostantól `engagement.agent` és a kulcs `engagement:agent`.

Webes közlemények létrehozásakor a Javascript kötő mostantól `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Hiba történt a sok módosítást, a szolgáltatás már nincs megosztva, és nagy mennyiségű fogadók nem exportálható többé.

A szolgáltatás deklaráció mostantól egyszerűbb; Távolítsa el a leképezési szűrő és az összes metaadatot azon belül, és adja hozzá `exportable=false`.

Továbbá minden engagement használata átnevezi.

Ez most néz ki:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Ha szeretné engedélyezni a vizsgálati naplók, a metaadatok most már át lett helyezve a alkalmazás címkére, és át lett nevezve:

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Minden egyéb metaadatot csak át lett nevezve, a teljes listája (az állomásokon átnevezése csak az megfelelően használata):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play áruházból és nyomon követését SmartAd el lett távolítva SDK csak el kell távolítania a helyettesítő nélkül:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

A Reach-tevékenységek most deklarált ehhez hasonló:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Ha egyéni Reach tevékenységeket, csak módosítani szeretné a leképezési műveletek egyező vagy `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` vagy `com.microsoft.azure.engagement.reach.intent.action.POLL`.

A szórási fogadók át lett nevezve, valamint mostantól felvehet `exported=false`. A fogadók (az állomásokon átnevezése csak az megfelelően használ) az új meghatározásával teljes listája itt található:

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Követés fogadó eltávolították, ezért el kell távolítania az ebben a szakaszban:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Vegye figyelembe, hogy a szórásos receiver megvalósítását deklarációjában **EngagementMessageReceiver** módosult a `AndroidManifest.xml`. Ennek az az oka az API-t küldeni, és távolítsa el az tetszőleges XMPP-üzeneteket tetszőleges XMPP-entitások és az API-t az eszközök közötti üzeneteket küldjön és fogadjon el lettek távolítva. Így is törölnie kell a következő visszahívások a **EngagementMessageReceiver** végrehajtására:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

és

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

Törölje a semmilyen hívásban **EngagementAgent** számára:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

és

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Proguard konfigurációs is negatív hatással lehet rebranding, például a szabályok most hasznosítani:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

