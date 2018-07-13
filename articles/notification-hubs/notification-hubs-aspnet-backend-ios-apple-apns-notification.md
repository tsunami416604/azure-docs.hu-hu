---
title: Leküldéses értesítések küldése az Azure Notification Hubs használatával adott felhasználók |} A Microsoft Docs
description: Ebből az anyagból megtudhatja, hogyan küldhet leküldéses értesítéseket meghatározott felhasználóknak az Azure Notification Hubs használatával.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/13/2018
ms.author: dimazaid
ms.openlocfilehash: 36d70c40e3de7bd38cdfc566da37060cdcea9060
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681553"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése megadott felhasználóknak Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Az oktatóanyag bemutatja, hogy hogyan küldhetők leküldéses értesítések adott alkalmazásfelhasználónak, adott eszközre az Azure Notification Hubs használatával. Egy ASP.NET WebAPI háttérrendszert szolgál ügyfelek hitelesítéséhez, és az értesítéseket, ahogyan az az útmutató témakör [regisztrálja az alkalmazás háttérrendszeréből](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A WebAPI-projekt létrehozása
> * Ügyfelek hitelesítése a WebAPI háttérrendszeren
> * Regisztráció értesítésekre a WebAPI háttérrendszer használatával
> * Értesítések küldése a WebAPI háttérrendszerről
> * Az új WebAPI háttérrendszer közzététele
> * Az iOS-alkalmazás módosítása
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Ebben az oktatóanyagban egyben a előfeltétele a [(iOS) biztonságos leküldéses](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) oktatóanyag.
Ha szeretné a háttér-szolgáltatásként használhatja a Mobile Apps, tekintse meg a [Mobile Apps használatának első lépései leküldéses](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Az iOS-alkalmazás módosítása
1. Nyissa meg a létrehozott egyoldalas alkalmazás megtekintése a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.
   
   > [!NOTE]
   > Ez a szakasz azt feltételezi, hogy a projekt egy üres szervezetnév van konfigurálva. Ha nem, akkor kell illesztenie a szervezet neve, az összes osztály nevét.
   > 
   > 
2. Az a `Main.storyboard`, adja hozzá a összetevőket az objektumtárból a képernyőképen látható.
   
    ![][1]
   
   * **Felhasználónév**: helyőrző szöveget, és a egy UITextField *adja meg felhasználónevét*, azonnal eredmények címkét, és korlátozza a bal és jobb margók a küldés alatt, és a küldési eredmények felirat alatt.
   * **Jelszó**: helyőrző szöveget, és a egy UITextField *jelszó megadása*, azonnal a felhasználónév alatt szöveges mezőt, és korlátozott, a bal és jobb margók és a felhasználónév szövegmező alatt található. Ellenőrizze a **szöveg biztonságos** a beállítást a attribútum vizsgáló *kulcsát adja vissza*.
   * **Jelentkezzen be**: A UIButton feliratú azonnal a jelszó szövegmező alatt található, és törölje a jelet a **engedélyezve** a beállítást az attribútumok vizsgáló *vezérlő – tartalom*
   * **Wns –**: címke és a kapcsoló a Windows értesítési szolgáltatás értesítést küld, ha azt be lett állítva a hub engedélyezéséhez. Tekintse meg a [Windows bevezetés](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag.
   * **GCM**: címke és a kapcsoló a értesítést küld a Google Cloud Messaging, ha azt be lett állítva a hub engedélyezéséhez. Lásd: [Android bevezetés](notification-hubs-android-push-notification-google-gcm-get-started.md) oktatóanyag.
   * **APNS**: címke és a kapcsoló az értesítés küldése az Apple Platform Notification Service engedélyezéséhez.
   * **Címzett Username:A** helyőrző szöveg, UITextField *címzett felhasználónév-címkéjével*, azonnal a GCM alatt címkézését, és korlátozott, a bal és jobb margók és a GCM felirat alatt.

    Néhány összetevőt verzióban lettek hozzáadva a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.

1. **CTRL** ViewController.h húzza az a nézet az összetevőket, és adja hozzá ezek új értékesítési lehetőségek.
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
    ```
2. A ViewController.h, adja hozzá a következő `#define` után az importálási utasításokban. Helyettesítse be a *< adja meg a háttérbeli végpont\>*  helyőrzőt az előző szakaszban az alkalmazás háttérrendszere központi telepítéséhez használt cél URL-címe. Például: *http://you_backend.azurewebsites.net*.
   
    ```obj-c
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```
3. A projektben hozzon létre egy új **Cocoa Touch osztály** nevű **RegisterClient** , az ASP.NET háttér-létrehozott kapcsolaton. Hozzon létre az osztály örököl `NSObject`. Majd adja hozzá a következő kódot a RegisterClient.h a.

    ```obj-c   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
    ```
4. Az a `RegisterClient.m`, frissítse a `@interface` szakaszban:

    ```obj-c   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
    ```
5. Cserélje le a `@implementation` szakasz a RegisterClient.m a következő kóddal:

    ```obj-c
        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end
    ```

    Ez a kód implementálja az útmutató a cikkben ismertetett logikát [regisztrálja az alkalmazás háttérrendszeréből](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) NSURLSession-végrehajtására a REST-hívások a háttéralkalmazáshoz, és helyileg tárolja a registrationId NSUserDefaults által visszaadott a értesítési központ.

    Ez az osztály megköveteli a tulajdonsága **authorizationHeader** kell beállítani ahhoz, hogy megfelelően működjön. Ez a tulajdonság van beállítva a **ViewController** osztály a bejelentkezés után.

1. A ViewController.h, adjon hozzá egy `#import` RegisterClient.h utasítást. Ezután adjon hozzá egy nyilatkozatot, az eszköz jogkivonatát az és referenciáját egy `RegisterClient` példány a `@interface` szakaszban:

    ```obj-c   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
    ```
2. ViewController.m, adja hozzá a privát metódus határozza meg a `@interface` szakaszban:
   
    ```obj-c
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end
    ```

    > [!NOTE]
    > Az alábbi kódrészlet nem egy biztonságos hitelesítési séma, helyettesítse a megvalósítását az **createAndSetAuthenticationHeaderWithUsername:AndPassword:** az adott hitelesítési mechanizmus az, hogy létrehoz egy hitelesítési tokent, a regisztráció ügyfél osztályt, például az OAuth, az Active Directory által használt.
1. Ezt a a `@implementation` szakaszában `ViewController.m`, adja hozzá a következő kódra, amely hozzáadja a beállítás az eszköz token és a hitelesítési fejléc végrehajtására.
   
    ```obj-c
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
    ```
   
    Figyelje meg, hogyan beállítás az eszköz jogkivonatát lehetővé teszi, hogy a Bejelentkezés gombra. Ez azért, mert a bejelentkezési művelet részeként, a nézet-vezérlő regisztrálja a leküldéses értesítések a háttéralkalmazás az. Ezért nem szeretné Log művelet elérhetők lesznek, amíg az eszköz jogkivonatát megfelelően be lett állítva. A bejelentkezés, a leküldéses regisztráció mindaddig, amíg az előbbi történik, az utóbbi előtt is leválasztja.
2. ViewController.m, használja az alábbi kódrészletek az a műveleti metódus megvalósításához az **bejelentkezés** gombra, és a egy metódust az ASP.NET-háttérrendszer használatával értesítési üzenetet küldeni.
   
    ```obj-c
       - (IBAction)LogInAction:(id)sender {
           // create authentication header and set it in register client
           NSString* username = self.UsernameField.text;
           NSString* password = self.PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController* selfie = self;
           [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
               andCompletion:^(NSError* error) {
               if (!error) {
                   dispatch_async(dispatch_get_main_queue(),
                   ^{
                       selfie.SendNotificationButton.enabled = YES;
                       [self MessageBox:@"Success" message:@"Registered successfully!"];
                   });
               }
           }];
       }

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                    Message:(NSString*)message
        {
            NSURLSession* session = [NSURLSession
                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
                delegateQueue:nil];

            // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
            NSURL* requestURL = [NSURL URLWithString:[NSString
                stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
                usernameTag]];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];

            // Get the mock authenticationheader from the register client
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

            // Execute the send notification REST API on the ASP.NET Backend
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                        pns, httpResponse.statusCode, error];
                    dispatch_async(dispatch_get_main_queue(),
                    ^{
                        // Append text because all 3 PNS calls may also have information to view
                        [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                    });
                    NSLog(status);
                }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
    ```

1. A művelet frissítése a **értesítés küldése** az ASP.NET-háttéralkalmazás használatára, és a egy kapcsoló által engedélyezett bármely PNS küldése gombra.

    ```obj-c
        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }

        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }
    ```

1. A függvény **ViewDidLoad**, vegye fel a következő példányt létrehozni a RegisterClient példány és állítsa be a szöveges mezők delegát.
   
    ```obj-c
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```
2. Most már a **AppDelegate.m**, távolítsa el a metódus a tartalom `application:didRegisterForPushNotificationWithDeviceToken:` és cserélje le a következő, győződjön meg arról, hogy a nézet-vezérlő tartalmazza-e a legújabb eszköztoken APNs lekért:
   
    ```obj-c
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
    ```
3. Végül pedig a **AppDelegate.m**, ellenőrizze, hogy a következő metódust:

    ```obj-c   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése
1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem működnek a szimulátor).
2. Adja meg ugyanazt az értéket az iOS-alkalmazás felhasználói felületén, felhasználónév és jelszó. Kattintson a **bejelentkezés**.
   
    ![][2]
3. Megjelenik egy előugró ablak tájékoztatja Önt a regisztráció sikeres. Kattintson az **OK** gombra.
   
    ![][3]
4. Az a **címzett felhasználónév-címkéjével* szöveg mezőben adja meg a másik eszközről történő regisztráció használt felhasználói név címke.
5. Adjon meg egy értesítési üzenetet, és kattintson a **értesítés küldése**. Csak azokat az eszközöket, amelyek rendelkeznek a címzett felhasználói név címkével rendelkező a regisztrálását az értesítési üzenet. Ez csak a felhasználók küldeni.
   
    ![][4]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spartial-data-geofencing-notification.md)


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
