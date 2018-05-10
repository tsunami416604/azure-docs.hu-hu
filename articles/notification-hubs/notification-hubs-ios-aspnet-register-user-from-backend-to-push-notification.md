---
title: Az aktuális felhasználó a leküldéses értesítések regisztrálása webes API használatával |} Microsoft Docs
description: Útmutató az iOS-alkalmazásoknak az Azure Notification Hubs a leküldéses értesítési regisztrációban kérése regisztrálás ASP.NET Web API hajtja végre.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c43c15131afb5fbf346b0137dac566f5331c65a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Az aktuális felhasználó a leküldéses értesítések regisztrálása ASP.NET használatával
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan kérjen az Azure Notification Hubs leküldéses értesítési regisztrációban, ha regisztrációs ASP.NET Web API hajtja végre. Ez a témakör kibővíti az oktatóanyag [értesítse a felhasználókat a Notification hubs használatával]. Már végrehajtotta a szükséges lépéseket, hogy az oktatóanyagban a hitelesített mobilszolgáltatás létrehozásához. Az értesítendő felhasználók forgatókönyvön további információkért lásd: [értesítse a felhasználókat a Notification hubs használatával].

## <a name="update-your-app"></a>Az alkalmazás frissítése
1. A MainStoryboard_iPhone.storyboard adja hozzá a következő összetevőket az objektumtárból:
   
   * **Címke**: "A Notification Hubs felhasználói leküldése"
   * **Címke**: "Végrehajtott"
   * **Címke**: "User"
   * **Szövegmező**: "User"
   * **Címke**: "Password"
   * **Szövegmező**: "Password"
   * **Gomb**: "Bejelentkezés"
     
     Ezen a ponton a storyboard néz ki a következő:
     
      ![][0]
2. Segéd-szerkesztőben kimeneteket a kapcsolt vezérlők létrehozása, keresheti őket, az mezők csatlakozzon a nézetvezérlő (delegált), és hozzon létre egy **művelet** a a **bejelentkezési** gombra.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Hozzon létre egy osztályt **deviceinfo információja**, és másolja az alábbi kódot a fájl DeviceInfo.h felület szakaszába:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Az implementációs szakaszban DeviceInfo.m fájl másolja az alábbi kódot:
   
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
5. PushToUserAppDelegate.h adja hozzá a következő tulajdonság egypéldányos:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. Az a **didFinishLaunchingWithOptions** PushToUserAppDelegate.m, metódusban adja hozzá a következő kódot:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    Az első sor inicializálja a **deviceinfo információja** egypéldányos. A második sor elindul a regisztráció a leküldéses értesítések, amelyek már létezik, ha már végrehajtotta a [Ismerkedés a Notification Hubs] oktatóanyag.
7. A PushToUserAppDelegate.m, valósítja meg a **didRegisterForRemoteNotificationsWithDeviceToken** a AppDelegate a, és adja hozzá a következő kódot:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    Ez a beállítás az eszköz jogkivonatát a kérelemhez.
   
   > [!NOTE]
   > Ezen a ponton nem lehetnek többi kód az ezzel a módszerrel. Ha már rendelkezik egy hívás a **registerNativeWithDeviceToken** metódus befejezése felvett a [Ismerkedés a Notification Hubs](/manage/services/notification-hubs/get-started-notification-hubs-ios/) oktatóanyag, kell Megjegyzés kimenő, vagy távolítsa el az adott hívás.
   > 
   > 
8. A PushToUserAppDelegate.m fájlban adja hozzá a következő kezelő metódust:
   
   * (a "void") alkalmazás:(UIApplication *) alkalmazás didReceiveRemoteNotification:(NSDictionary *) userInfo {NSLog (@"% @", userInfo);   UIAlertView * riasztás = [[UIAlertView foglalási] initWithTitle:@"Notification" üzenetet: [userInfo objectForKey:@"inAppMessage"] delegált: üres cancelButtonTitle: @"OK" otherButtonTitles:nil, üres];   [riasztás megjelenítése]; }
   
   Ez a módszer a felhasználói felületen megjelenít egy riasztást, amikor az alkalmazás futtatása értesítéseket fogad.
9. Nyissa meg a PushToUserViewController.m fájlt, és térjen vissza a billentyűzet a következő megvalósításában:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. Az a **viewDidLoad** PushToUserViewController.m fájlban metódus inicializálni a végrehajtott címke az alábbiak szerint:
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. Adja hozzá a következő tulajdonságok PushToUserViewController.m felülettel:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Adja hozzá a következő végrehajtására:
    
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
13. Másolja az alábbi kódot a **bejelentkezési** eseménykezelő metódus hozta létre xcode-ban:
    
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
    
    Ez a módszer egy telepítési Azonosítót és a csatorna lekérdezi a leküldéses értesítések, és elküldi, az eszköz típusa, valamint a hitelesített webes API-módszer, amely a Notification Hubs egy regisztrációs hoz. A Web API lett definiálva [értesítse a felhasználókat a Notification hubs használatával].

Most, hogy az ügyfélalkalmazás frissítették, térjen vissza a [értesítse a felhasználókat a Notification hubs használatával] , és frissítse a mobilszolgáltatáshoz az értesítések küldése a Notification Hubs használatával.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[értesítse a felhasználókat a Notification hubs használatával]: /manage/services/notification-hubs/notify-users-aspnet

[Ismerkedés a Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios
