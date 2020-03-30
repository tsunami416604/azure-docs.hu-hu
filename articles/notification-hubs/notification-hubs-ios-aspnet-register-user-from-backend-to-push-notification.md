---
title: Az aktuális felhasználó regisztrálása leküldéses értesítésekhez a Webes API használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan kérhet leküldéses értesítési regisztrációt egy iOS-alkalmazásban az Azure Notification Hubs segítségével, ha ASP.NET webes API-val történik a regisztráció.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71211869"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Az aktuális felhasználó regisztrálása leküldéses értesítésekhez ASP.NET használatával

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan kérhet leküldéses értesítési regisztrációt az Azure Notification Hubs használatával, ha ASP.NET webes API-ASP.NET végzi a regisztrációt. Ez a témakör kiterjeszti az oktatóanyag [Értesítés i felhasználók értesítési központok]. A hitelesített mobilszolgáltatás létrehozásához már el kell végeznie az oktatóanyag szükséges lépéseit. A felhasználók értesítése esetén további információt a [Felhasználók értesítése az értesítési központtal]című témakörben talál.

## <a name="update-your-app"></a>Alkalmazás frissítése

1. A MainStoryboard_iPhone.storyboardban adja hozzá a következő összetevőket az objektumtárból:

   * **Címke**: "Leküldése a felhasználóhoz értesítési központokkal"
   * **Címke**: "InstallationId"
   * **Címke**: "Felhasználó"
   * **Szövegmező**: "Felhasználó"
   * **Címke**: "Jelszó"
   * **Szövegmező**: "Jelszó"
   * **Gomb**: "Bejelentkezés"

     Ezen a ponton a történet a következőképpen néz ki:

     ![][0]

2. A segédszerkesztőben hozzon létre felvevőpiacokat az összes átváltott vezérlőhöz, és hívja meg őket, csatlakoztassa a szövegmezőket a Nézetvezérlővel (meghatalmazott), és hozzon létre egy **műveletet** a **bejelentkezési** gombhoz.

    ![][1]

    A BreakingNewsViewController.h fájlnak most antól a következő kódot kell tartalmaznia:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Hozzon létre `DeviceInfo`egy osztálynevű titulust, és másolja a következő kódot a DeviceInfo.h fájl felületszakaszába:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Másolja a következő kódot a DeviceInfo.m fájl implementációs szakaszában:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. A PushToUserAppDelegate.h-ban adja hozzá a következő tulajdonságot:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. A `didFinishLaunchingWithOptions` PushToUserAppDelegate.m metódusban adja hozzá a következő kódot:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Az első sor inicializálja a `DeviceInfo` singleton. A második sor elindítja a leküldéses értesítések regisztrációját, amely már jelen van, ha már elvégezte az Első lépések az [értesítési központokkal oktatóanyagot.]
7. A PushToUserAppDelegate.m, valósítsa meg a metódust `didRegisterForRemoteNotificationsWithDeviceToken` az AppDelegate és adja hozzá a következő kódot:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Ez beállítja a kérelem eszközjogkivonatát.

   > [!NOTE]
   > Ezen a ponton nem lehet más kód ebben a metódusban. Ha már van egy `registerNativeWithDeviceToken` hívása a metódus, amely a dedikált az Első lépések az [értesítési központok](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag, meg kell megjegyzést, vagy távolítsa el a hívást.

8. A `PushToUserAppDelegate.m` fájlban adja hozzá a következő kezelőmódszert:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ez a módszer riasztást jelenít meg a felhasználói felületen, amikor az alkalmazás értesítést kap futás közben.

9. Nyissa `PushToUserViewController.m` meg a fájlt, és adja vissza a billentyűzetet a következő implementációban:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. A `viewDidLoad` fájlban `PushToUserViewController.m` lévő módszerben `installationId` inicializálja a címkét az alábbiak szerint:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adja hozzá a következő `PushToUserViewController.m`tulajdonságokat az összeköttetésben:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Ezután adja hozzá a következő implementációt:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Másolja a következő `login` kódot az XCode által létrehozott kezelőmetódusba:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Ez a módszer lekéri a leküldéses értesítések telepítési azonosítóját és csatornáját, és az eszköztípussal együtt elküldi azt a hitelesített webes API-metódusnak, amely regisztrációt hoz létre az értesítési központokban. Ezt a webes API-t az [Értesítési központoksegítségével a Felhasználók értesítése]határozza meg.

Most, hogy az ügyfélalkalmazás frissült, térjen vissza a [Felhasználók értesítése központtal,] és frissítse a mobilszolgáltatás értesítési központok használatával értesítéseket küldeni.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Felhasználók értesítése az értesítési központokkal]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Az értesítési központok – első lépések]: notification-hubs-ios-apple-push-notification-apns-get-started.md
