---
title: Az Azure Notification Hubs Rich formátumú leküldés
description: Ismerje meg, hogyan gazdag leküldéses értesítések küldése iOS-alkalmazásokba az Azure-ból. Objective-C és C# nyelven írt kódmintákat.
documentationcenter: ios
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: f2fa2b8b43a4840d8c6956efb7704327cca78d7c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232512"
---
# <a name="azure-notification-hubs-rich-push"></a>Az Azure Notification Hubs Rich formátumú leküldés
## <a name="overview"></a>Áttekintés
Annak érdekében, hogy a felhasználók azonnali gazdag tartalmát az igényeik szerint, egy alkalmazás érdemes leküldéses túl egyszerű szöveg. Ezek az értesítések elősegítésére felhasználói interakció érdekében, valamint a tartalom URL-címek, hangok, képeket vagy kuponok és. Ebben az oktatóanyagban épül, amely a [– felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) a témakör bemutatja, hogyan küldhetők leküldéses értesítések, amelyek hasznos (például képek) és.

Ez az oktatóanyag a kompatibilis az iOS 7 és a 8.

  ![][IOS1]

Magas szintű:

1. Az alkalmazás háttérrendszere:
   * A gazdag hasznos adatokat tárolja (ebben az esetben kép) a háttérbeli adatbázis/helyi tárolóban
   * Elküldi az eszköznek az gazdag értesítés azonosítója
2. Alkalmazás az eszközön:
   * Kapcsolatba lép a háttérrendszer a gazdag hasznos kap azonosítójú kérése
   * Felhasználók értesítéseket küld az eszközön, amikor az adatok beolvasása befejeződött, és bemutatja a hasznos adatokat, azonnal, amikor a felhasználók további információkért koppintson

## <a name="webapi-project"></a>WebAPI-projekt
1. A Visual Studióban nyissa meg a **AppBackend** a létrehozott projekt a [– felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyag.
2. Szerezzen be egy képet szeretne értesíteni a felhasználókat, és helyezze egy **img** mappa a projektkönyvtárban.
3. Kattintson a **minden fájl megjelenítése** a Megoldáskezelőben, és kattintson a jobb gombbal a mappa **tartalmazza a projekt**.
4. A kiválasztott lemezképhez, módosítsa a Tulajdonságok ablakban, a Build Action **beágyazott erőforrás**.
   
    ![][IOS2]
5. A **Notifications.cs**, adja hozzá a következő using utasítást:
   
        using System.Reflection;
6. A teljes frissítés **értesítések** osztályban az alábbi kódra. Győződjön meg arról, hogy cserélje le a helyőrzőket a notification hub hitelesítő adatok és a bináris fájl neve.
   
        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }
   
        public class Notifications {
            public static Notifications Instance = new Notifications();
   
            private List<Notification> notifications = new List<Notification>();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }
   
            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };
   
                notifications.Add(notification);
   
                return notification;
            }
   
            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }
   
   > [!NOTE]
   > (nem kötelező) Tekintse meg [beágyazási és erőforrások eléréséhez a Vizualizáció C# ](http://support.microsoft.com/kb/319292) hogyan adhat hozzá, és szerezze be a Projekt erőforrások további tájékoztatást.
   > 
   > 
7. A **notificationscontroller.cs fájlhoz**, definiálja újra **NotificationsController** az alábbi kódrészletek az. Ez egy kezdeti csendes gazdag értesítés azonosítója küld az eszközre, és lehetővé teszi, hogy a kép lekérésének ügyféloldali:
   
        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);
   
            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");
   
            return result;
        }
   
        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");
   
            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";
   
            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
8. Most már azt fogja újra az alkalmazást üzembe helyezni egy Azure-webhelyen annak érdekében, hogy minden eszközről elérhető. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
9. A közzétételi célként válassza ki Azure-webhelyen. Jelentkezzen be az Azure-fiókjával, és válassza ki egy meglévő vagy új webhely és jegyezze fel a a **cél URL-címe** tulajdonságot a **kapcsolat** fülre. Az oktatóanyag további részében erre az URL-címre fogunk hivatkozni a *háttérrendszer végpontjaként*. Kattintson a **Publish** (Közzététel) gombra.

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása
Most, hogy módosította a háttéralkalmazás számára küldése csak a *azonosító* egy értesítés, az iOS-alkalmazás azonosítóval, és a részletes üzenetek beolvasása a háttérrendszerből változik.

1. Nyissa meg az iOS-projektet, és nyissa meg az alkalmazás fő cél a távoli értesítések engedélyezése a **célok** szakaszban.
2. Kattintson a **képességek**, kapcsolja be a **háttérbeli üzemmódok**, és ellenőrizze a **távoli értesítések** jelölőnégyzetet.
   
    ![][IOS3]
