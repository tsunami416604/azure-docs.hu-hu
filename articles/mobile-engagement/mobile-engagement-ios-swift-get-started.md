<properties
    pageTitle="Ismerkedés az Azure Mobile Engagement Swift nyelven írt iOS-alkalmazásokkal való használatával"
    description="Ismerje meg, hogyan használható az Azure Mobile Engagement az iOS-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében."
    services="mobile-engagement"
    documentationCenter="ios"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# Ismerkedés az Azure Mobile Engagement Swift nyelven írt iOS-alkalmazásokkal való használatával

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazás használatának megértéséhez, valamint leküldéses értesítések iOS-alkalmazásba történő küldéséhez a szegmentált felhasználók számára.
Ebben az oktatóanyagban létrehoz egy üres iOS-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad az Apple leküldéses értesítési rendszerének (APNS) használatával.

Az oktatóanyaghoz az alábbiakra lesz szükség:

+ XCode 6 vagy XCode 7, amely a MAC App Store áruházából telepíthető
+ a [Mobile Engagement iOS SDK]
+ Leküldéses értesítési tanúsítvány (.p12), amelyet az Apple fejlesztési központjában szerezhet be

> [AZURE.NOTE] Ez az oktatóanyag a Swift 2.0-s verzióját használja. 

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, iOS-alkalmazásokkal kapcsolatos Mobile Engagement-oktatóanyag elvégzéséhez.

> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>A Mobile Engagement beállítása az iOS-alkalmazáshoz

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez

Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. A teljes integrációs dokumentáció itt található: [Mobile Engagement iOS SDK-integráció](mobile-engagement-ios-sdk-overview.md)

Létre fogunk hozni egy alapszintű alkalmazást az XCode segítségével az integráció bemutatásához:

###Új iOS-projekt létrehozása

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez

1. Töltse le a [Mobile Engagement iOS SDK]
2. Bontsa ki a .tar.gz fájlt a számítógép egyik mappájába
3. Kattintson jobb gombbal a projektre, majd válassza az „Add files to...” (Fájlok hozzáadása ehhez:) elemet

    ![][1]

4. Lépjen abba a mappába, amelyben kibontotta az SDK-t, jelölje ki az `EngagementSDK` mappát, majd kattintson az OK gombra.

    ![][2]

5. Nyissa meg a `Build Phases` lapot, majd a `Link Binary With Libraries` menüben adja hozzá a keretrendszereket az alábbiakban láthatók szerint. **MEGJEGYZÉS** Hozzá kell adnia a következőket: `CoreLocation, CFNetwork, CoreTelephony, and SystemConfiguration`:

    ![][3]

6. **XCode 7** esetén – a `libxml2.tbd` fájlt adja hozzá a `libxml2.dylib` helyett.

7. Hozzon létre egy áthidalási fejlécet, hogy használni tudja az SDK Objective C API-jait; ehhez válassza a File (Fájl) > New (Új) > File (Fájl) > iOS > Source (Forrás) > Header File (Fejlécfájl) elemet.

    ![][4]

8. Szerkessze az áthidalási fejlécfájlt a Mobile Engagement Objective-C kódjának a Swift-kód számára történő közzétételéhez, ehhez adja hozzá az alábbi importálásokat:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEIdfaProvider.h"

9. A Build Settings (Létrehozási beállítások) részen ellenőrizze, hogy a Swift Compiler - Code Generation (Swift fordítóprogram – kódlétrehozás) alatt az Objective-C Bridging Header (Objective-C áthidalási fejléc) létrehozási beállításai erre a fejlécre mutató útvonalat tartalmaznak. Példa az útvonalra: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (az útvonaltól függően)**

    ![][6]

10. Lépjen vissza az Azure Portalra az alkalmazás *Connection Info* (Kapcsolati adatok) lapjáról, és másolja a kapcsolati karakterláncot

    ![][5]

11. Illessze be a kapcsolati karakterláncot a `didFinishLaunchingWithOptions` delegáltba

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Valós idejű figyelés engedélyezése

Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt (tevékenységet) el kell küldenie a Mobile Engagement háttérrendszere számára.

1. Nyissa meg a **ViewController.swift** fájlt, és cserélje le a **ViewController** alapszintű osztályát az **EngagementViewController** osztályra:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése

A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### Csendes leküldéses értesítések fogadásának engedélyezése az alkalmazásban

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### A Reach könyvtár hozzáadása a projekthez

1. Kattintson a jobb gombbal a projektre
2. Válassza ezt: `Add file to ...`
3. Lépjen abba a mappába, amelyben kibontotta az SDK-t
4. Jelölje ki az `EngagementReach` mappát
5. Kattintson az Add (Hozzáadás) parancsra
6. Szerkessze az áthidalási fejlécfájlt a Mobile Engagement Objective-C Reach fejléceinek közzétételéhez, és adja hozzá az alábbi importálásokat:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### Az alkalmazás delegáltjának módosítása

1. A `didFinishLaunchingWithOptions` módszerben hozzon létre egy Reach modult, és adja át azt az Engagement meglévő inicializációs sorának:

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###APNS leküldéses értesítések fogadásának engedélyezése az alkalmazásban
1. Adja a következő sort az `didFinishLaunchingWithOptions` módszerhez:

        /* Ask user to receive push notifications */
        if #available(iOS 8.0, *)
        {
           let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
           application.registerUserNotificationSettings(settings)
           application.registerForRemoteNotifications()
        }
        else
        {
           application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
        }

2. Adja hozzá a `didRegisterForRemoteNotificationsWithDeviceToken` módszert az alábbiak szerint:

        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
        {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Adja hozzá a `didReceiveRemoteNotification:fetchCompletionHandler:` módszert az alábbiak szerint:

        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
        {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=sep16_HO1-->


