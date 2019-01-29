---
title: Leküldéses honosított értesítések küldése iOS-eszközök Azure Notification Hubs használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használható a honosított leküldéses értesítések küldése iOS-eszközök Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: eef3f153844be00d0338aa98b8aba21c5b749e46
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094223"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Oktatóanyag: Honosított leküldéses értesítések küldése iOS-eszközök Azure Notification Hubs használatával

> [!div class="op_single_selector"]
> * [Windows Áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Az oktatóanyag bemutatja, hogyan használható a [sablonok](notification-hubs-templates-cross-platform-push-messages.md) honosított nyelvét és az eszköz által legfrissebb hírekről szóló értesítések küldhetők az Azure Notification hubs szolgáltatás. Ebben az oktatóanyagban először az iOS-alkalmazás létrehozott [A legfrissebb hírek elküldése a Notification Hubs használatával]. Amikor végzett, regisztráljon az Önt érdeklő kategóriák, adja meg a nyelvet, amelyen az értesítések fogadásához és a kiválasztott kategóriákra csak leküldéses értesítések fogadása az adott nyelveken.

Ebben a forgatókönyvben két részből áll:

* iOS-alkalmazást tesz lehetővé az válassza ki a nyelvet, és különböző breaking news kategóriák; előfizetés
* A háttér-közzéteszi az értesítéseket, használja a **címke** és **sablon** Azure Notification Hubs funkcióit.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Az alkalmazás felhasználói felület frissítéséhez
> * Az iOS-alkalmazás készítése
> * A sablon honosított értesítések küldése a .NET-Konzolalkalmazás
> * A sablon honosított értesítések küldése az eszközről

## <a name="overview"></a>Áttekintés

A [A legfrissebb hírek elküldése a Notification Hubs használatával], egy alkalmazást, amelyet használni létrehozott **címkék** előfizetés az értesítésekre, a másik hírkategóriák. Számos alkalmazás, azonban több piacok cél és honosítási igényelnek. Ez azt jelenti, hogy a tartalom értesítést maguknak kell honosított és az eszközök a megfelelő csoporthoz-i. Az oktatóanyag bemutatja, hogyan használható a **sablon** könnyen biztosításához honosított legfrissebb hírekről szóló értesítések a Notification hubs szolgáltatás.

> [!NOTE]
> Honosított értesítések küldése az egyik lehetőség, hogy minden címke több verzióját. Például angol, francia és Mandarin támogatásához kell három különböző címkék a globális hírekkel: "world_en", "world_fr", és a "world_ch". Majd kell a globális hírekkel honosított változatát el egyes ezekkel a címkékkel. Ez a témakör a sablonok címkék elterjedése és több üzenetet küld az megkövetelését elkerülése érdekében használhatja.

Magas szinten sablonok, amelyek egy adja meg, hogy egy adott eszközhöz egy értesítést kell kapnia. A sablon meghatározza a hasznos adatok pontos formátumát a háttéralkalmazás által küldött üzenet részét képező tulajdonságokra hivatkozva. Abban az esetben amely tartalmazza az összes támogatott nyelvet területibeállítás-agnosztikus üzenet küldése:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Ezután, győződjön meg arról, hogy az eszközök regisztrálás egy sablont, amely a megfelelő tulajdonságra hivatkozik. Például az iOS-alkalmazás, amely regisztrálni szeretne a francia hírek regisztrálja a következő szintaxis használatával:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

A sablonok további információkért lásd: [sablonok](notification-hubs-templates-cross-platform-push-messages.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a [leküldéses értesítések küldése az adott iOS-eszközök](notification-hubs-ios-xplat-segmented-apns-push-notification.md) oktatóanyag és van elérhető, a kódot, mert ebben az oktatóanyagban közvetlenül épít, hogy a kódot.
* A Visual Studio 2017 nem kötelező.

## <a name="update-the-app-user-interface"></a>Az alkalmazás felhasználói felület frissítéséhez

Ebben a szakaszban módosítsa a témakörben létrehozott híreket használhatatlanná tévő alkalmazást [A legfrissebb hírek elküldése a Notification Hubs használatával] honosított sablonok használatával a legfrissebb hírek elküldése.

Az a `MainStoryboard_iPhone.storyboard`, a három nyelvekkel szegmentált vezérlőelem felvétele: Angol, francia és Mandarin.

![Az iOS felhasználói felület storyboard létrehozása][13]

Végezze el a ViewController.h adjon hozzá egy IBOutlet, az alábbi képen látható módon:

![Értékesítési lehetőségek, a kapcsolók létrehozása][14]

## <a name="build-the-ios-app"></a>Az iOS-alkalmazás készítése

1. Az a `Notification.h` adja hozzá a `retrieveLocale` metódust, és a tároló módosítása, és feliratkozhat a módszereket, az alábbi kódban látható módon:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Az a `Notification.m`, módosítsa a `storeCategoriesAndSubscribe` metódus hozzáadásával a `locale` paramétert, és menti a felhasználók alapértelmezett beállításait:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Majd módosíthatja a *előfizetés* metódus a területi kódot tartalmazza:

    ```objc
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

    A módszer használatát `registerTemplateWithDeviceToken`, hanem `registerNativeWithDeviceToken`. Amikor regisztrál egy sablont, meg kell adnia a json-sablont, és a sablon nevét (Előfordulhat, hogy az alkalmazás szeretne regisztrálni, eltérő sablonokban). Ügyeljen arra, hogy címkéket, mint a kategóriák regisztrálni, ügyeljen arra, hogy ezeket a hírek értesítéseket fogadni szeretné.

    Adjon meg egy metódust a területi beállítások lekérése a felhasználói alapértelmezett beállításokat:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Most, hogy módosította a `Notifications` osztály rendelkezik, győződjön meg arról, hogy a `ViewController` alkalmazza az új `UISegmentControl`. Adja hozzá a következő sort a a `viewDidLoad` metódust, hogy a jelenleg kiválasztott területi beállítás megjelenítéséhez:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ezt követően a a `subscribe` metódus, a hívás a módosítása a `storeCategoriesAndSubscribe` a következő kódot:

    ```objc
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

3. Végül, frissítenie kell a `didRegisterForRemoteNotificationsWithDeviceToken` metódus az AppDelegate.m az, hogy megfelelően frissítse a regisztrációt az alkalmazás indításakor. A hívás a módosítása a `subscribe` metódus az értesítések a következő kóddal:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(nem kötelező) A sablon honosított értesítések küldése a .NET-Konzolalkalmazás

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(nem kötelező) A sablon honosított értesítések küldése az eszközről

Ha nem fér hozzá a Visual Studióban, vagy csak szeretné tesztelni a sablon honosított értesítések küldése közvetlenül az alkalmazásból az eszközön. A honosított sablon paramétereihez is hozzáadhat a `SendNotificationRESTAPI` metódus az előző oktatóanyagban meghatározott.

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

Ebben az oktatóanyagban elküldött honosított értesítések iOS-eszközökön. Megtudhatja, hogyan küldhet leküldéses értesítéseket az iOS-alkalmazások meghatározott felhasználókra, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
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
