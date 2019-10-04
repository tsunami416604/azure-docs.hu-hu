---
title: Az aktuális felhasználó regisztrálása leküldéses értesítésekhez webes API használatával | Microsoft Docs
description: Ismerje meg, hogyan kérhet leküldéses értesítéseket egy iOS-alkalmazásban az Azure Notification Hubs, ha a ASP.NET web API végzi a regisztrációt.
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
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211869"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Az aktuális felhasználó regisztrálása leküldéses értesítésekhez a ASP.NET használatával

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Áttekintés

Ebből a témakörből megtudhatja, hogyan kérhet le leküldéses értesítések regisztrációját az Azure Notification Hubs, ha a ASP.NET web API végzi a regisztrációt. Ez a témakör kibővíti az oktatóanyagot [Felhasználók értesítése Notification Hubs]. A hitelesített Mobile szolgáltatás létrehozásához már el kell végeznie az oktatóanyagban szükséges lépéseket. A felhasználók értesítése forgatókönyvről további információt a [Felhasználók értesítése Notification Hubs]című témakörben talál.

## <a name="update-your-app"></a>Az alkalmazás frissítése

1. A MainStoryboard_iPhone. storyboardban adja hozzá a következő összetevőket az objektum könyvtárából:

   * **Címke**: "Leküldés a felhasználónak a Notification Hubs"
   * **Címke**: Telepítésazonosító
   * **Címke**: Felhasználói
   * **Szövegmező**: Felhasználói
   * **Címke**: Jelszó
   * **Szövegmező**: Jelszó
   * **Gomb**: Bejelentkezési

     Ezen a ponton a storyboard a következőhöz hasonlóan néz ki:

     ![][0]

2. A Segéd-szerkesztőben hozzon létre az összes kapcsolót az összes átváltott vezérlő számára, és hívja meg őket, kapcsolja össze a szövegmezőket a nézet vezérlővel (delegált), és hozzon létre egy **műveletet** a **Bejelentkezés** gombhoz.

    ![][1]

    A BreakingNewsViewController. h fájlnak most a következő kódot kell tartalmaznia:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Hozzon létre egy `DeviceInfo`nevű osztályt, és másolja a következő kódot a deviceinfo. h fájl Interface szakaszába:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Másolja a következő kódot a DeviceInfo. m fájl implementáció szakaszában:

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

5. A PushToUserAppDelegate. h-ben adja hozzá a következő tulajdonságot (egyszer):

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. A PushToUserAppDelegate. m metódusábanadjahozzáakövetkezőkódot:`didFinishLaunchingWithOptions`

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Az első sor inicializálja az `DeviceInfo` egypéldányos. A második sor elindítja a leküldéses értesítések regisztrációját, ami már jelen van, ha már elvégezte az első [Ismerkedés a Notification Hubs] oktatóanyagot.
7. A PushToUserAppDelegate. m-ben implementálja `didRegisterForRemoteNotificationsWithDeviceToken` a metódust a AppDelegate, és adja hozzá a következő kódot:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Ezzel beállítja a kérelemhez tartozó eszköz jogkivonatát.

   > [!NOTE]
   > Ezen a ponton nem lehet más kód ebben a metódusban. Ha már van olyan hívása a `registerNativeWithDeviceToken` metódushoz, amelyet a [Notification Hubs első lépéseinek](notification-hubs-ios-apple-push-notification-apns-get-started.md) elvégzése oktatóanyag során adott meg, akkor megjegyzést kell kijelölnie, vagy el kell távolítania a hívást.

8. `PushToUserAppDelegate.m` A fájlban adja hozzá a következő kezelői metódust:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ez a módszer riasztást jelenít meg a felhasználói felületen, ha az alkalmazás a futása közben értesítést kap.

9. Nyissa `PushToUserViewController.m` meg a fájlt, és a következő implementációban küldje vissza a billentyűzetet:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. A fájl metódusában a következő módon inicializálja a `installationId` címkét: `viewDidLoad` `PushToUserViewController.m`

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adja hozzá a következő tulajdonságokat a felületen `PushToUserViewController.m`a alkalmazásban:

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

13. Másolja a következő kódot a `login` Xcode által létrehozott kezelő metódusba:

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

    Ez a módszer a leküldéses értesítések telepítési AZONOSÍTÓját és csatornáját is lekéri, valamint az eszköz típusával együtt a hitelesített webes API-metódushoz, amely létrehoz egy regisztrációt Notification Hubs. Ezt a webes API-t a [Felhasználók értesítése Notification Hubs]című cikkben definiálták.

Most, hogy az ügyfélalkalmazás frissítve lett, térjen vissza a [Felhasználók értesítése Notification Hubs] és frissítse a Mobile Service-t, hogy a Notification Hubs használatával küldjön értesítéseket.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Felhasználók értesítése Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Ismerkedés a Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
