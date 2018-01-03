---
title: "Biztonságos Azure Notification Hubs leküldéses"
description: "Megtudhatja, hogyan biztonságos leküldéses értesítések küldéséhez iOS-alkalmazásokhoz az Azure-ból. Kódminták Objective-C és C#."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: e5f09fb3716303bb21fe7442aa6fa8832174838e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Biztonságos Azure Notification Hubs leküldéses
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Leküldéses értesítési támogatása a Microsoft Azure lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúrában, ami jelentősen egyszerűsíti a leküldéses értesítések mobil platformokhoz fogyasztói, valamint a vállalati alkalmazások eléréséhez.

Szabályozó miatt vagy biztonsági korlátozások néha egy alkalmazás előfordulhat, hogy szeretne valamit az értesítés, amely a szabványos leküldéses értesítési infrastruktúrát keresztül nem lehet megadni. Ez az oktatóanyag ismerteti, hogyan által a biztonságos és hitelesített kapcsolatot az ügyféleszközön és a háttéralkalmazás keresztül érzékeny adatokat küld a felhasználói élmény eléréséhez.

Magas szinten a folyamat a következőképpen történik:

1. Az alkalmazás háttér:
   * Háttér-adatbázisban tárolja biztonságos hasznos.
   * Az értesítés Azonosítójának elküldi az eszköznek (nem biztonságos információk küldése).
2. Az alkalmazást az eszközön, az értesítés fogadása közben:
   * Az eszköz kapcsolatot létesít a háttér-kérő a biztonságos tartalom.
   * Az alkalmazás a tartalom megjeleníthető értesítésként az eszközön.

Fontos megjegyezni, hogy az előző folyamatában (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárol egy hitelesítési jogkivonatot helyi tároló, a felhasználó bejelentkezése után. Ez biztosítja, hogy teljesen zökkenőmentes élményt, mivel az eszköz le az értesítési biztonságos tartalom a tokent. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ezeket a jogkivonatokat is lejárt, az eszköz alkalmazást, és az értesítés fogadásakor megjelenjen-e a felhasználó megkérdezése általános értesítési indíthatja el az alkalmazást. Az alkalmazás majd hitelesíti a felhasználót, és az értesítési tartalom jeleníti meg.

Biztonságos leküldéses az oktatóanyag bemutatja, hogyan biztonságosan leküldéses értesítés küldéséhez. Az oktatóanyag épít, a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyag, ezért el kell végeznie a lépéseket, hogy az oktatóanyagban először.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Az iOS-projektre módosítása
Most, hogy módosította, a app háttér küldése csak a *azonosító* egy értesítés, módosítania kell az iOS-alkalmazás az értesítés, és a háttér-letölteni a biztonságos üzenetet megjelenítendő visszahívási.

E cél eléréséhez, igazolnia kell a biztonságos tartalmat lekérjen a app háttér-logika írását.

1. A **AppDelegate.m**, győződjön meg arról, hogy az alkalmazás regisztrálása a beavatkozás nélküli értesítések, akkor feldolgozza a háttérrendszer által küldött értesítés-azonosítója. Adja hozzá a **UIRemoteNotificationTypeNewsstandContentAvailability** didFinishLaunchingWithOptions beállítást:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. Az a **AppDelegate.m** adja hozzá az implementációs szakaszban a következő nyilatkozattal felső:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Majd adja hozzá a megvalósítási szakaszban a következő kódra, és a helyőrző `{back-end endpoint}` a végponthoz, a háttér-korábban beszerzett esetében:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Most van a bejövő értesítést, és a fenti metódus használatával a tartalom megjelenítése. Először azt kell ahhoz, hogy az iOS-alkalmazás fut a háttérben leküldéses értesítés fogadásakor. A **XCode**, a app projektet, a bal oldali panelen, majd kattintson a fő cél a a **célok** szakasz a központi panelről.
2. Kattintson a **képességek** a központi ablaktábla tetején fülre, és ellenőrizze a **távoli értesítések** jelölőnégyzetet.
   
    ![][IOS1]
3. A **AppDelegate.m** adja hozzá leküldéses értesítések kezeléséhez a következő metódust:
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Ne feledje, hogy az esetek hiányzó hitelesítési fejléc tulajdonság vagy elutasítása kezelésére által a háttér-érdemes. Ezekben az esetekben adott kezelésének legtöbbször a cél felhasználói élményét függ. Egy lehetőség egy értesítés megjelenítése egy általános kérdéshez a felhasználó hitelesítésére a tényleges értesítési beolvasása.

## <a name="run-the-application"></a>Futtassa az alkalmazást
Futtassa az alkalmazást, tegye a következőket:

1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem fog működni a szimulátor).
2. Az iOS-alkalmazás felhasználói felületén adja meg egy felhasználónevet és jelszót. Bármilyen karakterlánc is lehetnek, de ugyanarra az értékre kell lenniük.
3. Kattintson az iOS-alkalmazás felhasználói felületén, **jelentkezzen be**. Kattintson a **leküldéses küldése**. A következő nem jelenik meg az értesítési központ a biztonságos értesítésnek kell megjelennie.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
