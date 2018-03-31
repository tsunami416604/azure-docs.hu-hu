---
title: Az Azure Mobile Engagement iOS SDK-integráció elérni |} Microsoft Docs
description: Legújabb frissítések és az Azure Mobile Engagement SDK iOS eljárásai
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 8d531f5850e8f7f352774f5894285402bd4cc53e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Hogyan kell integrálni Engagement Reach IOS rendszerű eszközökön
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Az integráció az ismertetett eljárást kell követni a [hogyan integrálhatja Engagement iOS dokumentumon](mobile-engagement-ios-integrate-engagement.md) Ez az útmutató követése előtt.

Ebben a dokumentációban XCode 8 igényel. Ha valóban függenek XCode 7, akkor előfordulhat, hogy használja a [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Nincs egy ismert hiba az előző verzió iOS 10-eszközök futtatása közben: nincsenek műveletet kiváltó rendszerértesítéseket. A javítás kell megvalósítani a elavult API `application:didReceiveRemoteNotification:` az alkalmazás delegálása az alábbiak szerint:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Ez a megoldás nem ajánlott** , ez a viselkedés a jövőbeli (még akkor is kisebb) iOS verzió frissítése is módosítható, mivel az iOS API elavult. Át kell váltania XCode 8 lehető legrövidebb időn belül.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Csendes leküldéses értesítések fogadásának engedélyezése az alkalmazásban
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Integrációs lépések
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Az Engagement Reach SDK beágyazása az iOS-projekthez
* A Reach sdk hozzáadása az Xcode projektjében. Az Xcode-ban Ugrás **projekt \> hozzáadása a projekthez** , és válassza a `EngagementReach` mappát.

### <a name="modify-your-application-delegate"></a>Az alkalmazás delegáltjának módosítása
* A megvalósítás fájl felső részén importálja az Engagement Reach modulját:

      [...]
      #import "AEReachModule.h"
* Módszer belül `applicationDidFinishLaunching:` vagy `application:didFinishLaunchingWithOptions:`, hozzon létre egy reach modult, és adja át azt az Engagement meglévő inicializációs sorának:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Módosítsa **"icon.png"** a lemezkép neve, amelyet az értesítési ikon karakterlánc.
* Ha azt szeretné, hogy a kapcsoló használatához *frissítés jelvény érték* Reach-kampányokat, vagy ha a natív leküldéses használni kívánt \<SaaS/Reach API/kampány formátum/natív leküldéses\> kampányok, segítségével a Reach-modul a jelvény kezelése ikon magát (emellett automatikusan törli a az alkalmazás jelvény és is alaphelyzetbe állíthatja az Engagement által tárolt minden alkalommal, amikor az alkalmazás elindítva, vagy foregrounded érték). Ehhez adja hozzá a következő sort a Reach-modul inicializálása után:

      [reach setAutoBadgeEnabled:YES];
* Szeretné kezelni a Reach adatleküldés, ha az alkalmazás delegáltjának felelnek meg kell lehetővé teszik a `AEReachDataPushDelegate` protokoll. Adja hozzá a következő sort a Reach-modul inicializálása után:

      [reach setDataPushDelegate:self];
* A módszerek is létrehozható, majd `onDataPushStringReceived:` és `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` a az alkalmazás delegáltjának:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Kategória
A kategória paraméter nem kötelező, amikor adatokat leküldéses kampány létrehozása, és lehetővé teszi, hogy az adatok szűrése leküldéses értesítések. Ez akkor hasznos, ha azt szeretné, hogy különböző leküldéses a `Base64` adatokat, és azonosítja a őket elemzése előtt szeretné.

**Az alkalmazás fogadni ki és jelenítheti meg a reach-tartalmak készen áll!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Mutató hirdetmények és szavazások bármikor fogadásának módját
Engagement Reach értesítéseket küldhet a végfelhasználók bármikor az Apple Push Notification szolgáltatás használatával.

Ez a funkció engedélyezéséhez meg kell előkészítése az Apple leküldéses értesítések az alkalmazás és az alkalmazás delegáltjának módosítása.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Az Apple leküldéses értesítések az alkalmazás előkészítése
Hajtsa végre az útmutató: [az Apple leküldéses értesítések az alkalmazás előkészítése](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Adja hozzá a szükséges ügyfél kódot
*Az alkalmazás ezen a ponton a bevonási előtér kell rendelkezniük a regisztrált Apple leküldéses tanúsítvány.*

Ha nem már elkészült, akkor kell leküldéses értesítések fogadásának az alkalmazás regisztrálásához.

* Importálás a `User Notification` keretrendszer:

        #import <UserNotifications/UserNotifications.h>
* Adja hozzá a következő sort, az alkalmazás indításakor (általában a `application:didFinishLaunchingWithOptions:`):

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

Ezt követően meg kell adnia az eszköz jogkivonatát az Apple-kiszolgálók által visszaadott engagement. Ezt a metódust nevű `application:didRegisterForRemoteNotificationsWithDeviceToken:` a az alkalmazás delegáltjának:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Végül hogy tájékoztatja az Engagement SDK-t, ha az alkalmazás távoli kapcsolatos értesítőt kap. A metódus meghívására ehhez `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` a az alkalmazás delegáltjának:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Alapértelmezés szerint az Engagement Reach szabályozza a completionHandler. Ha azt szeretné, hogy manuálisan válaszolni a `handler` a kódban letiltása, üres átadhatók a `handler` argumentum vezérlő befejezésére blokkolják vagy saját maga. Tekintse meg a `UIBackgroundFetchResult` típusa a lehetséges értékek listáját.
>
>

### <a name="full-example"></a>Teljes példa
Ez az integráció teljes példa:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
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

## <a name="how-to-customize-campaigns"></a>A kampányok testreszabása
### <a name="notifications"></a>Értesítések
Az értesítések két típusa van: rendszer és az alkalmazáson belüli értesítések.

Rendszer értesítések iOS kezeli, és nem szabható testre.

Alkalmazásbeli értesítéseket egy az aktuális alkalmazás ablak dinamikusan hozzáadott nézetek. Ez a lehetőség egy értesítési területre. Értesítési átfedések rendkívül gyors integrációs, mivel azok nem szükséges, hogy módosítsa úgy a nézetet, az alkalmazásban.

#### <a name="layout"></a>Elrendezés
A tulajdonságtáblázat használatával módosíthatja az alkalmazáson belüli értesítések, egyszerűen módosíthatja a fájlt `AENotificationView.xib` az igényeinek megfelelően, amennyiben megakadályozhatja az előfizetéscímkék értékeit és a meglévő subviews típusú.

Alapértelmezés szerint az alkalmazásbeli értesítésekben jelennek meg a képernyő alján. Ha szeretné megjeleníteni azokat a képernyő tetejére, szerkesztheti a megadott `AENotificationView.xib` , és módosítsa a `AutoSizing` tulajdonság a fő nézet, így is megmarad a superview tetején.

#### <a name="categories"></a>Kategóriák
Ha módosítja a megadott elrendezését, az értesítések megjelenésének módosítása. Kategóriák lehetővé teszik a különböző megcélzott keres (valószínűleg viselkedések) megadása az értesítések. A kategória a Reach-kampány létrehozásakor adható meg. Ne feledje, hogy kategóriák is segítségével testre szabható mutató hirdetmények és szavazások, a dokumentum későbbi szakaszában ismertetett.

Az értesítések kategória leíró regisztrálásához kell hozzáadnia hívása után a reach-modul inicializálása.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` olyan objektum, amely megfelel-e a protokoll egy példányának kell lennie `AENotifier`.

A protokoll módszerek önállóan is alkalmazható, vagy dönthet úgy, hogy a meglévő osztály reimplement `AEDefaultNotifier` melyik már teljesít a munka nagyobb része.

Például ha azt szeretné, az értesítési nézet egy adott kategória újradefiniálása, ebben a példában követheti:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Egyszerű példa a kategória azt feltételezik, hogy rendelkezik-e egy fájlt `MyNotificationView.xib` az alkalmazás fő kötegben. Ha a metódus nem találta meg a megfelelő `.xib`, az értesítés nem fog megjelenni, és Engagement kimeneteként egy üzenetet a konzolon.

A megadott nib fájl tiszteletben kell tartania a következő szabályokat:

* Csak egy nézetet kell tartalmaznia.
* Subviews nevet kell adni a azonos típusú, a megadott nib fájl megfelelően `AENotificationView.xib`
* Subviews kell rendelkeznie a azonos címkéket, mint a belsejében a megadott nevű nib-fájl `AENotificationView.xib`

> [!TIP]
> Csak a megadott nib nevű fájlban található, másolja `AENotificationView.xib`, és ott munka megkezdéséhez. De legyen óvatos, a nézet a nib fájlban társítva az osztály `AENotificationView`. Ez az osztály a metódus újradefiniálása `layoutSubViews` áthelyezni, és annak megfelelően környezetben subviews átméretezése. Érdemes lehet cserélje le egy `UIView` vagy egyéni nézet osztály.
>
>

Ha az értesítések mélyebb testreszabása van szüksége (Ha például azt szeretné betölteni a nézetet közvetlenül a kódból), javasoljuk, hogy a megadott forrás kódot és az osztály a dokumentációban tekintse meg `Protocol ReferencesDefaultNotifier` és `AENotifier`.

Vegye figyelembe, hogy az azonos bejelentő több kategóriákban használhatja.

Az alapértelmezett bejelentő ilyen újra is definiálva:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Értesítés kezelése
Az alapértelmezett kategóriát használatakor egyes életciklus metódusok felhívta a `AEReachContent` jelentés statisztikákhoz objektumra, és frissíti a kampány állapotát:

* Ha az alkalmazás megjelenik az értesítés a `displayNotification` módszer neve (amely statisztikáit) által `AEReachModule` Ha `handleNotification:` adja vissza `YES`.
* Az értesítés meg van nyitva, ha a `exitNotification` metódus lehívásra kerül, statisztika jelez, és tovább kampányok most már tudja feldolgozni.
* Ha az értesítési kattint, `actionNotification` van nevű statisztika jelez, és a kapcsolódó művelet végrehajtását.

Ha a végrehajtásának `AENotifier` figyelmen kívül hagyja az alapértelmezett viselkedés önállóan életciklus módszerekhez hívja. A következő példák bemutatják az egyes esetekben, ahol az alapértelmezett viselkedés elmarad:

* Nem terjeszti ki `AEDefaultNotifier`, pl. megvalósította a teljesen új kategória kezelését.
* Ön overrode `prepareNotificationView:forContent:`, ügyeljen arra, hogy legalább leképezése `onNotificationActioned` vagy `onNotificationExited` egy a U.I szabályozza.

> [!WARNING]
> Ha `handleNotification:` jelez kivételt, a tartalom törlése és `drop` van neve, ez statisztika jelez, és tovább kampányok most már tudja feldolgozni.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Egy meglévő nézethez részeként értesítési tartalmazza
Átfedések ideális az egy gyors integrációt, de lehet néha nem kényelmes, vagy nemkívánatos hatásai lehetnek.

Ha nem elégedett az átmeneti területre rendszer egyes a nézetek, testre szabhatja, ezek a nézetek.

Eldöntheti, hogy az értesítési elrendezés szerepeljenek a meglévő nézetek. Ehhez két megvalósítási stílusok van:

1. A felület builder használatával értesítési nézet hozzáadása

   * Nyissa meg *jelentéskészítő csatoló*
   * Helyezze a 320 x 60 (vagy ha iPad 768 x 60) `UIView` a értesítést megjeleníteni kívánt
   * Ez a nézet a Tag értékét: **36822491**
2. Programozott módon vegye fel az értesítési nézet. A nézet inicializálásakor csak hozzá a következő kódot:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG` makró található `AEDefaultNotifier.h`.

> [!NOTE]
> Az alapértelmezett bejelentő automatikusan észleli, hogy az értesítési elrendezés szerepel ebben a nézetben, és nem adja hozzá egy átmeneti területre az.
>
>

### <a name="announcements-and-polls"></a>Hirdetmények és szavazások esetén
#### <a name="layouts"></a>Elrendezés
Módosíthatja a fájlokat `AEDefaultAnnouncementView.xib` és `AEDefaultPollView.xib` mindaddig megőrzi az előfizetéscímkék értékeit és a meglévő subviews típusú.

#### <a name="categories"></a>Kategóriák
##### <a name="alternate-layouts"></a>Alternatív elrendezések
Értesítések, például a kampány kategória használható kell rendelkeznie a hirdetmények és szavazások esetén a másik elrendezés.

Hozzon létre egy közlemény kategóriát, ki kell terjesztenie **AEAnnouncementViewController** , és regisztrálja a reach-modul inicializálása után:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Minden alkalommal, amikor a felhasználó fog kattintson a kategória egy közlemény értesítést "saját\_kategória", a regisztrált nézetvezérlő (ebben az esetben `MyCustomAnnouncementViewController`) metódus hívásával inicializálni fogja `initWithAnnouncement:` és a nézet nem kerülnek be a aktuális alkalmazás ablak.
>
>

A végrehajtására a `AEAnnouncementViewController` kell olvasni a tulajdonságot osztály `announcement` a subviews inicializálása. Vegye figyelembe az alábbi példában, ahol két címkék használatával olyan inicializálása `title` és `body` tulajdonságainak a `AEReachAnnouncement` osztály:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Ha nem szeretné betölteni a nézetek önállóan, de szeretné újra felhasználhatja az alapértelmezett bejelentés nézet elrendezését, egyszerűen elvégezheti a egyéni nézetvezérlő bővíti a megadott osztály `AEDefaultAnnouncementViewController`. Ebben az esetben a nib fájl ismétlődő `AEDefaultAnnouncementView.xib` , és nevezze át, az egyéni nézetvezérlő betölthető legyen (nevű tartományvezérlő `CustomAnnouncementViewController`, meg kell hívnia a nib fájl `CustomAnnouncementView.xib`).

Cserélje le a hirdetmények alapértelmezett kategóriáját, egyszerűen regisztrálja a megadott kategória egyéni nézet tartományvezérlője `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Szavazások ugyanúgy testre szabható:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Most, a megadott `MyCustomPollViewController` kell terjesztenie `AEPollViewController`. Vagy választhatja azt is, az alapértelmezett vezérlőből kiterjeszteni: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Ne feledje hívás vagy `action` (`submitAnswers:` az egyéni lekérdezési nézet vezérlők) vagy `exit` módszer a nézetvezérlő eltűnése előtt. Ellenkező esetben statisztika nem küldhető el (azaz a kampány nem elemzés), és további fontos következő kampányok nem kapnak értesítést az alkalmazás folyamatának újraindításáig.
>
>

##### <a name="implementation-example"></a>Megvalósítási példa
Ebben az implementációban a egyéni bejelentés nézet be van töltve egy külső xib fájlból.

Például a speciális értesítési testreszabáshoz, javasoljuk, hogy tekintse meg a szabványos megvalósítástól forráskódját.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
