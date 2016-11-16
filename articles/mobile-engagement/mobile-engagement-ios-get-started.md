---
title: "Ismerkedés az Azure Mobile Engagement Objective C nyelven írt iOS-alkalmazásokkal való használatával | Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Mobile Engagement az iOS-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/05/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1dc9885e4cdbad1153ac476e3f0c0068ec391374


---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Ismerkedés az Azure Mobile Engagement Objective C nyelven írt iOS-alkalmazásokkal való használatával
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazás használatának megértéséhez, valamint leküldéses értesítések iOS-alkalmazásba történő küldéséhez a szegmentált felhasználók számára.
Ebben az oktatóanyagban létrehoz egy üres iOS-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad az Apple leküldéses értesítési rendszerének (APNS) használatával.

Az oktatóanyaghoz az alábbiakra lesz szükség:

* XCode 8, amely a MAC App Store áruházából telepíthető
* a [Mobile Engagement iOS SDK]

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, iOS-alkalmazásokkal kapcsolatos Mobile Engagement-oktatóanyag elvégzéséhez.

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>A Mobile Engagement beállítása az iOS-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. A teljes integrációs dokumentáció itt található: [Mobile Engagement iOS SDK-integráció](mobile-engagement-ios-sdk-overview.md)

Létre fogunk hozni egy alapszintű alkalmazást az XCode segítségével az integráció bemutatásához.

### <a name="create-a-new-ios-project"></a>Új iOS-projekt létrehozása
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Töltse le a [Mobile Engagement iOS SDK].
2. Bontsa ki a .tar.gz fájlt a számítógép egyik mappájába.
3. Kattintson jobb gombbal a projektre, majd válassza az **Add files to** (Fájlok hozzáadása ehhez:) elemet.
   
    ![][1]
4. Lépjen abba a mappába, amelyben kibontotta az SDK-t, jelölje ki az `EngagementSDK` mappát, majd kattintson az **OK** gombra.
   
    ![][2]
5. Nyissa meg a **Build Phases** (Összeállítási fázisok) lapot, majd a **Link Binary With Libraries** (Bináris összekapcsolása könyvtárakkal) menüben adja hozzá a keretrendszereket az alábbiakban láthatók szerint:
   
    ![][3]
6. Lépjen vissza az Azure Portalra az alkalmazás **Connection Info** (Kapcsolati adatok) lapjáról, és másolja a kapcsolati karakterláncot.
   
    ![][4]
7. Adja hozzá a következő kódsort az **AppDelegate.m** fájlhoz.
   
        #import "EngagementAgent.h"
8. Illessze be a kapcsolati karakterláncot a `didFinishLaunchingWithOptions` delegáltba.
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
              [...]   
              [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
              [...]
        }
9. `setTestLogEnabled` egy választható utasítás, amely engedélyezi az SDK-naplókat a problémák azonosításához. 

## <a name="a-idmonitoraenable-realtime-monitoring"></a><a id="monitor"></a>Valós idejű figyelés engedélyezése
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt (tevékenységet) el kell küldenie a Mobile Engagement háttérrendszere számára.

1. Nyissa meg a **ViewController.h** fájlt, és importálja az **EngagementViewController.h** fájlt:
   
    `# import "EngagementViewController.h"`
2. Cserélje le a **ViewController** felületének főosztályát az `EngagementViewController` fájllal:
   
    `@interface ViewController : EngagementViewController`

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenable-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Csendes leküldéses értesítések fogadásának engedélyezése az alkalmazásban
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>A Reach könyvtár hozzáadása a projekthez
1. Kattintson jobb gombbal a projektre.
2. Válassza az **Add file to** (Fájl hozzáadása ehhez:) elemet.
3. Lépjen abba a mappába, amelyben kibontotta az SDK-t.
4. Jelölje ki az `EngagementReach` mappát.
5. Kattintson az **Add** (Hozzáadás) parancsra.

### <a name="modify-your-application-delegate"></a>Az alkalmazás delegáltjának módosítása
1. Az **AppDeletegate.m** fájlban importálja az Engagement Reach modulját.
   
        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>
2. Az `application:didFinishLaunchingWithOptions` módszerben hozzon létre egy Reach modult, és adja át azt az Engagement meglévő inicializációs sorának:
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>APNS leküldéses értesítések fogadásának engedélyezése az alkalmazásban
1. Adja a következő sort az `application:didFinishLaunchingWithOptions` módszerhez:
   
        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }
2. Adja hozzá a `application:didRegisterForRemoteNotificationsWithDeviceToken` módszert az alábbiak szerint:
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
             [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }
3. Adja hozzá a `didFailToRegisterForRemoteNotificationsWithError` módszert az alábbiak szerint:
   
        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
   
           NSLog(@"Failed to get token, error: %@", error);
        }
4. Adja hozzá a `didReceiveRemoteNotification:fetchCompletionHandler` módszert az alábbiak szerint:
   
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png




<!--HONumber=Nov16_HO2-->


