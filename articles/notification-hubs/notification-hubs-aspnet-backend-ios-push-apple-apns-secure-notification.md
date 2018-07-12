---
title: Az Azure Notification hubs szolgáltatás biztonságos
description: Ismerje meg, hogyan biztonságos leküldéses értesítések küldése iOS-alkalmazásokba az Azure-ból. Objective-C és C# nyelven írt kódmintákat.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: d3ba967a164a35af5bf66f7e74d5f95b5dc2a37f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308571"
---
# <a name="azure-notification-hubs-secure-push"></a>Az Azure Notification hubs szolgáltatás biztonságos
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Leküldéses értesítés támogatása a Microsoft Azure segítségével elérheti egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra, amely jelentősen egyszerűsíti a leküldéses értesítések a mobile fogyasztói és a vállalati alkalmazások számára Platform.

Szabályozási miatt, vagy biztonsági okokból, néha egy alkalmazás előfordulhat, hogy szeretne foglalni valamit a értesítést, amelyet nem lehet megadni a standard szintű leküldéses értesítési infrastruktúrát keresztül. Ebben az oktatóanyagban értékre a bizalmas adatokat az ügyféleszköz- és az alkalmazási háttérrendszer között biztonságos, hitelesített kapcsolaton keresztül küldésével ugyanazt a felhasználói élményt ismerteti.

Magas szinten a folyamat a következőképpen történik:

1. A háttér:
   * Háttér-adatbázisban tárolja biztonságos adattartalma.
   * Ez az értesítés azonosítója elküldi az eszköznek (nem biztonságos küld adatokat).
2. Az alkalmazás az eszközön, az értesítés fogadásakor:
   * Az eszköz kapcsolatba lép a háttéralkalmazás a biztonságos hasznos adatokat kér.
   * Az alkalmazás az eszközön értesítést, a hasznos adatokat jeleníti meg.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban) feltételezzük, hogy az eszköz tárolja a egy hitelesítési tokent a helyi tárban, miután a felhasználó bejelentkezik. Ez garantálja a zökkenőmentes élményt,, az eszköz kérheti le az értesítési e-jogkivonat használatával biztonságos hasznos. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ha ezek a jogkivonatok is járhatott, az eszköz alkalmazást, az értesítés fogadásakor indítsa el az alkalmazást a felhasználó megkérdezése általános értesítést kell megjelenítenie. Az alkalmazás ezután hitelesíti a felhasználót, és az értesítési tartalom látható.

Biztonságos leküldéses oktatóanyag bemutatja, hogyan biztonságosan küldhet leküldéses értesítéseket. Az oktatóanyag épül, amely a [– felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) így, hajtsa végre a lépéseket, hogy az oktatóanyagban először az oktatóanyagban.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása
Most, hogy módosította, a háttér küldése csak a *azonosító* egy értesítés, módosítania kell az iOS-alkalmazás az értesítések kezelésére, és a háttérbeli letölteni a megjeleníteni kívánt biztonságos üzenetet a visszahívási.

E cél eléréséhez rendelkezünk a tartalmak biztonságos lekérése a háttér logikai írni.

1. A **AppDelegate.m**, ellenőrizze, hogy a háttérrendszer által küldött az alkalmazás regisztrál a beavatkozás nélküli értesítésekhez úgy feldolgozza az értesítés azonosítója. Adja hozzá a **UIRemoteNotificationTypeNewsstandContentAvailability** didFinishLaunchingWithOptions lehetőség:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. Az a **AppDelegate.m** adjon hozzá egy megvalósítási szakaszban elejéhez, az alábbi nyilatkozatot:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Majd adja hozzá a megvalósítási szakaszban a következő kódra, és cserélje le a helyőrző `{back-end endpoint}` a végponthoz tartozó a háttérszolgáltatáshoz korábban beszerzett:

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

1. Most már van a bejövő értesítések kezelésére, és a fenti metódus lekérni a tartalmat megjelenítéséhez. Először is van az iOS-alkalmazás futtatását a háttérben, amikor a leküldéses értesítések engedélyezéséhez. A **XCode**, válassza a bal oldali panelen projektre, majd kattintson az alkalmazás fő cél az az **célok** szakaszban a középső ablaktáblán.
2. Kattintson a **képességek** a középső ablaktábla felső részén látható, és ellenőrizze a **távoli értesítések** jelölőnégyzetet.
   
    ![][IOS1]
3. A **AppDelegate.m** adja hozzá a következő metódust a leküldéses értesítések kezeléséhez:
   
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
   
    Fontos megjegyezni, hogy az esetek hiányzó hitelesítési fejléc tulajdonság vagy elutasítási kezeléséhez által a háttér-előnyösebb. Ezekben az esetekben az adott kezelését leginkább a célként megadott felhasználói élményét függ. Az egyik lehetőség, hogy értesítést kérek, és általános kéri a felhasználó hitelesítésére lekérni a tényleges értesítést.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Az alkalmazás futtatásához tegye a következőket:

1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem fog működni a szimulátor).
2. Az iOS-alkalmazás felhasználói felületén adja meg egy felhasználónevet és jelszót. Ezek bármilyen karakterlánc lehet, de ugyanazt az értéket kell lenniük.
3. Az IOS-es alkalmazás felhasználói felületén kattintson **bejelentkezés**. Kattintson a **küldjön leküldéses**. A biztonságos értesítést az értesítési központ által megjelenített kell megjelennie.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
