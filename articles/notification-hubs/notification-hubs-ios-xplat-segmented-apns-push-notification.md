---
title: Leküldéses értesítések küldése adott iOS-eszközökre az Azure Értesítési központok használatával | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Értesítési központok leküldéses értesítéseket adott iOS-eszközökre.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80126998"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése adott iOS-eszközökre az Azure Értesítési központok használatával

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Értesítési központokat a legfrissebb hírekről szóló értesítések iOS-alkalmazásokba történő közvetítéséhez. Ha elkészült, regisztrálhat az Önt érdeklő rendkívüli hírkategóriákra, és csak leküldéses értesítéseket kaphat ezekről a kategóriákról. Ezt a forgatókönyvet gyakran használják olyan alkalmazásokban, ahol az értesítéseket egy adott téma iránt korábban érdeklődő felhasználók csoportjainak kell elküldeni. Ilyen lehet például egy RSS-olvasó, a zenerajongóknak készült alkalmazások stb.

A közvetítési forgatókönyveket úgy lehet engedélyezni, ha az értesítési központban a regisztráció létrehozásakor hozzáad egy vagy több *címkét*. Amikor értesítéseket küld egy címkének, a címkére regisztrált eszközök megkapják az értesítést. A címkék egyszerű sztringek, nem szükséges őket előzetesen kiosztani. További információ a címkékről: [Útválasztás és címkekifejezések az értesítési központokban](notification-hubs-tags-segment-push-message.md).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Kategóriakijelölés hozzáadása az alkalmazáshoz
> * Címkézett értesítések küldése
> * Értesítések küldése az eszközről
> * Az alkalmazás futtatása és értesítések létrehozása

## <a name="prerequisites"></a>Előfeltételek

Ez a témakör az oktatóanyagban létrehozott alkalmazásra [épül: Leküldéses értesítések az Azure Notification Hubs használatával iOS-alkalmazásokba.][get-started] Az oktatóanyag megkezdése előtt már el kell [végeznie az oktatóanyagot: Leküldéses értesítések az Azure Notification Hubs használatával iOS-alkalmazásokba.][get-started]

## <a name="add-category-selection-to-the-app"></a>Kategóriaválasztó hozzáadása az alkalmazáshoz

Az első lépés az, hogy adja hozzá a felhasználói felület elemeit a meglévő történet, amely lehetővé teszi a felhasználó számára, hogy válassza ki a kategóriákat regisztrálni. A felhasználó által kiválasztott kategóriákat az eszköz tárolja. Az alkalmazás indításakor egy eszközregisztráció jön létre az értesítési központban, amely címkeként tartalmazza a választott kategóriákat.

1. A **MainStoryboard_iPhone.storyboardban** adja hozzá a következő összetevőket az objektumtárból:

   * A címke a "Breaking News" szöveget,
   * "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Kategóriájánként hat kapcsoló, kategóriánként egy kapcsoló beállításával minden **kapcsolóállapot** alapértelmezés szerint **ki van kapcsolva.**
   * Egy gomb "Feliratkozás" címkével

     A történetnek a következőképpen kell kinéznie:

     ![Xcode illesztő készítő][3]

2. A segédszerkesztőben hozzon létre üzleteket az összes kapcsolóhoz, és hívja őket "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Hozzon létre egy `subscribe`műveletet a gomb neve; a `ViewController.h` következő kódot kell tartalmaznia:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Hozzon létre egy új `Notifications` **Cocoa Touch osztály** nevű . Másolja a következő kódot az Notifications.h fájl felületszakaszába:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adja hozzá a következő importálási irányelvet az értesítésekhez:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Másolja a következő kódot az Értesítések.m fájl implementációs szakaszába.

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

    Ez az osztály helyi tárolót használ az eszköz által fogadott hírek kategóriáinak tárolására és lekérésére. Emellett tartalmaz egy módszert, amivel regisztrálhat ezekhez a kategóriákhoz [egy sablonregisztráció](notification-hubs-templates-cross-platform-push-messages.md) használatával.

7. A `AppDelegate.h` fájlban adjon hozzá `Notifications.h` egy importálási utasítást, `Notifications` és adjon hozzá egy tulajdonságot az osztály egy példányához:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. A `didFinishLaunchingWithOptions` metódusban `AppDelegate.m`adja hozzá a kódot az értesítési példány inicializálásához a metódus elején.  
    `HUBNAME`és `HUBLISTENACCESS` (a `hubinfo.h`következőben definiálva) `<connection string with listen access>` már rendelkeznie kell a és a `<hub name>` helyőrzőkkel az értesítési központ nevével és a *DefaultListenSharedAccessSignature* korábban beszerzett kapcsolati karakterláncával.

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Mivel az ügyfélalkalmazással terjesztett hitelesítő adatok általában nem biztonságosak, csak a figyelési hozzáférés kulcsát terjessze az ügyfélalkalmazással. A figyelési hozzáférés lehetővé teszi, hogy az alkalmazás regisztráljon értesítésekre, a meglévő regisztrációkat azonban nem lehet módosítani, és értesítéseket sem lehet küldeni. A teljes körű hozzáférési kulcsot egy biztonságos háttérszolgáltatásban használja a rendszer értesítések kiküldésére és a meglévő regisztrációk módosítására.

