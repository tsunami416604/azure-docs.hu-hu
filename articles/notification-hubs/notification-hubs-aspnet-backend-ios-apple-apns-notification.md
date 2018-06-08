---
title: Az egyes felhasználók Azure Notification Hubs használata leküldéses értesítések |} Microsoft Docs
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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "33777535"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Oktatóanyag: Az egyes felhasználók Azure Notification Hubs használata leküldéses értesítések
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Az oktatóanyag bemutatja, hogy hogyan küldhetők leküldéses értesítések adott alkalmazásfelhasználónak, adott eszközre az Azure Notification Hubs használatával. ASP.NET WebAPI háttérrendszerből használt hitelesíti az ügyfeleket, és értesítéseket, ahogy az az útmutató témakör [az alkalmazás háttérrendszeréből regisztrálása](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

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
Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Ez az oktatóanyag egyben a előfeltételeit a [(iOS) biztonságos leküldéses](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) oktatóanyag.
Ha szeretné használni a háttérszolgáltatás Mobile Apps, tekintse meg a [Mobile Apps első lépések leküldéses](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Az iOS-alkalmazás módosítása
1. Nyissa meg a létrehozott alkalmazás egylapos megtekintése a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.
   
   > [!NOTE]
   > Jelen szakaszban feltételezzük, hogy a projekt egy üres szervezetnév van konfigurálva. Ha nem, akkor kell illesztenie a szervezet neve, az összes osztály nevét.
   > 
   > 
2. Az a `Main.storyboard`, adja hozzá a összetevőket az objektumtárból képernyőfelvételen látható módon.
   
    ![][1]
   
   * **Felhasználónév**: a helyőrző szöveg A UITextField *adjon meg felhasználónevet*, azonnal eredmények címkével, és a bal és jobb margó korlátozott a küldés alatt, és a küldési eredmények címke alatt.
   * **Jelszó**: a helyőrző szöveg A UITextField *jelszó megadása*, azonnal a felhasználónév alatt szöveges mezőt, és korlátozott, a bal és jobb margó és a felhasználónév szövegmezőt alatt. Ellenőrizze a **szövegbeviteli biztonságos** attribútum megtekintő lehetőséget a *vissza kulcs*.
   * **Jelentkezzen be**: A UIButton feliratú azonnal a jelszó szövegmező alatt, és törölje a jelet a **engedélyezve** attribútumok megtekintő lehetőséget a *vezérlő – tartalom*
   * **WNS**: a Windows értesítési szolgáltatásával értesítés küldését, ha ez be van állítva a hub címke és kapcsoló. Tekintse meg a [Windows bevezetés](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag.
   * **GCM**: az értesítés küldését a Google Cloud Messaging, ha ez be van állítva a hub címke és kapcsoló. Lásd: [Android bevezetés](notification-hubs-android-push-notification-google-gcm-get-started.md) oktatóanyag.
   * **APNS**: címke és kapcsoló engedélyezése az Apple Platform Notification szolgáltatással küldött.
   * **Címzett Username:A** helyőrző szöveg UITextField *címzett felhasználónév címke*, azonnal a GCM alatt címkével, és a bal és jobb margó és a GCM-címke alatt korlátozott.

    Néhány összetevőt hozzáadott a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.

1. **CTRL** ViewController.h húzza a összetevői a nézetben, és adja hozzá ezek új értékesítési lehetőségek.
   
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
2. A ViewController.h, adja hozzá a következő `#define` után az importálási utasítást. Helyettesítő a *< írja be a háttérkiszolgáló végpont\>*  helyőrző a célként megadott URL-címet, a háttéralkalmazás az előző szakaszban központi telepítéséhez használt. Például: *http://you_backend.azurewebsites.net*.
   
    ```obj-c
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```
3. A projekt, hozzon létre egy új **Cocoa Touch osztály** nevű **RegisterClient** való az ASP.NET-háttér létrehozott. Hozzon létre a osztályt való öröklődés `NSObject`. Majd adja hozzá a következő kódot a RegisterClient.h a.

    ```obj-c   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
    ```
4. Az a `RegisterClient.m`, frissítse a `@interface` szakasz:

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
5. Cserélje le a `@implementation` című szakasza a következő kóddal RegisterClient.m:

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

    Ez a kód valósítja meg a programot, tekintse meg a cikk útmutatást [az alkalmazás háttérrendszeréből regisztrálása](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) a háttéralkalmazás NSURLSession REST elvégzéséhez használt hívások, és helyileg tárolja a registrationId NSUserDefaults által visszaadott a értesítési központ.

    Ez az osztály van szükség a tulajdonsága **authorizationHeader** kell beállítani a megfelelő működéshez. A tulajdonság értéke a **ViewController** osztály a bejelentkezés után.

1. A ViewController.h, adjon hozzá egy `#import` RegisterClient.h utasítást. Ezután adjon hozzá egy; az az eszköz jogkivonatát, és hivatkozás egy `RegisterClient` a példányt a `@interface` szakasz:

    ```obj-c   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
    ```
2. ViewController.m, adja hozzá a belső metódust nyilatkozatot a `@interface` szakasz:
   
    ```obj-c
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end
    ```

    > [!NOTE]
    > Az alábbi kódrészletben nincs a biztonságos hitelesítési séma, helyettesítse be a végrehajtása a **createAndSetAuthenticationHeaderWithUsername:AndPassword:** , az adott hitelesítési mechanizmus, amely létrehoz egy hitelesítési jogkivonatot a register ügyfél osztály, például az OAuth, az Active Directory által.
1. Ezt a a `@implementation` szakasza `ViewController.m`, adja hozzá a következő kódra, amely hozzáadja a beállítás a eszköz token és hitelesítési fejléc végrehajtására.
   
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
   
    Figyelje meg, hogyan az eszköz jogkivonatát beállítás lehetővé teszi, hogy a Bejelentkezés gombra. Mivel a bejelentkezési művelet részeként a view vezérlő regisztrálja, a leküldéses értesítések a háttéralkalmazás az van. Emiatt nem szeretné, hogy bejelentkezési művelet elérhetők lesznek, amíg az eszköz jogkivonatát megfelelően be van állítva. A bejelentkezés a leküldéses regisztrációt is absztrakcióhoz, mindaddig, amíg a korábbi történik, az utóbbi előtt.
2. A ViewController.m, az alábbi kódtöredékek segítségével valósítja meg a műveletet a a **bejelentkezés** gombra és a értesítést küldeni az ASP.NET-háttérrendszerből használatával metódust.
   
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

1. A művelet frissíti a **értesítés küldése** gombot használja az ASP.NET-háttérrendszerből és bármely, a kapcsoló által engedélyezett PNS küldeni.

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

1. A függvény **ViewDidLoad**, adja hozzá a következő példányt létrehozni a RegisterClient példány és állítsa be a szövegmezők delegáltja.
   
    ```obj-c
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```
2. Most már a **AppDelegate.m**, távolítsa el a módszer a tartalom `application:didRegisterForPushNotificationWithDeviceToken:` , és cserélje le a következő győződjön meg arról, hogy a view vezérlő tartalmaz-e a legújabb eszköz jogkivonatát APNs lekért:
   
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
3. Végül a **AppDelegate.m**, győződjön meg arról, hogy a következő metódust:

    ```obj-c   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése
1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem működnek a szimulátor).
2. Az iOS-alkalmazás felhasználói felületén, a adja meg ugyanazt az értéket a felhasználónevét és jelszavát. Kattintson a **bejelentkezés**.
   
    ![][2]
3. Meg kell jelennie egy előugró ablak regisztráció sikeres ad tájékoztatást. Kattintson az **OK** gombra.
   
    ![][3]
4. Az a **címzett felhasználónév címke* szöveg mezőbe írja be a felhasználó nevét címke használt egy másik eszköz regisztrációját.
5. Adjon meg egy értesítési üzenetet, és kattintson a **értesítés küldése**. Csak azokat az eszközöket, amelyek a címzett felhasználói név címkével regisztráció a értesítési üzenet. Csak azoknak a felhasználóknak továbbítja.
   
    ![][4]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spartial-data-geofencing-notification.md)


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
