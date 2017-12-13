---
title: "A Notification Hubs Breaking News oktatóanyag – iOS"
description: "Útmutató: Azure Service Bus Notification Hubs használatával legfrissebb híreket tartalmazó értesítések küldése iOS-eszközök."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 8aec171b46df3e0e7f2a2d3cc9d44084d064e6fd
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>A legfrissebb hírek elküldése a Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan szórási legfrissebb híreket tartalmazó értesítések iOS-alkalmazásokhoz Azure Notification Hubs használatával. Amikor végzett, akkor fog számára megtörje érdekli hírek kategóriák regisztrálni, és ezen kategóriák csak leküldéses értesítések fogadásához. Ebben a forgatókönyvben számos alkalmazás általános felépítését, ahol értesítések kell őket, például az RSS-olvasóval, az alkalmazások zene ventilátorok stb érdeklődik elemnek már deklarálva felhasználói csoportokat kell küldeni.

Szórási forgatókönyvek engedélyezve vannak, beleértve a következőket egy vagy több *címkék* regisztráció létrehozásakor az értesítési központban. Amikor a rendszer értesítéseket küld egy címkét, akkor a címke regisztrált minden eszköz a értesítést kap. Mivel a címkékkel egyszerűen csak karakterláncok, nem rendelkeznek előre kell építeni. Címkékkel kapcsolatos további információkért tekintse meg [Notification Hubs útválasztási és címke kifejezések](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör a létrehozott alkalmazás épül [Ismerkedés a Notification Hubs][get-started]. Az oktatóanyag elindítása előtt már végrehajtotta [Ismerkedés a Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Kategória kiválasztása hozzáadása az alkalmazáshoz
Az első lépés a felhasználói felületi elemek hozzáadása a meglévő storyboard, amelyek lehetővé teszik a felhasználó számára a kategóriák regisztrálni kívánt. A felhasználó által kiválasztott kategóriák tárolódnak az eszközön. Az alkalmazás indításakor a eszközregisztráció címkeként az értesítési központ, a kiválasztott kategóriákra jön létre.

1. A MainStoryboard_iPhone.storyboard adja hozzá a következő összetevőket az objektumtárból:
   
   * A címkék "Megtörje hírek" szóra,
   * Címkék kategória szöveget a "World", "Politika", "Vállalati", "Technológia", "Tudományos", "Sport",
   * Egy kategóriát, egy hat kapcsolók beállítása minden kapcsoló **állapot** kell **ki** alapértelmezés szerint.
   * Egy gomb "Előfizetés" címkével
     
     A storyboard fájlt a következőképpen kell kinéznie:
     
     ![][3]
2. A Segéd-szerkesztőben kimeneteket a kapcsolók létrehozása, és hívja meg őket "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Az "előfizetés" gombra a művelet létrehozása. A ViewController.h tartalmazniuk kell a következőket:
   
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
4. Hozzon létre egy új **Cocoa Touch osztály** nevű `Notifications`. Másolja az alábbi kódot a fájl Notifications.h felület részében:
   
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
5. Adja hozzá a következő importálási irányelv Notifications.m:
   
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
6. Másolja az alábbi kódot a fájl Notifications.m az implementation szakaszban.
   
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



    Ez az osztály tárolásához és lekéréséhez híreket az eszköz fogadó kategóriáinak helyi tárolást használ. Ezen kategóriák használatával regisztrálni egy metódust tartalmaz is, egy [sablon](notification-hubs-templates-cross-platform-push-messages.md) regisztrációs.

1. A AppDelegate.h fájlban adja hozzá az importálási utasítást Notifications.h, és az értesítések osztály egy példányának tulajdonság hozzáadása:
   
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
2. Az a **didFinishLaunchingWithOptions** metódus a AppDelegate.m, adja hozzá a kódot, a módszer az elején értesítések példány inicializálása.  
   
    `HUBNAME`és `HUBLISTENACCESS` (hubinfo.h-ban meghatározott) már rendelkezik a `<hub name>` és `<connection string with listen access>` helyőrzők helyett az értesítési központ nevére és a kapcsolati karakterláncot *DefaultListenSharedAccessSignature* korábban beszerzett
   
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
   
   > [!NOTE]
   > Eszközzel együtt egy ügyfélalkalmazás hitelesítő adatok nem általában biztonságos, mert csak kell terjeszteni a figyelési hozzáférési kulcs ügyfél alkalmazása. Figyeljen hozzáférés lehetővé teszi, hogy az alkalmazás regisztrálásához értesítések, de a meglévő regisztrációk nem módosítható, és értesítések nem küldhető el. A teljes körű hozzáférési kulcs értesítések küldését, és meglévő regisztrációk módosítása védett háttérszolgáltatás használatban.
   > 
   > 
3. Az a **didRegisterForRemoteNotificationsWithDeviceToken** metódus a AppDelegate.m, cserélje le a kód metódus a következő kódot az eszköz jogkivonatát átadása az értesítések osztály. Az értesítések osztály fogja elvégezni az értesítések küldése a kategóriák regisztrálásakor. A felhasználó megváltoztatja a kategória-beállításokat, ha hívása a `subscribeWithCategories` válaszul módszer a **előfizetés** gombra kattintva frissítse azokat.
   
   > [!NOTE]
   > Az eszköz jogkivonatát által az Apple Push Notification (APN) szolgáltatás hozzárendelt is alkalommal bármikor, mert az értesítések a notification hibák elkerülése érdekében gyakran kell regisztrálni. Ebben a példában regisztrál az értesítési minden alkalommal, az alkalmazás indítása. Gyakran futtatott alkalmazások esetén naponta csak egyszer, valószínűleg kihagyhatja regisztrációt, hogy a sávszélesség megőrzése, ha az előző regisztráció óta eltelt egy napnál.
   > 
   > 
   
        self.notifications.deviceToken = deviceToken;
   
        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.
   
        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Vegye figyelembe, hogy ezen a ponton kell nincs más kód a **didRegisterForRemoteNotificationsWithDeviceToken** metódust.

1. Az alábbi módszerek már befejezését AppDelegate.m jelen kell lenniük a [Ismerkedés a Notification Hubs] [ get-started] oktatóanyag.  Ha nem, adja hozzá.
   
    -(void) MessageBox:(NSString *) cím üzenet:(NSString *) ÜzenetSzövege {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * (a "void") alkalmazás:(UIApplication *) alkalmazás didReceiveRemoteNotification: (NSDictionary *) userInfo {NSLog (@"% @", userInfo);   [önkiszolgáló MessageBox:@"Notification" üzenetet: [[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];}
   
   Ez a metódus kezeli az alkalmazás futtatásakor egy egyszerű megjelenítésével fogadott értesítések **UIAlert**.
2. A ViewController.m, adjon hozzá egy importálási utasítást a AppDelegate.h, és másolja a következő kódot az XCode által létrehozott **előfizetés** metódust. Ez a kód frissíti az új, a felhasználó által választott, a felhasználói felületen kategória címkék használata az értesítési regisztráció.
   
       ```
       #import "Notifications.h"
       ```
   
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
   
   Ezzel a módszerrel hoz létre egy **NSMutableArray** kategóriák és használja a **értesítések** osztályra, hogy a lista tárolása a helyi tárolóhoz, és regisztrál az értesítési központban a megfelelő címkéket. Kategóriák módosításakor a regisztrációs újra létrejön az új kategóriák.
3. ViewController.m, adja hozzá a következő kódot a **viewDidLoad** beállítása a felhasználói felület módszer a korábban mentett kategóriák alapján.

        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Az alkalmazás most tárolhat kategóriák készlete eszköz helyi tárolójára regisztrálhatók az értesítési központban, amikor az alkalmazás indítása.  A felhasználó módosíthatja a kiválasztott kategóriák futásidejű, és kattintson a **előfizetés** módszer az eszköz regisztrációjának frissítését. A következő később frissíteni az alkalmazásnak, hogy a legfrissebb híreket tartalmazó értesítések küldése közvetlenül az alkalmazás funkcióit.

## <a name="optional-sending-tagged-notifications"></a>(választható) Címkézett értesítések küldése
Ha nem fér a Visual Studio, ugorjon a következő részre, és értesítések küldése az alkalmazás funkcióit. A megfelelő sablon értesítést is küldhet a [Azure-portálon] az értesítési központ hibakeresési lapján. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(választható) Értesítések küldése az eszköz
Általában akkor kell értesítéseket egy háttér-szolgáltatás, de az alkalmazásból közvetlenül is elküldheti a legfrissebb híreket tartalmazó értesítések. Ehhez a Microsoft frissíti a `SendNotificationRESTAPI` , hogy a megadott metódust a [Ismerkedés a Notification Hubs] [ get-started] oktatóanyag.

1. Frissítés a ViewController.m a `SendNotificationRESTAPI` módszert az követi, hogy a kategória címke egy paramétert fogad és küld a megfelelő [sablon](notification-hubs-templates-cross-platform-push-messages.md) értesítést.
   
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
2. Frissítés a ViewController.m a **értesítés küldése** művelet az alábbi kódban látható módon. Hogy azt az egyes címkék használatával külön-külön értesítések küldéséhez, és több platform küldeni.

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



1. A projekt újraépítéséhez, és győződjön meg arról, hogy nincs összeállítási hiba.

## <a name="run-the-app-and-generate-notifications"></a>Futtassa az alkalmazást, és értesítések
1. A Futtatás gombra a projekt felépítéséhez és az alkalmazás indításához. Adja meg néhány legfrissebb hírek beállításokat előfizetni, és nyomja le az **előfizetés** gombra. Az értesítések már előfizetett utaló üzenet jelenik meg.
   
    ![][1]
   
    Ha úgy dönt, **előfizetés**, az app alakítja át a kiválasztott kategóriákra címkék és a kijelölt címke egy új regisztrálásának kéri le az értesítési központban.
2. Adja meg az elküldött, legfrissebb hírek nyomja meg az üzenetet a **értesítés küldése** gombra. Alternatív megoldásként futtassa a .NET-Konzolalkalmazás értesítések létrehozásához.
   
    ![][2]
3. Minden egyes előfizetője a legfrissebb hírek lesz, elküldjük az imént a telefonjára küldött legfrissebb híreket tartalmazó értesítések.

## <a name="next-steps"></a>Következő lépések
Az oktatóanyag azt megtudta, hogyan kell közvetíteni legfrissebb hírek kategória szerint. Vegye figyelembe az alábbi oktatóanyagok számára más speciális Notification Hubs-forgatókönyvek közül befejezése:

* **[Honosított legfrissebb hírek szórási a Notification Hubs használatával]**
  
    Megtudhatja, hogyan bontsa ki a legfrissebb hírek app küldő honosított értesítések engedélyezéséhez.

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Honosított legfrissebb hírek szórási a Notification Hubs használatával]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure-portálon]: https://portal.azure.com