9. A `didRegisterForRemoteNotificationsWithDeviceToken` metódusban `AppDelegate.m`cserélje le a metódusban lévő kódot a következő `notifications` kóddal, hogy az eszköz tokenje átmenjen az osztálynak. Az `notifications` osztály elvégzi a kategóriákkal rendelkező értesítések regisztrálását. Ha a felhasználó módosítja a `subscribeWithCategories` kategóriakijelöléseket, hívja meg a metódust az **előfizetés gombra** válaszul a frissítésükhöz.

    > [!NOTE]
    > Mivel az Apple Leküldéses értesítési szolgáltatás (APNS) által hozzárendelt eszközjogkivonat bármikor változhat, az értesítési hibák elkerülése érdekében gyakran regisztrálnia kell az értesítésekre. Ebben a példában a rendszer az alkalmazás minden egyes indításakor regisztrál az értesítésekre. A gyakran, naponta egynél többször futtatott alkalmazások esetén a sávszélesség megőrzése érdekében akár ki is hagyhatja a regisztrációt, ha kevesebb mint egy nap telt el az előző regisztráció óta.

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

    Ezen a ponton nincs más kód `didRegisterForRemoteNotificationsWithDeviceToken` a metódusban.

10. A következő módszerek már `AppDelegate.m` jelen kell lennie a befejezéskor az Első lépések az [értesítési központok][get-started] oktatóanyag. Ha nem, add hozzá őket.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Ez a módszer egy egyszerű **UIAlert**megjelenítésével kezeli az alkalmazás futása során kapott értesítéseket.

11. A `ViewController.m`alkalmazásban `import` adjon `AppDelegate.h` hozzá egy utasítást, és másolja `subscribe` a következő kódot az XCode által létrehozott metódusba. Ez a kód frissíti az értesítési regisztrációt, hogy a felhasználó által a felhasználói felületen kiválasztott új kategóriacímkéket használja.

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
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Ez a `NSMutableArray` módszer létrehoz egy `Notifications` kategóriákat, és az osztály segítségével tárolja a listát a helyi tárolóban, és regisztrálja a megfelelő címkéket az értesítési központhoz. A kategóriák módosításakor a rendszer újra létrehozza a regisztrációt az új kategóriákkal.

12. A `ViewController.m`alkalmazásban adja hozzá `viewDidLoad` a következő kódot a metódushoz, hogy a felhasználói felületet a korábban mentett kategóriák alapján állítsa be.

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

Az alkalmazás most már tárolhatja a kategóriák egy sor a helyi tároló regisztrálni az értesítési központ, amikor az alkalmazás elindul. A felhasználó módosíthatja a kategóriák kiválasztását futásidőben, és kattintson az `subscribe` eszköz regisztrációjának frissítésére szolgáló módszerre. Ezután frissítse az alkalmazást, hogy a legfrissebb híreket közvetlenül az alkalmazásban küldje el.

## <a name="optional-send-tagged-notifications"></a>(nem kötelező) Címkézett értesítések küldése

Ha nem fér hozzá a Visual Studio-hoz, ugorjon a következő szakaszra, és küldjön értesítéseket magából az alkalmazásból. A megfelelő sablonértesítést az [Azure Portalról] is elküldheti az értesítési központ hibakeresési fülével.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(nem kötelező) Értesítések küldése az eszközről

Az értesítéseket általában egy háttérszolgáltatás küldi el, de a legfrissebb híreket közvetlenül az alkalmazásból is elküldheti. Ehhez frissítse az `SendNotificationRESTAPI` Értesítési központok első lépései oktatóanyagban definiált [metódust.][get-started]

1. A `ViewController.m`alkalmazásban `SendNotificationRESTAPI` frissítse a metódust az alábbiak szerint, hogy elfogadja a kategóriacímke paraméterét, és elküldi a megfelelő [sablonértesítést.](notification-hubs-templates-cross-platform-push-messages.md)

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

2. A `ViewController.m`alkalmazásban `Send Notification` frissítse a műveletet a következő kódban látható módon. Annak érdekében, hogy az értesítéseket az egyes címkek használatával külön-külön küldi el, és több platformra küldi.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Építse újra a projektet, és győződjön meg arról, hogy nincsenek építési hibák.

## <a name="run-the-app-and-generate-notifications"></a>Az alkalmazás futtatása és értesítések létrehozása

1. Nyomja le a Run (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához. Válassza ki az előfizetéshez rendelkezésre álló néhány rendkívüli hírlehetőséget, majd nyomja meg az **Előfizetés** gombot. Meg kell jelennie egy párbeszédpanelnek, amely jelzi, hogy az értesítésekre feliratkoztak.

    ![Példa értesítés iOS-en][1]

    Ha az **Előfizetés**lehetőséget választja, az alkalmazás a kijelölt kategóriákat címkékké alakítja, és új eszközregisztrációt kér a kijelölt címkékhez az értesítési központból.

2. Írjon be egy üzenetet, amelyet friss hírként szeretne elküldeni, majd nyomja meg az **Értesítés küldése** gombot. Másik lehetőségként futtassa a .NET konzolalkalmazást az értesítések létrehozásához.

    ![Értesítési beállítások módosítása az iOS rendszerben][2]

3. Minden olyan eszköz, amely feliratkozott a legfrissebb hírekre, megkapja az ön által küldött legfrissebb híreket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban szórásos értesítéseket küldött a kategóriákhoz regisztrált iOS-eszközökre. Ha meg szeretné tudni, hogyan kell leadni a honosított értesítéseket, olvassa el a következő oktatóanyagot:

> [!div class="nextstepaction"]
>[Honosított leküldéses értesítések küldése](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure-portál]: https://portal.azure.com
