---
title: "Azure Notification Hubs – felhasználók értesítése iOS rendszerhez .NET-háttérrendszerrel"
description: "Megtudhatja, hogyan küldhetők leküldéses értesítések az Azure-ban a felhasználók számára. Kódminták Objective-C és a háttérkiszolgáló .NET API-t."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Azure Notification Hubs – felhasználók értesítése iOS rendszerhez .NET-háttérrendszerrel
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Áttekintés
Leküldéses értesítési támogatás az Azure-ban lehetővé teszi egy könnyen kezelhető, multiplatform és kibővített leküldéses infrastruktúrában, ami jelentősen egyszerűsíti a leküldéses értesítések mobil platformokhoz fogyasztói, valamint a vállalati alkalmazások eléréséhez. Az oktatóanyag bemutatja, hogyan küldhetők az Azure Notification Hubs használatával leküldéses értesítések adott alkalmazásfelhasználónak, meghatározott eszközre. ASP.NET WebAPI háttérrendszerből használt hitelesíti az ügyfeleket, és értesítéseket, ahogy az az útmutató témakör [az alkalmazás háttérrendszeréből regisztrálása](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Ez az oktatóanyag egyben a előfeltételeit a [(iOS) biztonságos leküldéses](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) oktatóanyag.
> Ha szeretné használni a háttérszolgáltatás Mobile Apps, tekintse meg a [Mobile Apps első lépések leküldéses](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Az iOS-alkalmazás módosítása
1. Nyissa meg a létrehozott alkalmazás egylapos megtekintése a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.
   
   > [!NOTE]
   > Ebben a szakaszban feltételezzük, hogy a projekt egy üres szervezetnév van konfigurálva. Ha nem, akkor be kell illesztenie a szervezet neve, az összes osztály nevét.
   > 
   > 
2. A Main.storyboard adja hozzá az összetevőket az objektumtárból alatt a képernyőfelvételen látható módon.
   
    ![][1]
   
   * **Felhasználónév**: a helyőrző szöveg A UITextField *adjon meg felhasználónevet*, azonnal eredmények címkével, és a bal és jobb margó korlátozott a küldés alatt, és a küldési eredmények címke alatt.
   * **Jelszó**: a helyőrző szöveg A UITextField *jelszó megadása*, azonnal a felhasználónév alatt szöveges mezőt, és korlátozott, a bal és jobb margó és a felhasználónév szövegmezőt alatt. Ellenőrizze a **szövegbeviteli biztonságos** attribútum megtekintő lehetőséget a *vissza kulcs*.
   * **Jelentkezzen be**: A UIButton feliratú azonnal a jelszó szövegmező alatt, és törölje a jelet a **engedélyezve** attribútumok megtekintő lehetőséget a *vezérlő – tartalom*
   * **WNS**: címke és kapcsoló ahhoz, hogy a Windows értesítési szolgáltatás értesítést küld, ha a központi telepítés le van. Tekintse meg a [Windows bevezetés](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag.
   * **GCM**: címke és a kapcsoló az értesítés küldését a Google Cloud Messaging, ha a központi telepítés lett. Lásd: [Android bevezetés](notification-hubs-android-push-notification-google-gcm-get-started.md) oktatóanyag.
   * **APNS**: címke és kapcsoló engedélyezése az Apple Platform Notification szolgáltatással küldött.
   * **Recipent felhasználónév**: a helyőrző szöveg A UITextField *címzett felhasználónév címke*, azonnal a GCM alatt címkével, és a bal és jobb margó és a GCM-címke alatt korlátozott.

    Néhány összetevőt hozzáadott a [Ismerkedés a Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) oktatóanyag.

1. **CTRL** ViewController.h húzza a összetevői a nézetben, és adja hozzá ezek új értékesítési lehetőségek.
   
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
2. A ViewController.h, adja hozzá a következő `#define` alatt az importálási utasítást. Helyettesítő a *< írja be a háttérkiszolgáló végpont\>*  helyőrző a célként megadott URL-címet, a háttéralkalmazás az előző szakaszban központi telepítéséhez használt. Például *http://you_backend.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. A projekt, hozzon létre egy új **Cocoa Touch osztály** nevű **RegisterClient** való az ASP.NET-háttér létrehozott. Hozzon létre a osztályt való öröklődés `NSObject`. Majd adja hozzá a következő kódot a RegisterClient.h a.
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. A RegisterClient.m frissítés a `@interface` szakasz:
   
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
5. Cserélje le a `@implementation` című szakasza a következő kóddal RegisterClient.m.

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

    A fenti kódot valósítja meg az útmutató a cikkben ismertetett logika [az alkalmazás háttérrendszeréből regisztrálása](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) a háttéralkalmazás NSURLSession REST elvégzéséhez használt hívások, és helyileg tárolja a registrationId NSUserDefaults adott vissza által az értesítési központban.

    Vegye figyelembe, hogy ez az osztály van szükség a tulajdonsága **authorizationHeader** kell beállítani a megfelelő működéshez. A tulajdonság értéke a **ViewController** osztály a bejelentkezés után.

1. A ViewController.h, adjon hozzá egy `#import` RegisterClient.h utasítást. Ezután adjon hozzá egy; az az eszköz jogkivonatát, és hivatkozás egy `RegisterClient` a példányt a `@interface` szakasz:
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. ViewController.m, adja hozzá a belső metódust nyilatkozatot a `@interface` szakasz:
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> Az alábbi kódrészletben nincs a biztonságos hitelesítési séma, helyettesítse be a végrehajtása a **createAndSetAuthenticationHeaderWithUsername:AndPassword:** , az adott hitelesítési mechanizmus, amely létrehoz egy hitelesítési jogkivonatot a register ügyfél osztály, például az OAuth, az Active Directory által.
> 
> 

1. Ezt a a `@implementation` ViewController.m szakasza adja hozzá az alábbi kód, amely hozzáadja a beállítás a eszköz token és hitelesítési fejléc végrehajtására.
   
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
   
    Vegye figyelembe, hogyan az eszköz jogkivonatát beállítás lehetővé teszi, hogy a Bejelentkezés gombra. Ennek oka az, a bejelentkezési művelet részeként a view vezérlő regisztrálja, a leküldéses értesítések az a háttéralkalmazás. Emiatt nem szeretnénk bejelentkezési művelet elérhetők lesznek, amíg az eszköz jogkivonatát megfelelően be van állítva. Mindaddig, amíg a korábbi történik, az utóbbi előtt használata is leválasztja a leküldéses regisztráció a bejelentkezéshez.
2. A ViewController.m, az alábbi kódtöredékek segítségével valósítja meg a műveletet a a **bejelentkezés** gombra és a értesítést küldeni az ASP.NET-háttérrendszerből használatával metódust.
   
       - (IBAction) LogInAction: (id) küldő {/ / hitelesítési fejléc létrehozásáról és a nyilvántartás ügyfél NSString * felhasználónév = saját maga. UsernameField.text;   NSString * jelszó = saját maga. PasswordField.text;
   
           [az önkiszolgáló createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController * selfie = önkiszolgáló;   [self.registerClient registerWithDeviceToken:self.deviceToken címkék: üres andCompletion:^(NSError* error) {Ha (! hiba) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = YES;               [self MessageBox:@"Success" message:@"Registered sikeresen!"];});}}];}

        -SendNotificationASPNETBackend (a "void"): (NSString*) pns UsernameTag: (NSString*) usernameTag üzenetet: (NSString*) üzenet {NSURLSession* munkamenet = [NSURLSession sessionWithConfiguration: [ NSURLSessionConfiguration defaultSessionConfiguration] delegált: üres delegateQueue:nil];

            A pns-sel és a username címke a többi URL-címet paraméterekként átadása a háttérrendszer által igényelt NSURL * requestURL ASP.NET = [által igényelt NSURL URLWithString: [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @", BACKEND_ENDPOINT, pns, usernameTag]];

            NSMutableURLRequest * kérelem = [NSMutableURLRequest requestWithURL:requestURL];    [lekérő setHTTPMethod:@"POST"];

            A utánzatait authenticationheader beszerezni a register-ügyfél NSString * authorizationHeaderValue = [NSString stringWithFormat:@"Basic % @", self.registerClient.authenticationHeader];    [lekérő setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            Az értesítési üzenet törzse hozzáadása [setValue:@"application/json;charset=utf-8 kérelem" forHTTPHeaderField:@"Content-Type"];    [setHTTPBody kérése: [üzenet dataUsingEncoding:NSUTF8StringEncoding]];

            Hajtsa végre a send notification REST API-t az ASP.NET háttér NSURLSessionDataTask * dataTask = [munkamenet dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError  *Hiba történt) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) választ;        Ha (hiba || httpResponse.statusCode! = 200) {NSString* állapot = [NSString stringWithFormat:@"Error állapota a(z) % @: % d\nError: %@\n", pns, httpResponse.statusCode, hiba];            dispatch_async(dispatch_get_main_queue(), ^ {/ szöveg hozzáfűzése, mert minden 3 PNS-hívások is előfordulhat, hogy rendelkezik információval nézet [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];            });            NSLog(status);        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask folytatása]; }


1. A művelet frissíti a **értesítés küldése** gombot használja az ASP.NET-háttérrendszerből és bármely, a kapcsoló által engedélyezett PNS küldeni.

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



1. A függvény **ViewDidLoad**, adja hozzá a következő példányt létrehozni a RegisterClient példány és állítsa be a szövegmezők delegáltja.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. Most már a **AppDelegate.m**, távolítsa el a módszer a tartalom **alkalmazás: didRegisterForPushNotificationWithDeviceToken:** , és cserélje le a következő meggyőződni arról, hogy a view vezérlő a legújabb eszköz jogkivonatát APNs lekért tartalmazza:
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Végül a **AppDelegate.m**, győződjön meg arról, hogy a következő metódust:
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Az alkalmazás tesztelése
1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem fog működni a szimulátor).
2. Az iOS-alkalmazás felhasználói felületén adja meg egy felhasználónevet és jelszót. Bármilyen karakterlánc is lehetnek, de mindkét kell ugyanaz a karakterláncértéke. Kattintson a **bejelentkezés**.
   
    ![][2]
3. Meg kell jelennie egy előugró ablak regisztráció sikeres ad tájékoztatást. Kattintson az **OK** gombra.
   
    ![][3]
4. Az a **címzett felhasználónév címke* szöveg mezőbe írja be a felhasználó nevét címke használt egy másik eszköz regisztrációját.
5. Adjon meg egy értesítési üzenetet, és kattintson a **értesítés küldése**.  Csak azokat az eszközöket, amelyek a címzett felhasználói név címkével regisztráció a értesítési üzenet.  Csak azoknak a felhasználóknak továbbítja.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
