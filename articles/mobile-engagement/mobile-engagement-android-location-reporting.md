---
title: Az Azure Mobile Engagement Android SDK Reporting helye
description: "Az Azure Mobile Engagement Android SDK reporting hely konfigurálása"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Az Azure Mobile Engagement Android SDK Reporting helye
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Ez a témakör ismerteti az Android alkalmazás reporting helye.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Helyi jelentéskészítés
Ha azt szeretné, hogy a helyek jelentendő, kell hozzáadnia a konfigurációs néhány sornyi (között a `<application>` és `</application>` címkék).

### <a name="lazy-area-location-reporting"></a>Helymeghatározást
Helymeghatározást lehetővé teszi, hogy az ország, régió és Helység szerint társított eszközök reporting. A hely jelentéskészítési típusú csak használ a hálózati helyek (cella azonosító vagy Wi-Fi alapján). Az eszköz területen munkamenetenként legfeljebb egyszer jelenti. A rendszer nem használja a GPS, és így ez a típus helye jelentés alacsony hatással van az akkumulátor.

Jelentett területek segítségével számítási felhasználók munkamenetek, események és hibák földrajzi statisztikája. Ezek használhatók a Reach-kampányokat feltételként.

Engedélyezi a korábban az itt ismertetett eljárás konfigurációval reporting Lusta terület helye:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Meg kell adnia a helyre vonatkozó engedély is. Ezt a kódot használja ``COARSE`` engedély:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ha az alkalmazás számára szükséges, akkor használhatja ``ACCESS_FINE_LOCATION`` helyette.

### <a name="real-time-location-reporting"></a>Valós idejű hely jelentéskészítési
Lehetővé teszi, hogy a szélességi és hosszúsági eszközök társított reporting reporting valós idejű helye. Alapértelmezés szerint a hely jelentéskészítési típusú hálózati helyek, cella azonosító vagy Wi-Fi alapú csak használja. A jelentéskészítési akkor csak aktív, ha az alkalmazás futtatása az előtérben (például egy munkamenetben).

Valós idejű helyek *nem* statisztika kiszámítására használt. Az egyetlen célja, hogy a geokerítések valós idejű használatának engedélyezése \<Reach-célközönségre-geokerítések\> Reach-kampányokat a feltételnek.

Ahhoz, hogy a valós idejű hely jelentéskészítési, ahol a bevonási kapcsolati karakterlánc beállítása indítója tevékenységben adja hozzá egy kódsort. Az eredmény a következőképpen néznek:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS-alapú jelentés
Alapértelmezés szerint a valós idejű hely jelentéskészítési csak használja hálózati helyek. A GPS-alapú helyeket, amelyek jobban pontos, használatának engedélyezése a konfigurációs objektum használja:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Szükség vegye fel a következő engedélyt, ha hiányoznak:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Háttér-jelentés
Alapértelmezés szerint valós idejű hely jelentéskészítési akkor csak aktív, ha az alkalmazás futtatása az előtérben (például egy munkamenetben). A jelentések engedélyezéséhez is háttérben a konfigurációs objektum használja:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Amikor az alkalmazás hátterében, csak a hálózati helyek jelentett, még akkor is, ha engedélyezte a GPS.
> 
> 

Ha a felhasználó az eszköz újraindul, a háttér hely jelentés le van állítva. Abba, hogy a rendszer indításakor automatikusan újraindul, adja hozzá ezt a kódot.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Szükség vegye fel a következő engedélyt, ha hiányoznak:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M-engedélyek
Az Android M-től kezdődően egyes engedélyek futásidőben felügyelt, és felhasználói jóváhagyás szükséges.

Ha cél Android API-szintet 23, a futtatókörnyezet engedélyek ki vannak kapcsolva alapértelmezés szerint új alkalmazástelepítések. Ellenkező esetben ezek alapértelmezés szerint vannak kapcsolva.

Akkor is engedélyezi/letiltja ezeket az engedélyeket az eszköz beállítások menüjében. A rendszer menüből engedélyek kikapcsolása használhatatlanná teszi az alkalmazás, amely a rendszer viselkedését, és nincs hatással van a háttérben leküldéses fogadni a háttérfolyamatot.

A Mobile Engagement-hely jelentési keretében a futási időben jóváhagyás szükséges engedélyeket a következők:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Kérjen engedélyt a felhasználó egy szabványos rendszer párbeszédpanelen. Ha a felhasználónak jóvá, mondja el ``EngagementAgent`` figyelembe, hogy módosítsa a valós idejű érvénybe. Ellenkező esetben a módosítás a felhasználó elindítja az alkalmazást a következő alkalommal dolgozza fel.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
