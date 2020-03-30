---
title: Honosított leküldéses értesítések küldése az iOS-nek az Azure Értesítési központok használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja a leküldéses honosított értesítéseket az iOS-eszközökön az Azure Értesítési központok használatával.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72385661"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Oktatóanyag: Honosított leküldéses értesítések küldése az iOS-nek az Azure Értesítési központok használatával

> [!div class="op_single_selector"]
> * [Windows Áruház C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Notification Hubs [sablonfunkcióját](notification-hubs-templates-cross-platform-push-messages.md) a nyelv és az eszköz szerint honosított, legfrissebb hírek közvetítésére. Ebben az oktatóanyagban az Értesítési központok használata című ablakban létrehozott iOS-alkalmazással kezdi [a legfrissebb hírek küldését.] Ha elkészült, regisztrálhat az Önt érdeklő kategóriákra, megadhatja azt a nyelvet, amelyen az értesítéseket fogadni szeretné, és csak az adott nyelven kiválasztott kategóriákra vonatkozó leküldéses értesítéseket kaphat.

Ennek a forgatókönyvnek két része van:

* Az iOS alkalmazás lehetővé teszi az ügyféleszközök számára, hogy megadjanak egy nyelvet, és feliratkozzanak a különböző breaking news kategóriákra;
* A háttérrendszer az Azure Notification Hubs **címke-** és **sablonfunkcióinak** használatával közvetíti az értesítéseket.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Az alkalmazás felhasználói felületének frissítése
> * Az iOS-alkalmazás létrehozása
> * Honosított sablonértesítések küldése a .NET konzolalkalmazásból
> * Honosított sablonértesítések küldése az eszközről

## <a name="overview"></a>Áttekintés

Az [Értesítési központok használata a legfrissebb hírek küldéséhez]olyan alkalmazást hozott fel, amely **címkékkel** fizetett elő a különböző hírkategóriák értesítéseire. Számos alkalmazás azonban több piacot céloz meg, és lokalizációt igényel. Ez azt jelenti, hogy maguknak az értesítéseknek a tartalmát honosítani kell, és a megfelelő eszközcsoportba kell szállítani. Ez az oktatóanyag bemutatja, hogyan használhatja az Értesítési központok **sablonfunkcióját** a honosított legfrissebb hírekről szóló értesítések egyszerű kézbesítéséhez.

> [!NOTE]
> A honosított értesítések küldésének egyik módja, ha az egyes címkékből több verziót hoz létre. Például az angol, a francia és a mandarin nyelv támogatásához három különböző címkére van szükség a világhírműsorokhoz: "world_en", "world_fr" és "world_ch". Ezután el kell küldenie a világhírek honosított verzióját mindegyik címkére. Ebben a témakörben sablonokat használ a címkék elterjedésének és a több üzenet küldésének követelményének elkerülésére.

A sablonok segítségével megadhatja, hogy egy adott eszköz hogyan kapjon értesítést. A sablon meghatározza a hasznos adatok pontos formátumát a háttéralkalmazás által küldött üzenet részét képező tulajdonságokra hivatkozva. Az Ön esetében egy területi-független üzenetet küld, amely tartalmazza az összes támogatott nyelvet:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Ezután győződjön meg arról, hogy az eszközök regisztrálnak egy sablont, amely a megfelelő tulajdonságra hivatkozik. Például egy iOS-alkalmazás, amely a következő szintaxissal szeretne regisztrálni a francia hírjegyzékekbe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

A sablonokról a Sablonok ról szóló további [tudnivalókat](notification-hubs-templates-cross-platform-push-messages.md) a Sablonok című cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a [leküldéses értesítéseket az adott iOS-eszközök](notification-hubs-ios-xplat-segmented-apns-push-notification.md) bemutató, és a kód áll rendelkezésre, mert ez az oktatóanyag épül közvetlenül a kódot.
* A Visual Studio 2019 nem kötelező.

## <a name="update-the-app-user-interface"></a>Az alkalmazás felhasználói felületének frissítése

Ebben a szakaszban módosíthatja a Breaking News alkalmazást, amelyet a témakörben létrehozott Az értesítési központok használata című témakörben [hozott létre, hogy friss híreket küldjön] a helyi legfrissebb hírek sablonok használatával történő küldéséhez.

A `MainStoryboard_iPhone.storyboard`hozzá, adjon hozzá egy szegmentált vezérlőt a három nyelvhez: angol, francia és mandarin.

![Az iOS felhasználói felületének történetének létrehozása][13]

Ezután győződjön meg róla, hogy iBOutlet-et ad hozzá a ViewController.h webhelyhez az alábbi képen látható módon:

![A kapcsolók aljzatainak létrehozása][14]

## <a name="build-the-ios-app"></a>Az iOS-alkalmazás létrehozása

1. A `Notification.h`alkalmazásban adja `retrieveLocale` hozzá a módszert, és módosítsa a tároló- és előfizetési módszereket a következő kód szerint:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    A `Notification.m`alkalmazásban módosítsa `storeCategoriesAndSubscribe` a metódust `locale` a paraméter hozzáadásával és a felhasználói alapértelmezésekben való tárolásával:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ezután módosítsa az *előfizetési* módszert, hogy tartalmazza a területi beállításokat:

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

    A helyett `registerTemplateWithDeviceToken`a módszert `registerNativeWithDeviceToken`használja. Amikor regisztrál egy sablont, meg kell adnia a json sablont és a sablon nevét is (mivel az alkalmazás különböző sablonokat szeretne regisztrálni). Győződjön meg arról, hogy a kategóriákat címkeként regisztrálja, mivel biztosítani szeretné, hogy megkapja a hírekről szóló értesítéseket.

    Adjon hozzá egy módszert a területi beállítás nak a felhasználó alapértelmezett beállításaiból történő lekéréséhez:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Most, hogy `Notifications` módosította az osztályt, `ViewController` meg kell győződnie arról, hogy az új `UISegmentControl`. Adja hozzá a `viewDidLoad` következő sort a metódushoz, hogy biztosan megjelenjen az aktuálisan kijelölt területi beállítás:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ezután a `subscribe` módszerben módosítsa a `storeCategoriesAndSubscribe` hívást a következő kódra:

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

3. Végül frissítenie kell `didRegisterForRemoteNotificationsWithDeviceToken` a módszert az AppDelegate.m-ben, hogy az alkalmazás indításakor megfelelően frissíthesse a regisztrációt. Változtassa meg hívását az `subscribe` értesítések metódusára a következő kóddal:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(nem kötelező) Honosított sablonértesítések küldése a .NET konzolalkalmazásból

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(nem kötelező) Honosított sablonértesítések küldése az eszközről

Ha nincs hozzáférése a Visual Studióhoz, vagy csak tesztelheti a honosított sablonértesítések elküldését közvetlenül az eszközön lévő alkalmazásból. Hozzáadhatja a honosított sablon `SendNotificationRESTAPI` paramétereit az előző oktatóanyagban megadott metódushoz.

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

Ebben az oktatóanyagban honosított értesítéseket küldött az iOS-eszközökre. Ha meg szeretné tudni, hogyan küldhet értesítéseket az iOS-alkalmazások bizonyos felhasználóinak, olvassa el a következő oktatóanyagot:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[A legfrissebb hírek elküldése a Notification Hubs használatával]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
