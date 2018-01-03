---
title: "Értesítési központok honosított Megtörje hírek oktatóanyag iOS-hez"
description: "Útmutató: Azure Service Bus Notification Hubs használatával küldése honosított legfrissebb híreket tartalmazó értesítések (iOS)."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: fd2b7d9dfd4f432bbcbaa3ed76f8bec0b9677e17
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>IOS-eszközök honosított legfrissebb hírek küldése Notification Hubs használatával
> [!div class="op_single_selector"]
> * [Windows áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan használható a [sablonok](notification-hubs-templates-cross-platform-push-messages.md) az Azure Notification Hubs – legfrissebb híreket tartalmazó értesítések nyelv és az eszköz honosított katalóguselemekről adás szolgáltatása. Ebben az oktatóanyagban az iOS-alkalmazás létrehozása az megkezdése [legfrissebb hírek küldése Notification Hubs használata]. Amikor végzett, lesz kategóriák szeretné regisztrálni, adja meg a nyelvet, amelyen az értesítéseket, és csak a kiválasztott kategóriákra leküldéses értesítéseket kapni az adott nyelveken.

Ez a forgatókönyv két részből áll:

* iOS-alkalmazás tesz lehetővé az nyelv megadása, és fizessen elő a különböző breaking news kategóriák;
* a háttér-közzéteszi az értesítéseket, a **címke** és **sablon** az Azure Notification Hubs feautres.

## <a name="prerequisites"></a>Előfeltételek
Már végrehajtotta a [legfrissebb hírek küldése Notification Hubs használata] oktatóanyagot, és a kód érhető el, mert ez az oktatóanyag közvetlenül épít, hogy a kód.

A Visual Studio 2012 vagy újabb nem kötelező megadni.

## <a name="template-concepts"></a>Sablon fogalmak
A [legfrissebb hírek küldése Notification Hubs használata] olyan alkalmazás, amelynek használt parancsfájlkezelő **címkék** előfizetés az értesítésekre hírek különböző kategóriákban.
Számos alkalmazás, azonban több piacok célként, és honosítási igényelnek. Ez azt jelenti, hogy a tartalom maguk értesítést kell honosított és kívánt eszközök beküldeni.
Ebben a témakörben bemutatjuk, hogyan használható a **sablon** könnyen képes biztosítani a honosított legfrissebb híreket tartalmazó értesítések a Notification Hubs szolgáltatása.

Megjegyzés: egy honosított értesítések küldéséhez módja az egyes címkék több verzióját. Például angol, francia és Mandarin támogatásához lenne szükséges három különböző címkék híreket: "world_en", "world_fr" és "world_ch". A Microsoft majd kellene elküldeni a híreket honosított verzióját az egyes ezekkel a címkékkel. Ebben a témakörben a sablonok a címkék elterjedése és több üzenetet küldeni a követelmény elkerülése érdekében használjuk.

Magas szinten sablonok, amelyek egy adja meg, hogy egy adott eszközhöz egy értesítést kell kapnia. A sablon a pontos az adattartalom formátuma az app-háttér által küldött üzenet részét képező tulajdonságok alapján határozza meg. Ebben az esetben az összes támogatott nyelvek tartalmazó területibeállítás-független üzenetet küldünk:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ezután azt fogja győződjön meg arról, hogy az eszközök regisztrálása a sablont, amely a megfelelő tulajdonság hivatkozik. Például egy iOS-alkalmazást, amely a francia hírek regisztrálni szeretne regisztrálni fogja a következő:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Sablonok egyik újdonsága nagyon hatékony többet is megtudhat arról a a [sablonok](notification-hubs-templates-cross-platform-push-messages.md) cikk.

## <a name="the-app-user-interface"></a>Az alkalmazás felhasználói felülete
A Microsoft most módosítja a Megtörje hírek alkalmazást, amely létrehozta a következő témakör [legfrissebb hírek küldése Notification Hubs használata] küldése honosított a legfrissebb hírek sablonok használatával.

A három nyelv, amely támogatjuk szegmentált vezérlőt hozzá a MainStoryboard_iPhone.storyboard: angol, francia és Mandarin.

![][13]

Ezután győződjön meg arról, ha szeretne hozzáadni egy IBOutlet a ViewController.h. alább látható módon:

![][14]

## <a name="building-the-ios-app"></a>Az iOS-alkalmazás létrehozása
1. A Notification.h adja hozzá a *retrieveLocale* metódust, és módosítani az adattárolót és módszerek előfizetés alább látható módon:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    A Notification.m, módosítsa a *storeCategoriesAndSubscribe* metódus hozzáadásával a területi beállítással rendelkezik, és tárolja őket a felhasználók alapértelmezett beállításait:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
   
    Módosítsa a *előfizetés* metódus területi beállításokat tartalmazza:
   
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
   
    Vegye figyelembe, hogyan most használjuk a metódus *registerTemplateWithDeviceToken*, ahelyett, hogy *registerNativeWithDeviceToken*. Ha a sablon regisztráljuk azt kell adnia a json-sablon, és a sablon nevét (az alkalmazás szeretne regisztrálni, eltérő sablonokban). Ügyeljen arra, hogy a kategóriák regisztrálható címkék, mint azt szeretnénk, ügyeljen arra, hogy az adott hírek notifciations fogadására.
   
    A területi beállítás beolvasni a felhasználói alapértelmezett beállításokat egy olyan metódus hozzáadása:
   
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
2. Most, hogy az értesítések osztály módosítja azt, győződjön meg arról, hogy a ViewController lehetővé teszi az új UISegmentControl felhasználása van. Adja hozzá a következő sort a *viewDidLoad* metódus ügyeljen arra, hogy a jelenleg kiválasztott területi beállítás megjelenítése:
   
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
   
    Ezt követően a a *előfizetés* módszer, módosítsa a hívás a *storeCategoriesAndSubscribe* a következők:
   
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
3. Végül, frissítenie kell a *didRegisterForRemoteNotificationsWithDeviceToken* a AppDelegate.m metódust, hogy helyesen, is frissítheti a regisztrációt, az alkalmazás indításakor. Módosítsa a hívás a *előfizetés* metódus az értesítések a következőre:
   
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(választható) .NET-Konzolalkalmazás honosított sablonhoz értesítésküldést.
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(választható) Az eszköz honosított sablonhoz értesítésküldést
Ha nem rendelkezik hozzáféréssel a Visual Studio, vagy csak a közvetlenül az alkalmazásból az eszközön a honosított sablon értesítések küldésének teszteléséhez.  Egyszerű is honosított sablon paramétereinek hozzáadásához a `SendNotificationRESTAPI` az előző oktatóanyag meghatározott metódus.

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




## <a name="next-steps"></a>További lépések
Sablonok használatával kapcsolatos további információkért lásd:

* [Értesítse a felhasználókat a Notification hubs használatával: ASP.NET]
* [Értesítse a felhasználókat a Notification hubs használatával: Mobile Services]

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[legfrissebb hírek küldése Notification Hubs használata]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Értesítse a felhasználókat a Notification hubs használatával: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Értesítse a felhasználókat a Notification hubs használatával: Mobile Services]: /manage/services/notification-hubs/notify-users
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
