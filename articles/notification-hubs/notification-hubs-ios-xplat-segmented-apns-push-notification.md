---
title: Leküldéses értesítések küldése az adott iOS-eszközök Azure Notification Hubs használatával |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan használja az Azure Notification Hubs leküldéses értesítések küldéséhez az adott iOS-eszközökre.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: aaaeb4e101147c19af5bd1dc7071cca273255863
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449759"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése az adott iOS-eszközök Azure Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Az oktatóanyag bemutatja, hogyan használhatja az Azure Notification Hubs iOS-alkalmazás legfrissebb hírekről szóló értesítések küldhetők. Amikor végzett, regisztrálhat az Önt érdeklő hírkategóriák használhatatlanná tévő, és csak leküldéses értesítések fogadása az ezekben a kategóriákban. Ezt a forgatókönyvet gyakran használják olyan alkalmazásokban, ahol az értesítéseket egy adott téma iránt korábban érdeklődő felhasználók csoportjainak kell elküldeni. Ilyen lehet például egy RSS-olvasó, a zenerajongóknak készült alkalmazások stb.

A közvetítési forgatókönyveket úgy lehet engedélyezni, ha az értesítési központban a regisztráció létrehozásakor hozzáad egy vagy több *címkét*. Amikor a rendszer értesítéseket küld egy címkét, a címkével ellátott regisztrált eszközök megkapja az értesítést. A címkék egyszerű sztringek, nem szükséges őket előzetesen kiosztani. További információ a címkékről: [Útválasztás és címkekifejezések az értesítési központokban](notification-hubs-tags-segment-push-message.md).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A választott kategória hozzáadása az alkalmazáshoz
> * Címkézett értesítések küldése
> * Értesítések küldése az eszközről
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör épül, amely a létrehozott alkalmazás [oktatóanyag: Leküldéses értesítések iOS-alkalmazások Azure Notification Hubs használatával][get-started]. Az oktatóanyag elindítása előtt már végrehajtotta [oktatóanyag: Leküldéses értesítések iOS-alkalmazások Azure Notification Hubs használatával][get-started].

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Az első lépés, hogy a felhasználói felületi elemeket ad hozzá a meglévő storyboard fájlt definiál, amelyek lehetővé teszik a felhasználók kiválaszthatják a kategóriák regisztrálni. A felhasználó által kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. Az a **MainStoryboard_iPhone.storyboard** adja hozzá a következő összetevőket az objektumtárból:

    * Egy "Híreket használhatatlanná tévő" szövegű felirat
    * "A World", "Politika", "Vállalati", "Technológia", "Adatelemzési", "Sport", kategória szövegeket tartalmazó feliratok
    * Egy kategória, egy hat kapcsolók beállítása minden kapcsoló **állapot** kell **ki** alapértelmezés szerint.
    * Egy gomb "Subscribe" címkével

    A storyboard fájlt definiál az alábbihoz hasonlóan kell kinéznie:

    ![Xcode felület jelentéskészítő][3]

2. A Segéd-szerkesztőben létrehozása tartalomválogatást összes olyan kapcsolón, és hívja meg őket "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Hozzon létre egy műveletet a nevű gomb `subscribe`; a `ViewController.h` tartalmaznia kell a következő kódot:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Hozzon létre egy új **Cocoa Touch osztály** nevű `Notifications`. Másolja az alábbi kódot a fájl Notifications.h felület szakaszában:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adja hozzá a következő importálási irányelv Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Másolja az alábbi kódot a fájl Notifications.m a megvalósítási szakaszban.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Ez az osztály tárolására és beolvasására a kategóriák hírei, amely az eszköz megkapja a helyi tároló használ. Emellett ezekben a kategóriákban használatával regisztráljon egy metódust tartalmaz egy [sablon](notification-hubs-templates-cross-platform-push-messages.md) regisztráció.

7. Az a `AppDelegate.h` fájlt, adja hozzá az importálási utasítást a `Notifications.h` és a egy példányának tulajdonság hozzáadása a `Notifications` osztály:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. Az a `didFinishLaunchingWithOptions` metódus az `AppDelegate.m`, adja hozzá a kódot, a metódus az elején értesítések példány inicializálásához.  
    `HUBNAME` és `HUBLISTENACCESS` (meghatározott `hubinfo.h`) már rendelkezik a `<hub name>` és `<connection string with listen access>` helyőrzőket az értesítési központ nevére és a kapcsolati karakterláncát helyére *DefaultListenSharedAccessSignature*korábban beszerzett

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a figyelési hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

