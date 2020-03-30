---
title: Az Azure Notification Hubs biztonságos leküldéses az iOS-hez
description: Megtudhatja, hogy miként küldhet biztonságos leküldéses értesítéseket egy iOS-alkalmazásba az Azure-ból. A C és C# célkitűzéssel írt kódminták.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530748"
---
# <a name="azure-notification-hubs-secure-push"></a>Az Azure értesítési központok biztonságos leküldése

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

A Microsoft Azure leküldéses értesítési támogatása lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses infrastruktúra elérését, ami nagyban leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a nagyvállalati mobilalkalmazások számára Platformok.

Szabályozási vagy biztonsági korlátok miatt előfordulhat, hogy egy alkalmazás szeretne felvenni valamit az értesítésben, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag ismerteti, hogyan érheti el ugyanazt a felhasználói élményt azáltal, hogy bizalmas adatokat küld az ügyféleszköz és az alkalmazás háttérrendszere közötti biztonságos, hitelesített kapcsolaton keresztül.

Magas szinten az áramlás a következő:

1. Az alkalmazás háttér-vége:
   * Biztonságos tartalom tárolható a háttér-adatbázisban.
   * Elküldi az értesítés azonosítóját az eszköznek (nem küld biztonságos adatokat).
2. Az alkalmazás az eszközön, amikor megkapja az értesítést:
   * Az eszköz kapcsolatba lép a biztonsági adatért, a háttérrel.
   * Az alkalmazás értesítésként jelenítheti meg a hasznos adatot az eszközön.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárolja a hitelesítési jogkivonatot a helyi tárolóban, miután a felhasználó bejelentkezik. Ez garantálja a zökkenőmentes élményt, mivel az eszköz ezzel a jogkivonattal lekérheti az értesítés biztonságos hasznos adatait. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a tokenek lejárhatnak, az eszközalkalmazásnak az értesítés kézhezvételekor meg kell jelenítenie egy általános értesítést, amely felszólítja a felhasználót az alkalmazás elindítására. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési hasznos terhet.

Ez a Biztonságos leküldéses oktatóanyag bemutatja, hogyan küldhet leküldéses értesítést biztonságosan. Az oktatóanyag a [Felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagra épül, ezért először végre kell hajtania az oktatóanyag lépéseit.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy az értesítési központot az [IOS – Első lépések az értesítési központtal című](notification-hubs-ios-apple-push-notification-apns-get-started.md)részben leírtak szerint hozta létre és konfigurálta.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása

Most, hogy módosította az alkalmazás háttér-fiókját, hogy csak az értesítés *azonosítóját* küldje el, meg kell változtatnia az iOS-alkalmazást az értesítés kezeléséhez, és vissza kell hívnia a háttérrendszert a biztonságos üzenet megjelenítéséhez.

E cél elérése érdekében meg kell írni a logikát, hogy lekérje a biztonságos tartalmat az alkalmazás háttérrendszerből.

1. A `AppDelegate.m`alkalmazásban győződjön meg arról, hogy az alkalmazás regisztrálja a csendes értesítéseket, így feldolgozza a háttérrendszerből küldött értesítési azonosítót. Adja `UIRemoteNotificationTypeNewsstandContentAvailability` hozzá a lehetőséget a didFinishLaunchWithOptions eszközben:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. A `AppDelegate.m` végrehajtási szakasz hozzáadása a tetején a következő deklarációval:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Ezután adja hozzá a megvalósítási szakaszban a `{back-end endpoint}` következő kódot, helyettesítve a helyőrzőt a korábban kapott háttérrendszer végpontjával:

    ```objc
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

    Ez a módszer meghívja az alkalmazás háttérrendszerét az értesítési tartalom lekéréséhez a megosztott beállításokban tárolt hitelesítő adatok használatával.

4. Most kezelnünk kell a bejövő értesítést, és a fenti módszert kell használnunk a tartalom megjelenítéséhez. Először is engedélyeznünk kell, hogy az iOS-alkalmazás a háttérben fusson, amikor leküldéses értesítést kap. Az **XCode alkalmazásban**válassza ki az alkalmazásprojektet a bal oldali panelen, majd kattintson a fő alkalmazáscélzásra a központi ablaktábla **Célok** szakaszában.
5. Ezután kattintson a **Képességek** fülre a központi ablaktábla tetején, és jelölje be a **Távoli értesítések** jelölőnégyzetet.

    ![][IOS1]

6. A `AppDelegate.m` push értesítések kezeléséhez a következő módszert adja hozzá:

    ```objc
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
    ```

    Vegye figyelembe, hogy célszerű kezelni a hiányzó hitelesítési fejléc tulajdonság vagy elutasítás a háttér-rendszer. Ezeknek az eseteknek a konkrét kezelése leginkább a megcélzott felhasználói élménytől függ. Az egyik lehetőség az, hogy egy általános kéréssel ellátott értesítést jelenít meg, amelynek segítségével a felhasználó hitelesíti magát a tényleges értesítés lekéréséhez.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához tegye a következőket:

1. Az XCode-ban futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem fognak működni a szimulátorban).
2. Az iOS alkalmazás felhasználói felületén adjon meg egy felhasználónevet és egy jelszót. Ezek bármilyen karakterlánc lehetnek, de azonos értékűeknek kell lenniük.
3. Az iOS-alkalmazás felhasználói felületén kattintson a **Bejelentkezés**gombra. Ezután kattintson **a Leküldéses küldés**gombra. A biztonságos értesítésnek az értesítési központban kell megjelenhetnie.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
