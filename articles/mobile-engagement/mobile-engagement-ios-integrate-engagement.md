---
title: Az Azure Mobile Engagement iOS SDK-integráció |} Microsoft Docs
description: Legújabb frissítések és az Azure Mobile Engagement SDK iOS eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: f0d650205ca86205ad857003f892c1a60b3831bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-ios"></a>Hogyan integrálhatja Engagement IOS
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

Ez az eljárás ismerteti a legegyszerűbben úgy Engagement elemzés és Monitorozási funkciók az iOS-alkalmazás aktiválása.

Az Engagement SDK iOS7 + és Xcode 8 + igényel: az alkalmazás központi telepítés célját kell lennie legalább iOS 7-es.

> [!NOTE]
> Ha valóban függenek XCode 7, akkor előfordulhat, hogy használja a [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Nincs egy ismert hiba az előző verzió a Reach-modul iOS 10 eszközök lásd futtatott [a reach-modul integrációs](mobile-engagement-ios-integrate-engagement-reach.md) további részleteket. Ha úgy dönt, hogy az SDK v3.2.4 használja, akkor csak hagyja ki a `UserNotifications.framework` importálja a következő lépésben.
>
>

Az alábbi lépéseket kell aktiválni a jelentés az összes statisztikai adatok felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a Technicals kiszámításához szükséges naplók elegendő. A jelentés más hasonló eseményeket, hibákat és feladatok statisztika kiszámításához szükséges naplók az Engagement API segítségével manuálisan hajtható végre (lásd: [a speciális a Mobile Engagement az iOS-alkalmazás szerinti címkézését API használata](mobile-engagement-ios-use-engagement-api.md) mivel ezek a statisztikák a függő alkalmazást.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Az Engagement SDK beágyazása az iOS-projekthez
* Az iOS SDK letöltése a [Itt](http://aka.ms/qk2rnj).
* Az iOS-projekthez adja hozzá az Engagement SDK: az Xcode-ban kattintson a jobb gombbal a projekt, és válassza ki **"A fájlok hozzáadása..."** , és válassza a `EngagementSDK` mappát.
* Bevonási további keretrendszerek működéséhez szükséges: a project explorer nyissa meg a projekt ablaktáblán, és jelölje ki a megfelelő cél. Ezután nyissa meg a **"Összeállítási fázisok"** lapon és a a **"Bináris rendelkező Kódtárakon"** menüben adja hozzá a keretrendszereket:

  * `UserNotifications.framework` -a hivatkozásokat beállítása `Optional`
  * `AdSupport.framework` -a hivatkozásokat beállítása `Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> A AdSupport keretrendszer távolítható el. Bevonási kell ezt a keretrendszert idfa-JÁT gyűjtéséhez. Azonban le kell tiltani IDFA-gyűjtést \<ios-sdk-engagement-idfa-ját\> forrástároló új Apple házirenddel ahhoz, hogy
>
>

## <a name="initialize-the-engagement-sdk"></a>Az Engagement SDK inicializálása
Az alkalmazás delegáltjának módosítása kell:

* A megvalósítás fájl felső részén importálja az Engagement ügynök:

      [...]
      #import "EngagementAgent.h"
* Engagement inicializálására belül a metódus "**applicationDidFinishLaunching:**"vagy"**alkalmazás: didFinishLaunchingWithOptions:**":

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Alapvető jelentéskészítési
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Ajánlott módszer: túlterhelés a `UIViewController` osztályok
A jelentés minden, a felhasználók, a munkamenetek, a tevékenységek, az összeomlásokat és a műszaki statisztika számítási Engagement által igényelt naplók aktiválásához egyszerűen végezheti el az összes a `UIViewController` alosztályokat örökli a `EngagementViewController` (ugyanaz a szabály a osztályok`UITableViewController`  -\> `EngagementTableViewController`).

**Nélkül Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Az Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Másodlagos módszer: hívja `startActivity()` manuálisan
Ha nem, vagy nem szeretné, hogy túlterhelés a `UIViewController` osztályok, ehelyett megkezdheti a tevékenységek meghívásával `EngagementAgent`tartozó módszerek közvetlenül.

> [!IMPORTANT]
> Az iOS SDK automatikusan meghívja a `endActivity()` módszer az alkalmazás bezárása után. Így *magas* hívására ajánlott a `startActivity` módszer, amikor a felhasználó tevékenységét módosításához, és *soha* hívja a `endActivity` metódus, mert a metódus hívása az aktuális kényszeríti be kell fejezni munkamenet.
>
>

## <a name="location-reporting"></a>Helyi jelentéskészítés
Szolgáltatási feltételek Apple nem teszik lehetővé az alkalmazások legyen használva a hely csak a statisztika célra nyomon követése. Ebből kifolyólag javasoljuk, hogy helyet jelentések engedélyezése csak akkor, ha az alkalmazás is használhatja azt a helyet, más okból nyomon követése.

IOS 8 kezdve, meg kell adnia hogyan alkalmazása használja-e helyszolgáltatás úgy, hogy egy karakterláncot, a kulcs leírását [NSLocationWhenInUseUsageDescription] vagy [NSLocationAlwaysUsageDescription]az alkalmazás Info.plist fájljában. Ha azt szeretné, a háttérben, az Engagement jelentés helyre, adja meg a kulcs NSLocationAlwaysUsageDescription. Minden más esetben adja hozzá a kulcs NSLocationWhenInUseUsageDescription. Ne feledje, hogy NSLocationAlwaysAndWhenInUseUsageDescription és a NSLocationWhenInUseUsageDescription IOS 11 jelentés háttér helyre.

### <a name="lazy-area-location-reporting"></a>Helymeghatározást
Helymeghatározást lehetővé teszi, hogy az ország, valamint régió és Helység szerint társított eszközök jelentését. A hely jelentéskészítési típusú csak használ a hálózati helyek (cella azonosító vagy Wi-Fi alapján). Az eszköz területen munkamenetenként legfeljebb egyszer jelenti. A rendszer nem használja a GPS, és ezáltal helye a jelentés az ilyen típusú (nem a fel nem) nagyon kevés az akkumulátor hatással.

Jelentett területek segítségével számítási felhasználók, munkamenetek, kapcsolódó események és hibák földrajzi statisztikája. Ezek használhatók a Reach-kampányokat feltételként. Az utolsó ismert területen jelentett eszköz köszönet a következőknek lehet beolvasni a [Device API-val].

Helymeghatározást engedélyezéséhez adja hozzá a következő sort az Engagement ügynök inicializálása után:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Valós idejű hely jelentéskészítési
Valós idejű hely jelentéskészítési lehetővé teszi, hogy a szélességi és hosszúsági társított eszközök jelentését. Alapértelmezés szerint a hely jelentéskészítési csak használja a hálózati helyek (cella azonosító vagy Wi-Fi alapján), és a jelentéskészítési csak aktív futtatásakor az alkalmazás az előtérben (azaz egy munkamenetben).

Valós idejű helyek *nem* statisztika kiszámítására használt. Az egyetlen célja, hogy a geokerítések valós idejű használatának engedélyezése \<Reach-célközönségre-geokerítések\> Reach-kampányokat a feltételnek.

Valós idejű hely jelentések engedélyezéséhez adja hozzá a következő sort az Engagement ügynök inicializálása után:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS-alapú jelentés
Alapértelmezés szerint valós idejű helyét jelentő csak funkció alapú hálózati helyek. (Amelyek pontos sokkal) alapú GPS-helyek használatának engedélyezéséhez vegyen fel:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Háttér-jelentés
Alapértelmezés szerint valós idejű hely jelentéskészítési akkor csak aktív, ha az alkalmazás futtatása az előtérben (azaz egy munkamenetben). A jelentéskészítés is háttérben engedélyezéséhez vegyen fel:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Az alkalmazás futtatásakor a háttérben, csak a hálózati helyek alapú jelenti, még akkor is, ha engedélyezte a GPS.
>
>

Ez a függvény végrehajtása fel fogja hívni [startMonitoringSignificantLocationChanges] amikor az alkalmazás kerülnek-e a háttérben. Vegye figyelembe, hogy ez lesz automatikusan indítsa újra az alkalmazást a háttérben történő Ha új helyet esemény érkezik.

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
Szükség esetén szeretne jelentést készíteni az adott alkalmazásesemények, a hibák és a feladatok, ha szeretné használni az Engagement API-t a a módszerrel a `EngagementAgent` osztály. Ez az osztály egy objektumának meghívásával lehet beolvasni a `[EngagementAgent shared]` statikus metódus.

A bevonási API lehetővé teszi, hogy Engagement speciális funkciók használatára, és az útmutató részletesen az Engagement API-t használó IOS (valamint a műszaki dokumentációját az a `EngagementAgent` osztály).

## <a name="disable-idfa-collection"></a>Tiltsa le a IDFA-gyűjtést
Alapértelmezés szerint az Engagement fogja használni a [idfa-JÁT] a felhasználó egyedi azonosítására. De ha máshol forgalmaz hirdetményt, az alkalmazás nem használja, akkor előfordulhat, hogy utasítani az App Store felülvizsgálati folyamat. Az előfeldolgozó makró hozzáadásával IDFA-gyűjtést letiltható `ENGAGEMENT_DISABLE_IDFA` a pch fájlban (vagy a a `Build Settings` az alkalmazás). Ezzel biztosíthatja, hogy van-e hivatkozhat `ASIdentifierManager`, `advertisingIdentifier` vagy `isAdvertisingTrackingEnabled` az alkalmazás építés.

Az integráció a **prefix.pch** fájlt:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Ellenőrizheti, hogy az IDFA-gyűjtést megfelelően letiltotta az alkalmazás ellenőrzése a bevonási vizsgálati naplókat. Tekintse meg az integráció tesztelése\<ios-sdk-engagement-teszt-idfa-ját\> dokumentációja további információkat.

## <a name="disable-log-reporting"></a>Naplózási jelentések letiltásához
### <a name="using-a-method-call"></a>Metódushívások használatával
Ha azt szeretné, hogy leállítja a naplók küldése Engagement, hívása:

    [[EngagementAgent shared] setEnabled:NO];

Ez a hívás az állandó: használ `NSUserDefaults` az adatok tárolásához.

Engedélyezheti a reporting újra működik meghívásával napló `YES`.

### <a name="integration-in-your-settings-bundle"></a>A beállítások nyalábban integráció
Helyett ezt a funkciót, integrálhatja ezt a beállítást a közvetlenül a meglévő `Settings.bundle` fájlt. A karakterlánc `engagement_agent_enabled` kell használni egy preferencia azonosítóját, és azt kell rendelni a váltógomb kapcsolóhoz (`PSToggleSwitchSpecifier`).

A következő példa `Settings.bundle` megvalósítása szemlélteti:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Device API-val]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[idfa-JÁT]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