9. Az a `didRegisterForRemoteNotificationsWithDeviceToken` metódus az `AppDelegate.m`, cserélje le a metódus a kódot, az eszköz jogkivonatát adja át a következő kódot a `notifications` osztály. A `notifications` osztály hajt végre, a kategóriák az értesítések regisztrálása. Ha a felhasználó megváltoztatja a kategóriáinak kiválasztásánál, hívja meg a `subscribeWithCategories` válaszul metódus az **előfizetés** gombra kattintva frissítheti őket.

    > [!NOTE]
    > Az eszköz jogkivonatát által az Apple Push Notification Service (APNS) hozzárendelt is alkalommal tetszőleges időpontban, mert az értesítések értesítés sikertelen végrehajtásának elkerülése érdekében gyakran kell regisztrálni. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran, naponta egynél többször futtatott alkalmazások esetén a sávszélesség megőrzése érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Ezen a ponton meg kell adni a más kódot nem a `didRegisterForRemoteNotificationsWithDeviceToken` metódust.

10. Az alábbi módszerek már jelen kell lenniük `AppDelegate.m` befejezze a [Ismerkedés a Notification Hubs] [ get-started] oktatóanyag. Ha nem, adja hozzá őket.

    ```objc
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Ez a metódus kezeli az alkalmazás egy egyszerű megjelenítésével futtatásakor kapott értesítéseket **UIAlert**.

11. A `ViewController.m`, adjon hozzá egy `import` nyilatkozata `AppDelegate.h` másolja az alábbi kódot az xcode-ban létrehozott `subscribe` metódus. Ez a kód frissíti a felhasználó a felhasználói felületen úgy döntött, hogy az új kategória címkék használata az értesítésre való regisztráció.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Ezzel a módszerrel hoz létre egy `NSMutableArray` kategóriák, és használja a `Notifications` a lista tárolja a helyi tárhely és regisztrál az értesítési központban a megfelelő címkéket az osztály. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

12. A `ViewController.m`, adja hozzá a következő kódot a `viewDidLoad` metódussal adja meg a felhasználói felület a korábban mentett kategóriák alapján.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Az alkalmazás most már tárolhat kategóriák készletét, amikor az alkalmazás indítása az értesítési központon történő regisztrálásához használt eszköz helyi tárolójára. A felhasználó módosíthatja a kijelölt modult, és kattintson a kategóriák a `subscribe` metódus az eszköz regisztrációjának frissítését. Ezután frissítse az alkalmazásnak, hogy a legfrissebb hírekről szóló értesítések küldése közvetlenül magában az alkalmazásban.

## <a name="optional-send-tagged-notifications"></a>(nem kötelező) Címkézett értesítések küldése

Ha nincs hozzáférése a Visual Studióba, ugorjon a következő szakaszra, és értesítések küldése az alkalmazásból, magát. A megfelelő sablon értesítést is küldhet a [Azure Portal] az értesítési központ hibakeresési lapján.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(nem kötelező) Értesítések küldése az eszközről

Általában értesítéseket szeretne küldeni a háttérszolgáltatás által, de elküldheti a legfrissebb hírekről szóló értesítések közvetlenül az alkalmazásból. Ehhez frissítenie a `SendNotificationRESTAPI` , amelyet a megadott metódust az [Ismerkedés a Notification Hubs] [ get-started] oktatóanyag.

1. A `ViewController.m`, frissítse a `SendNotificationRESTAPI` módját követi, hogy a kategória címke olyan paramétereket fogadnak el, majd a megfelelő elküldése [sablon](notification-hubs-templates-cross-platform-push-messages.md) értesítést.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. A `ViewController.m`, frissítse a `Send Notification` művelet a következő kódban látható módon. Hogy az egyes címke használata külön-külön értesítéseket küld, majd elküldi a több platformra.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, GCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. A projekt újraépítéséhez, és ellenőrizze, hogy nincsenek fordítási hibákat.

## <a name="run-the-app-and-generate-notifications"></a>Az alkalmazás futtatása és értesítések létrehozása

1. A Futtatás gombra a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást. Válassza ki a feliratkozás, és nyomja le az legfrissebb hírek lehetőségek a **előfizetés** gombra. Megjelenik egy párbeszédpanel, jelezve, az értesítések már előfizetett.

    ![Példa értesítési iOS rendszeren][1]

    Ha úgy dönt **előfizetés**, az alkalmazás alakítja át a kiválasztott kategóriákba tartozó címkék és a egy új eszköz regisztrálása a kiválasztott címkék kér az értesítési központban.

2. Adjon meg egy üzenetet küldeni, majd nyomja le az legfrissebb hírek a **értesítés küldése** gombra. Alternatív megoldásként futtathatja a .NET-konzolalkalmazást az értesítések generálását.

    ![Az IOS-es értesítési beállítások módosítása][2]

3. A legfrissebb hírek az előfizetett minden eszköz megkapja a legfrissebb hírekről szóló értesítések az imént elküldött.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban, értesítéseket küldött az adott iOS-eszközök által regisztrált kategóriák. Megtudhatja, hogyan küldhet leküldéses értesítéseket a honosított, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Honosított leküldéses értesítések küldése](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
