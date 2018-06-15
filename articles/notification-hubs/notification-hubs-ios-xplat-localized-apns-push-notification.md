---
title: Leküldéses honosított értesítések küldése iOS-eszközök Azure Notification Hubs használatával |} Microsoft Docs
description: Ismerje meg a honosított leküldéses értesítések küldéséhez iOS-eszközök használata az Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776498"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses honosított értesítések küldése iOS-eszközök Azure Notification Hubs használatával 
> [!div class="op_single_selector"]
> * [Windows áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

Az oktatóanyag bemutatja, hogyan használható a [sablonok](notification-hubs-templates-cross-platform-push-messages.md) az Azure Notification Hubs – legfrissebb híreket tartalmazó értesítések nyelv és az eszköz honosított katalóguselemekről adás szolgáltatása. Ebben az oktatóanyagban a kiindulási pont az iOS-alkalmazás létrehozása [legfrissebb hírek küldése Notification Hubs használata]. Amikor végzett, kategóriák szeretné regisztrálni, adja meg a nyelvet, amelyen az értesítéseket, és csak a kiválasztott kategóriákra leküldéses értesítéseket kap ezen a nyelven.

Ez a forgatókönyv két részből áll:

* iOS-alkalmazás tesz lehetővé az nyelv megadása, és fizessen elő a különböző breaking news kategóriák;
* A háttér-közzéteszi az értesítéseket, a **címke** és **sablon** Azure Notification Hubs funkcióit.

Ebben az oktatóanyagban tegye a következőket:

> [!div class="checklist"]
> * Frissítés az alkalmazás felhasználói felülete
> * Az iOS-alkalmazás létrehozása
> * Honosított sablon értesítések küldése .NET-Konzolalkalmazás
> * Az eszköz honosított sablonhoz értesítésküldést


## <a name="overview"></a>Áttekintés
A [legfrissebb hírek küldése Notification Hubs használata], amellyel az alkalmazás a beépített **címkék** előfizetés az értesítésekre hírek különböző kategóriákban. Számos alkalmazás, azonban több piacok célként, és honosítási igényelnek. Ez azt jelenti, hogy a tartalom értesítést maguknak kell honosított és kívánt eszközök beküldeni. Az oktatóanyag bemutatja, hogyan használható a **sablon** könnyen képes biztosítani a honosított legfrissebb híreket tartalmazó értesítések a Notification Hubs szolgáltatása.

> [!NOTE]
> Egy honosított értesítések küldéséhez módja az egyes címkék több verzióját. Például angol, francia és Mandarin támogatásához lenne szüksége három különböző címkék híreket: "world_en", "world_fr" és "world_ch". Kell küldeni a híreket honosított verzióját az egyes ezekkel a címkékkel. Ebben a témakörben a sablonok a címkék elterjedése és több üzenetet küldeni a követelmény elkerülése érdekében használhat.

Magas szinten sablonok, amelyek egy adja meg, hogy egy adott eszközhöz egy értesítést kell kapnia. A sablon a pontos az adattartalom formátuma az app-háttér által küldött üzenet részét képező tulajdonságok alapján határozza meg. Az Ön esetében minden támogatott nyelven tartalmazó területibeállítás-független üzenetet küldünk:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ezt követően, győződjön meg arról, hogy az eszközök regisztrálása a sablont, amely a megfelelő tulajdonság hivatkozik. Például egy iOS-alkalmazást, amely a francia hírek regisztrálni szeretne regisztrálja a következő szintaxis használatával:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

A sablonok további információkért lásd: [sablonok](notification-hubs-templates-cross-platform-push-messages.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

- Fejezze be a [leküldéses értesítések küldése adott iOS-eszközök](notification-hubs-ios-xplat-segmented-apns-push-notification.md) oktatóanyagot, és a kód érhető el, mert ez az oktatóanyag közvetlenül épít, hogy a kód.
- A Visual Studio 2012 vagy újabb nem kötelező megadni.

## <a name="update-the-app-user-interface"></a>Frissítés az alkalmazás felhasználói felülete
Ebben a szakaszban a következő témakör létrehozott Megtörje hírek alkalmazásra módosítása [legfrissebb hírek küldése Notification Hubs használata] küldése honosított a legfrissebb hírek sablonok használatával.

A három nyelvek valamelyikével szegmentált vezérlőt hozzá a MainStoryboard_iPhone.storyboard: angol, francia és Mandarin.

![][13]

Ezután győződjön meg arról, ha szeretne hozzáadni egy IBOutlet a ViewController.h. a következő ábrán látható módon:

![][14]

## <a name="build-the-ios-app"></a>Az iOS-alkalmazás létrehozása
1. A Notification.h adja hozzá a *retrieveLocale* metódust, és módosítani az adattárolót, és fizessen elő a módszereket az alábbi kódban látható módon:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    A Notification.m, módosítsa a *storeCategoriesAndSubscribe* metódus hozzáadásával a területi beállítással rendelkezik, és tárolja őket a felhasználók alapértelmezett beállításait:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Módosítsa a *előfizetés* metódus területi beállításokat tartalmazza:
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Most, hogy módosította az értesítések osztály, akkor győződjön meg arról, hogy a ViewController lehetővé teszi az új UISegmentControl használatát. Adja hozzá a következő sort a *viewDidLoad* metódus ügyeljen arra, hogy a jelenleg kiválasztott területi beállítás megjelenítése:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Ezt követően a a *előfizetés* módszer, módosítsa a hívás a *storeCategoriesAndSubscribe* kód az alábbi:
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. Végül, frissítenie kell a *didRegisterForRemoteNotificationsWithDeviceToken* a AppDelegate.m metódust, hogy helyesen, is frissítheti a regisztrációt, az alkalmazás indításakor. Módosítsa a hívás a *előfizetés* metódus az értesítések a következő kóddal:
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(választható) Honosított sablon értesítések küldése .NET-Konzolalkalmazás
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(választható) Az eszköz honosított sablonhoz értesítésküldést
Ha nem rendelkezik hozzáféréssel a Visual Studio, vagy csak a közvetlenül az alkalmazásból az eszközön a honosított sablon értesítések küldésének teszteléséhez. A honosított sablon paramétereihez is hozzáadhat a `SendNotificationRESTAPI` az előző oktatóanyag meghatározott metódus.

    ```obj-c
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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

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


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban honosított értesítéseket az iOS-eszközre küldött. Leküldéses értesítések küldése iOS-alkalmazások bizonyos felhasználók beállításáról előzetes következő oktatóanyagot: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználókra](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[legfrissebb hírek küldése Notification Hubs használata]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
