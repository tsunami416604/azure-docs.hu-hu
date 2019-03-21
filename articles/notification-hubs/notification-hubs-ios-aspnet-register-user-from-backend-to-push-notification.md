---
title: Az aktuális felhasználó a leküldéses értesítések regisztrálása a webes API-val |} A Microsoft Docs
description: Ismerje meg, hogyan kérjen az iOS-alkalmazások az Azure Notification hubs használatával leküldéses értesítési regisztrációban, ha a regisztrációs végzi, az ASP.NET webes API-t.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101975"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>A leküldéses értesítések az aktuális felhasználó regisztrálása az ASP.NET használatával

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan kérhet az Azure Notification Hubs leküldéses értesítések regisztrálása regisztrációs ASP.NET webes API által végrehajtott. Ez a témakör az oktatóanyag kibővíti [értesítse a felhasználókat a Notification hubs használatával]. Kell már végrehajtotta a szükséges lépéseket, hogy az oktatóanyag a hitelesített mobilszolgáltatás létrehozásához. Az értesítendő felhasználók forgatókönyv további információkért lásd: [értesítse a felhasználókat a Notification hubs használatával].

## <a name="update-your-app"></a>Az alkalmazás frissítése

1. A MainStoryboard_iPhone.storyboard adja hozzá a következő összetevőket az objektumtárból:

   * **Címke**: "A felhasználó a Notification hubs használatával leküldéses"
   * **Címke**: "InstallationId"
   * **Címke**: "User"
   * **Szövegmező**: "User"
   * **Címke**: "Password"
   * **Szövegmező**: "Password"
   * **Gomb**: "Bejelentkezés"

     Ezen a ponton a storyboard fájlt definiál a következőhöz hasonlóan néz ki:

     ![][0]

2. A Segéd-szerkesztőben a kapcsolt vezérlők kimeneteket hozzon létre és hívhatják meg őket, a szöveges mezők összekapcsolása a View-Controller (delegált) és hozzon létre egy **művelet** számára a **bejelentkezési** gombra.

    ![][1]

    A BreakingNewsViewController.h fájl most tartalmaznia kell a következő kódot:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Hozzon létre egy osztályt `DeviceInfo`, és másolja az alábbi kódot a fájl DeviceInfo.h felület szakaszába:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Másolja az alábbi kódot a megvalósítási szakaszban DeviceInfo.m fájl:

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

5. PushToUserAppDelegate.h adja hozzá a következő tulajdonság egyszeres:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. Az a `didFinishLaunchingWithOptions` metódus az PushToUserAppDelegate.m, adja hozzá a következő kódot:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Az első sor inicializálja a `DeviceInfo` egypéldányos. A második sor elindítja a regisztráció leküldéses értesítésekhez, amely már van jelen, ha már végrehajtotta a [Notification Hubs használatának első lépései] oktatóanyag.
7. A metódus megvalósításához PushToUserAppDelegate.m, `didRegisterForRemoteNotificationsWithDeviceToken` az AppDelegate-ben, és adja hozzá a következő kódot:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Ez a beállítás az eszköz jogkivonatát a kéréshez.

   > [!NOTE]
   > Ezen a ponton nem lehetnek bármilyen más kód ezzel a módszerrel. Ha már rendelkezik egy hívás a `registerNativeWithDeviceToken` metódus befejezésekor jelent meg a [Ismerkedés a Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag, ha megjegyzésbe, vagy távolítsa el a hívást.

8. Az a `PushToUserAppDelegate.m` fájlt, adja hozzá a következő kezelő metódust:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ez a módszer a felhasználói felületen megjelenít egy riasztást, ha az alkalmazás értesítéseket fogad a futás közben.

9. Nyissa meg a `PushToUserViewController.m` fájlt, és a következő implementációban a billentyűzet vissza:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. Az a `viewDidLoad` metódus az a `PushToUserViewController.m` fájlt, inicializálja a `installationId` címkét az alábbiak szerint:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adja hozzá a következő tulajdonságok felületén az `PushToUserViewController.m`:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Adja hozzá az alábbi megvalósításra:

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

13. Másolja az alábbi kódot a `login` kezelő metódus xcode-ban hozott létre:

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

    Ez a módszer a leküldéses értesítésekhez egy telepítési Azonosítót és a csatorna beolvasása, és a hitelesített webes API-metódus, amely létrehoz egy regisztrációt a Notification Hubs küldi az eszköz típusától, valamint azt. A webes API lett definiálva [értesítse a felhasználókat a Notification hubs használatával].

Most, hogy az ügyfélalkalmazás frissítve lett, térjen vissza a [értesítse a felhasználókat a Notification hubs használatával] és frissítheti a mobilszolgáltatást küldhetők értesítések a Notification Hubs használatával.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Értesítse a felhasználókat a Notification hubs használatával]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notification Hubs használatának első lépései]: notification-hubs-ios-apple-push-notification-apns-get-started.md
