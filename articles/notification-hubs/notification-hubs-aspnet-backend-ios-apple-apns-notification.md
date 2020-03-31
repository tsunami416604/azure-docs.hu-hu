---
title: Leküldéses értesítések küldése adott felhasználóknak az Azure Értesítési központok használatával | Microsoft dokumentumok
description: Ebből az anyagból megtudhatja, hogyan küldhet leküldéses értesítéseket meghatározott felhasználóknak az Azure Notification Hubs használatával.
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 48135ea614bbab4ca6649a83895ae5f632918c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387478"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése adott felhasználóknak az Azure Értesítési központok használatával

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Az oktatóanyag bemutatja, hogy hogyan küldhetők leküldéses értesítések adott alkalmazásfelhasználónak, adott eszközre az Azure Notification Hubs használatával. A WebAPI-háttérrendszer ASP.NET az ügyfelek hitelesítésére és az értesítések létrehozására szolgál, ahogy az az [alkalmazás háttérrendszeréről történő regisztráció](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)című útmutatóban látható.

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

Ez az oktatóanyag feltételezi, hogy az értesítési központot az [IOS – Első lépések az értesítési központtal című](notification-hubs-ios-apple-push-notification-apns-get-started.md)részben leírtak szerint hozta létre és konfigurálta. Ez az oktatóanyag a [Biztonságos leküldéses (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) oktatóanyag előfeltétele is.
Ha háttérszolgáltatásként a Mobilalkalmazásokat szeretné használni, olvassa el a [Mobilalkalmazások első lépéseit a leküldéses szolgáltatással című témakörben.](../app-service-mobile/app-service-mobile-ios-get-started-push.md)

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Az iOS-alkalmazás módosítása

1. Nyissa meg az [IOS első lépések az értesítési központokkal](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyagban létrehozott egyoldalas nézetalkalmazást.

   > [!NOTE]
   > Ez a szakasz feltételezi, hogy a projekt üres szervezeti névvel van konfigurálva. Ha nem, akkor minden osztálynévre elő kell készítenie a szervezet nevét.

2. A `Main.storyboard` fájlban adja hozzá a képernyőképen látható összetevőket az objektumtárból.

    ![Történet szerkesztése az Xcode illesztőben][1]

   * **Felhasználónév:** Helyőrző szöveggel rendelkező UITextField , írja be a *felhasználónevet,* közvetlenül a küldési eredménycímke alatt, és a bal és a jobb margóra, valamint a küldési eredmény címkéje alá korlátozva.
   * **Jelszó**: Helyőrző szöveggel rendelkező UITextField, *közvetlenül*a felhasználónév szövegmezője alatt, a bal és a jobb margóra korlátozva, a felhasználónév szövegmezője alatt. Jelölje be a **Biztonságos szövegbevitel** jelölőnégyzetet az Attribútumfelügyelő billentyű *csoportban.*
   * **Bejelentkezés**: Közvetlenül a jelszó szövegmezője alatt címkézett UIButton, és törölje a jelet az **Attribútumfelügyelő Engedélyezve** beállításának jelölőnégyzetből a *Control-Content* csoportban.
   * **WNS**: Címke és váltás az értesítési Windows értesítési szolgáltatás elküldésének engedélyezéséhez, ha az a hubon van beállítva. Tekintse meg a [Windows első lépéseit](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) bemutató.
   * **GCM**: Címke és váltás, hogy az értesítést el küldje a Google Cloud Messaging nek, ha az a hubon van beállítva. Lásd: [Android – első lépések](notification-hubs-android-push-notification-google-gcm-get-started.md) – oktatóanyag.
   * **APNS**: Címke és váltás az értesítés elküldésének engedélyezéséhez az Apple Platform értesítési szolgáltatásának.
   * **Címzett felhasználóneve:UITextField** helyőrző szöveggel, *Címzett felhasználónév címkéjével*, közvetlenül a GCM címke alatt, a bal és a jobb margóra korlátozva, valamint a GCM címke alatt.

     Egyes összetevők az Első lépések az [értesítési központokkal (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyagba kerültek.

3. **Ctrl** húzza a nézet összetevőiből az új aljzatokat, `ViewController.h` és adja hozzá ezeket az új kivezetéseket.

    ```objc
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

4. A `ViewController.h`alkalmazásban adja `#define` hozzá a következőket az importálási utasítások után. Cserélje `<Enter Your Backend Endpoint>` ki a helyőrzőt az alkalmazás háttérszámítógépének az előző szakaszban történő üzembe helyezéséhez használt cél URL-címmel. Például: `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. A projektben hozzon létre egy `RegisterClient` új Cocoa Touch osztályt, amely a létrehozott ASP.NET háttér-háttér-kiszolgálóhoz kapcsolódik. Hozza létre az `NSObject`osztályból öröklődő osztályt. Ezután adja hozzá `RegisterClient.h`a következő kódot a.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. A `RegisterClient.m`, frissítse `@interface` a szakasz:

    ```objc
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

7. Cserélje `@implementation` le a RegisterClient.m szakaszát a következő kódra:

    ```objc
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

    Ez a kód megvalósítja a logikát az [útmutató cikkben az alkalmazás ból történő regisztráció](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) nsurlsession segítségével REST-hívások végrehajtása az alkalmazás háttérrendszer, és NSUserDefaults helyileg tárolja a registrationId visszaadott az értesítési központ.

    Ez az osztály `authorizationHeader` megköveteli, hogy a tulajdonság a megfelelő működéshez legyen beállítva. Ezt a tulajdonságot `ViewController` az osztály állítja be a bejelentkezés után.

8. A `ViewController.h`alkalmazásban `#import` adjon `RegisterClient.h`hozzá egy utasítást a számára. Ezután adjon hozzá egy deklarációt az eszközjogkivonathoz, és hivatkozzon egy `RegisterClient` példányra a `@interface` szakaszban:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. A ViewController.m-ben adjon hozzá `@interface` egy privát metódusdeklarációt a következő szakaszban:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > A következő kódrészlet nem biztonságos hitelesítési séma, `createAndSetAuthenticationHeaderWithUsername:AndPassword:` akkor a megvalósítása az adott hitelesítési mechanizmus, amely létrehoz egy hitelesítési jogkivonatot kell használni a regiszter ügyfélosztály, például OAuth, Active Directory.

10. Ezután `@implementation` a `ViewController.m`szakaszban a, adja hozzá a következő kódot, amely hozzáadja a megvalósításaz eszköz token és a hitelesítési fejléc beállításához.

    ```objc
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

    Figyelje meg, hogy az eszköztoken beállítása hogyan engedélyezi a bejelentkezési gombot. Ennek az az oka, hogy a bejelentkezési művelet részeként a nézetvezérlő regisztrálja a leküldéses értesítéseket az alkalmazás háttérrendszerével. Ezért nem szeretné, hogy a Log In művelet elérhető legyen, amíg az eszköztoken megfelelően be van állítva. A bejelentkezést leválaszthatja a push regisztrációról, feltéve, hogy az előbbi az utóbbi előtt történik.

11. A ViewController.m fájlban az alábbi kódrészletekkel valósíthatja meg a **bejelentkezési** gomb műveletmódját, és egy módszert az értesítési üzenet elküldéséhez a ASP.NET háttérrendszer használatával.

    ```objc
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

12. Frissítse az **Értesítés küldése** gomb műveletét a ASP.NET háttérrendszer használatához, és küldje el a kapcsoló által engedélyezett PNS-nek.

    ```objc
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

13. A `ViewDidLoad` függvényben adja hozzá az alábbiakat `RegisterClient` a példány példányképének beállításához, és állítsa be a küldőmező delegáltját.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Most `AppDelegate.m`a , távolítsa el `application:didRegisterForPushNotificationWithDeviceToken:` az összes tartalmát a módszer, és cserélje ki a következő (győződjön meg arról, hogy a nézet vezérlő tartalmazza a legújabb eszköz token beolvasni APN):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Végül `AppDelegate.m`a alkalmazásban győződjön meg arról, hogy a következő módszerrel rendelkezik:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Az XCode-ban futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem működnek a szimulátorban).
2. Az iOS alkalmazás felhasználói felületén adja meg ugyanazt az értéket a felhasználónévhez és a jelszóhoz is. Ezután kattintson **a Bejelentkezés gombra.**

    ![iOS tesztalkalmazás][2]

3. A regisztrációs sikerességről tájékoztató előugró ablaknak kell látnia. Kattintson az **OK** gombra.

    ![megjelenik az iOS-tesztértesítés][3]

4. A **Címzett felhasználónév címke* szöveg mezőbe írja be a felhasználónév címkéjét, amelyet egy másik eszközről a regisztrációhoz használ.
5. Adjon meg egy értesítési üzenetet, és kattintson **az Értesítés küldése gombra.** Csak azok az eszközök kapják meg az értesítési üzenetet, amelyek regisztrálva vannak a címzett felhasználónévcímkéjével. Csak azoknak a felhasználóknak küldik el.

    ![iOS-tesztcímkével ellátott értesítés][4]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
