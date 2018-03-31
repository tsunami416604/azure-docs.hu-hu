---
title: Az Azure Mobile Engagement iOS SDK frissítési eljárás |} Microsoft Docs
description: Legújabb frissítések és az Azure Mobile Engagement SDK iOS eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ac03593cf0c983297e583985a3e68ea6c17e39de
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Frissítési eljárások
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ha Ön már rendelkezik integrált Engagement régebbi verzióját az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Az SDK minden új verziójának először cserélje le (távolítsa el, és importálja újra az xcode-ban) EngagementSDK és EngagementReach mappákat.

## <a name="from-300-to-400"></a>A 3.0.0 4.0.0 számára
### <a name="xcode-8"></a>XCode 8
XCode 8 megadása kötelező, az SDK 4.0.0 verziójával kezdve.

> [!NOTE]
> Ha valóban függenek XCode 7, akkor előfordulhat, hogy használja a [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Nincs egy ismert hiba az előző verzió a reach-modul iOS 10-eszközök futtatása közben: nincsenek műveletet kiváltó rendszerértesítéseket. A javítás kell megvalósítani a elavult API `application:didReceiveRemoteNotification:` az alkalmazás delegálása az alábbiak szerint:
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Ez a megoldás nem ajánlott** , ez a viselkedés a jövőbeli (még akkor is kisebb) iOS verzió frissítése is módosítható, mivel az iOS API elavult. Át kell váltania XCode 8 lehető legrövidebb időn belül.
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications keretrendszer
Hozzá kell adnia a `UserNotifications` keretrendszer a Build lépésből áll.

a project explorer nyissa meg a projekt ablaktáblán, és jelölje ki a megfelelő cél. Ezután nyissa meg a **"Összeállítási fázisok"** lapon és a a **"Bináris rendelkező Kódtárakon"** menüben adja hozzá a keretrendszer `UserNotifications.framework` -állítsa be a hivatkozásokat `Optional`

### <a name="application-push-capability"></a>Alkalmazás leküldéses funkció
XCode 8 lehetséges, hogy az alkalmazás alaphelyzetbe leküldéses funkció, ellenőrizze még egyszer a `capability` a kiválasztott cél fülre.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Adja hozzá az új iOS 10 értesítési regisztrációs kódot
A régebbi kódrészletet értesítések az alkalmazás regisztrálásához továbbra is működik, de iOS 10 futtatott elavult API-kat használ.

Importálás a `User Notification` keretrendszer:

        #import <UserNotifications/UserNotifications.h> 

Az alkalmazás delegáltjának a `application:didFinishLaunchingWithOptions` metódus cseréje:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

szerint:

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

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter delegált ütközések feloldása

*Ha az alkalmazás és a harmadik féltől származó tárak egyike nem valósítja meg a `UNUserNotificationCenterDelegate` akkor kihagyhatja ezt a részt.*

A `UNUserNotificationCenter` delegált az SDK figyelésére szolgál a életciklusának Engagement értesítések IOS 10 vagy újabb rendszert futtató eszközökön. Az SDK-val rendelkezik saját megvalósítása a `UNUserNotificationCenterDelegate` protokoll, de lehet csak az egyik `UNUserNotificationCenter` alkalmazásonként delegálni. Bármely más delegált hozzáadni a `UNUserNotificationCenter` objektum ütközik az egyik Engagement. Ha az SDK-t a vagy bármely más harmadik fél delegált észlel, akkor azt nem használja a saját megvalósítási Önnek arra, hogy oldja fel az ütközéseket. A bevonási logika hozzáadása a saját delegált ahhoz, hogy oldja fel az ütközéseket fog.

Ennek eléréséhez két módja van.

Javaslat 1, egyszerűen működve a delegált hívások a SDK-val:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Vagy javaslat 2, való öröklődés a `AEUserNotificationHandler` osztály

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Azt is meghatározhatja, hogy értesítést az engagement szolgáltatásból, vagy nem úgy, hogy rendelkezik-e a `userInfo` ügynökkel szótár `isEngagementPushPayload:` metódus osztályban.

Győződjön meg arról, hogy a `UNUserNotificationCenter` objektum delegált belül vagy a meghatalmazott értékre van állítva a `application:willFinishLaunchingWithOptions:` vagy a `application:didFinishLaunchingWithOptions:` az alkalmazás delegáltjának metódusában.
Például ha megvalósította a fenti 1 javaslat:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="from-200-to-300"></a>A 2.0.0 3.0.0 számára
Támogatás az iOS eldobott 4.X. A központi telepítés célja az alkalmazás ebben a verzióban kiindulva kell legalább iOS 6.

Ha az alkalmazás Reach használ, hozzá kell adnia `remote-notification` egy érték a `UIBackgroundModes` tömb távoli értesítések fogadásához az Info.plist fájlban.

A metódus `application:didReceiveRemoteNotification:` által le kell cserélni `application:didReceiveRemoteNotification:fetchCompletionHandler:` a az alkalmazás delegáltjának.

Elavult "AEPushDelegate.h" illesztőfelület, és át kell távolítson el minden hivatkozást. Ez magában foglalja eltávolításával `[[EngagementAgent shared] setPushDelegate:self]` és az alkalmazás delegáltjának delegált metódusait:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>A 1.16.0 2.0.0 számára
A következő ismerteti, hogyan telepíthetők át az SDK-integráció az alkalmazásba az Azure Mobile Engagement technológiával Capptain SAS által kínált Capptain szolgáltatás.
Ha egy korábbi verziójáról telepít, tekintse meg a Capptain webhely 1.16 először át, akkor alkalmazza az alábbi eljárást.

> [!IMPORTANT]
> Capptain és a Mobile Engagement nem ugyanazok a szolgáltatások és az alábbi eljárás csak emel ki, hogyan telepítheti át az ügyfélalkalmazás. Az SDK-t az alkalmazás áttelepítése rendszer nem telepíti át az adatok a Capptain kiszolgálókról a Mobile Engagement-kiszolgálókon
> 
> 

### <a name="agent"></a>Ügynök
A metódus `registerApp:` felváltotta az új módszer `init:`. Az alkalmazás delegáltjának frissíteni kell ennek megfelelően, és használja a kapcsolati karakterlánc:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd követési el lett távolítva csak el kell távolítania az összes példányát SDK `AETrackModule` osztály

### <a name="class-name-changes"></a>Osztály neve megváltozik
A rebranding részeként nincsenek osztály/fájlnevek módosítani kell néhány.

Minden osztály előtagként a "CP" átnevezi "AE" előtaggal.

Példa:

* `CPModule.h` a rendszer átnevezi `AEModule.h`.

Minden osztály előtagként a "Capptain" átnevezi "Engagement" előtaggal.

Példák:

* Az osztály `CapptainAgent` neve módosult az `EngagementAgent`.
* Az osztály `CapptainTableViewController` neve módosult az `EngagementTableViewController`.
* Az osztály `CapptainUtils` neve módosult az `EngagementUtils`.
* Az osztály `CapptainViewController` neve módosult az `EngagementViewController`.