3. Lépjen a **Main.storyboard**, és ellenőrizze, hogy rendelkezik egy View-Controller (néven kezdőlap Nézetvezérlője ebben az oktatóanyagban) származó [a felhasználó értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyag.
4. Adjon hozzá egy **navigációs vezérlő** a storyboard fájlt definiál, és a vezérlő húzással kezdőlap nézet-vezérlő, hogy a **legfelső szintű nézet** navigáció. Győződjön meg arról, hogy a **kezdeti View-Controller van** attribútumok vizsgáló ki van jelölve, a navigációs vezérlő csak.
5. Adjon hozzá egy **Nézetvezérlője** történet, és adja hozzá egy **kép**. Ez az, hogy a felhasználók látják, amikor kiválasztják a notifiication kattintva további lap. A storyboard fájlt definiál az alábbihoz hasonlóan kell kinéznie:
   
    ![][IOS4]
6. Kattintson a a **kezdőlap Nézetvezérlője** storyboard fájlt definiál, és ellenőrizze, hogy rendelkezik **homeViewController** , annak **egyéni osztály** és **Storyboard azonosító**az identitás vizsgáló alatt.
7. Tegye meg ugyanezt a lemezkép View-Controller, **imageViewController**.
8. Ezután hozzon létre egy új nézet vezérlőosztály című **imageViewController** kezelje az újonnan létrehozott felhasználói felületén.
9. A **imageViewController.h**, adja hozzá a következő, a vezérlő felületet nyilatkozatokat. Ellenőrizze, hogy a vezérlőelem-húzza a történet lemezkép nézetből ezeket a tulajdonságokat, a kettő:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. A **imageViewController.m**, adja hozzá a következő végén **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. A **AppDelegate.m**, importálja a létrehozott lemezkép vezérlő:
    
        #import "imageViewController.h"
12. Adjon hozzá egy felület szakaszt az alábbi nyilatkozatot:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. A **AppDelegate**, ellenőrizze, hogy az alkalmazás regisztrál a beavatkozás nélküli értesítések **application: didFinishLaunchingWithOptions**:
    
        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];
    
        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");
    
            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";
    
            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];
    
            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

1. A következő implementációban a parancs **alkalmazás: didRegisterForRemoteNotificationsWithDeviceToken** felhasználói felület figyelembe módosítja a történet tennie:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Adja hozzá a következő módszerekkel **AppDelegate.m** a rendszerkép lekérése a végpont és a egy helyi értesítés küldése, ha a lekérési befejeződött. Ügyeljen arra, hogy cserélje le a helyőrző `{backend endpoint}` a háttér-végponthoz:
   
       NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";
   
       // Helper: retrieve notification content from backend with rich notification id
       - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
           UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
           homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
           NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
           // Check if authenticated
           if (!authenticationHeader) return;
   
           NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];
   
           NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
           NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
           [request setHTTPMethod:@"GET"];
           NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
           [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
   
           NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
   
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
               if (!error && httpResponse.statusCode == 200) {
                   // From NSData to UIImage
                   self.imagePayload = [UIImage imageWithData:data];
   
                   completion(nil);
               }
               else {
                   NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                   if (error)
                       completion(error);
                   else {
                       completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                   }
               }
           }];
           [dataTask resume];
       }
   
       // Handle silent push notifications when id is sent from backend
       - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
           self.userInfo = userInfo;
           int richId = [[self.userInfo objectForKey:@"richId"] intValue];
           NSString* richType = [self.userInfo objectForKey:@"richType"];
   
           // Retrieve image data
           if ([richType isEqualToString:@"img"]) {  
               [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                   if (!error){
                       // Send local notification
                       UILocalNotification* localNotification = [[UILocalNotification alloc] init];
   
                       // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                       localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                       localNotification.userInfo = self.userInfo;
                       localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                       localNotification.timeZone = [NSTimeZone defaultTimeZone];
   
                       // iOS8 categories
                       if (self.iOS8) {
                           localNotification.category = @"richPush";
                       }
   
                       [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                       handler(UIBackgroundFetchResultNewData);
                   }
                   else{
                       handler(UIBackgroundFetchResultFailed);
                   }
               }];
           }
           // Add "else if" here to handle more types of rich content such as url, sound files, etc.
       }
3. A helyi értesítések fent kezelésére nyissa meg a lemezkép nézetvezérlője a mentést **AppDelegate.m** a következő módszerekkel:
   
       // Helper: redirect users to image view controller
       - (void)redirectToImageViewWithImage: (UIImage *)img {
           UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
           UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                    bundle: nil];
           imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
           // Pass data/image to image view controller
           imgViewController.imagePayload = img;
   
           // Redirect
           [navigationController pushViewController:imgViewController animated:YES];
       }
   
       // Handle local notification sent above in didReceiveRemoteNotification
       - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
           if (application.applicationState == UIApplicationStateActive) {
               // Show in-app alert with an extra "more" button
               UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
   
               [alert show];
           }
           // App becomes active from user's tap on notification
           else {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
       }
   
       // Handle buttons in in-app alerts and redirect with data/image
       - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
           // Handle "more" button
           if (buttonIndex == 1)
           {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
           // Add "else if" here to handle more buttons
       }
   
       // Handle notification setting actions in iOS8
       - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
           // Handle richPush related buttons
           if ([identifier isEqualToString:@"richPushMore"]) {
               [self redirectToImageViewWithImage:self.imagePayload];
           }
           completionHandler();
       }

## <a name="run-the-application"></a>Az alkalmazás futtatása
1. Az xcode-ban az alkalmazás futtatása egy fizikai iOS-eszközön (leküldéses értesítések nem fog működni a szimulátor).
2. Az iOS-alkalmazás felhasználói felületén, adja meg egy felhasználónevet és jelszót, ugyanazt az értéket a hitelesítéshez, és kattintson **bejelentkezés**.
3. Kattintson a **küldjön leküldéses** és alkalmazáson belüli riasztást kell megjelennie. Kattintva **további**, úgy döntött, hogy az alkalmazás háttérrendszere bevonni a lemezképhez jut.
4. Is **küldjön leküldéses** azonnal nyomja meg az eszköz home gombra. Néhány perc múlva leküldéses értesítést fog kapni. Ha, koppintson vagy kattintson a további, jut az alkalmazás és a sokrétű kép tartalmához.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
