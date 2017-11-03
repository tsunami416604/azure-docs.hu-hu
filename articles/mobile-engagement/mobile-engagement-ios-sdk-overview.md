---
title: "Az Azure Mobile Engagement iOS SDK áttekintése |} Microsoft Docs"
description: "Legújabb frissítések és az Azure Mobile Engagement SDK iOS eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6acd343782a3ee07750e27ec3022ff81cedfadee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK for Azure Mobile Engagement (iOS SDK és Azure Mobile Engagement)
A részletek hozzáférhet integrálása az Azure Mobile Engagement az iOS-alkalmazás, kezdje itt. Ha meg szeretné adni az első egy try, tegye meg a [15 perc oktatóanyag](mobile-engagement-ios-get-started.md).

Megtekintéséhez kattintson ide a [SDK tartalom](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Integráció eljárások
1. Kezdje itt: [integrálása a Mobile Engagement az iOS-alkalmazás](mobile-engagement-ios-integrate-engagement.md)
2. Az értesítések: [Reach (értesítések) integrálása az iOS-alkalmazás](mobile-engagement-ios-integrate-engagement-reach.md)
3. Megvalósítási terv címkét: [a speciális a Mobile Engagement az iOS-alkalmazás szerinti címkézését API használata](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Rögzített jelvények háttérben törölve lett.
* Rögzített figyelmeztetések a XCode 9 kapcsolatos API-k nem hívja a fő várakozási sorba.
* Rögzített memóriavesztés Reach lekérdezéseinél.
* Támogatás az iOS eldobott 6.X. A központi telepítés célja az alkalmazás ebben a verzióban kiindulva kell legalább iOS 7-es.

Korábbi verziójához lásd: a [végezze el a kibocsátási megjegyzések](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Frissítési eljárások
Ha Ön már rendelkezik integrált Engagement régebbi verzióját az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Kövesse a számos eljárást, ha nem fogadta a teljes SDK lásd: a több verzióját is [frissítése eljárások](mobile-engagement-ios-upgrade-procedure.md).

Az SDK minden új verziójának először cserélje le (távolítsa el, és importálja újra az xcode-ban) EngagementSDK és EngagementReach mappákat.

### <a name="from-300-to-400"></a>A 3.0.0 4.0.0 számára
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

#### <a name="usernotifications-framework"></a>UserNotifications keretrendszer
Hozzá kell adnia a `UserNotifications` keretrendszer a Build lépésből áll.

a project explorer nyissa meg a projekt ablaktáblán, és jelölje ki a megfelelő cél. Ezután nyissa meg a **"Összeállítási fázisok"** lapon és a a **"Bináris rendelkező Kódtárakon"** menüben adja hozzá a keretrendszer `UserNotifications.framework` -állítsa be a hivatkozásokat`Optional`

#### <a name="application-push-capability"></a>Alkalmazás leküldéses funkció
XCode 8 lehetséges, hogy az alkalmazás alaphelyzetbe leküldéses funkció, ellenőrizze még egyszer a `capability` a kiválasztott cél fülre.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Adja hozzá az új iOS 10 értesítési regisztrációs kódot
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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>UNUserNotificationCenter delegált ütközések feloldása

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
