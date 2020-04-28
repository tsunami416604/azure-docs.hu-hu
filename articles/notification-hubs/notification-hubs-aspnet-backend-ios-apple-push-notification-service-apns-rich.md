---
title: Azure Notification Hubs Rich push
description: Ismerje meg, hogyan küldhet részletes leküldéses értesítéseket egy iOS-alkalmazásba az Azure-ból. A Objective-C és C# nyelven írt példák.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9da629929ca88f406dc503710477104be94c47e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71212190"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich push

## <a name="overview"></a>Áttekintés

Ha szeretné, hogy a felhasználók Instant Rich Content szolgáltatással legyenek elérhetők, előfordulhat, hogy egy alkalmazás az egyszerű szöveg fölé szeretné leküldeni. Ezek az értesítések népszerűsítik a felhasználói interakciókat, és olyan tartalmakat mutatnak be, mint az URL-címek, hangok, képek/kuponok stb. Ez az oktatóanyag a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) témakörre épül, és bemutatja, hogyan küldhet leküldéses értesítéseket a hasznos adatok (például a rendszerkép) használatával.

Ez az oktatóanyag kompatibilis az iOS 7 & 8 rendszerrel.

  ![][IOS1]

Magas szinten:

1. Az alkalmazás háttere:
   * A részletes adattartalom (ebben az esetben a rendszerkép) tárolása a háttér-adatbázis/helyi tárolóban
   * A gazdag értesítés AZONOSÍTÓjának küldése az eszközre
2. Alkalmazás az eszközön:
   * A gazdag adattartalomot kérő háttérrel kapcsolatba lép a kapott AZONOSÍTÓval
   * A felhasználók értesítéseit küldi az eszközön, amikor az adatok beolvasása befejeződött, és azonnal megjeleníti a hasznos adatokat, amikor a felhasználók további információra koppintanak

## <a name="webapi-project"></a>WebAPI projekt

1. A Visual Studióban nyissa meg a [felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagban létrehozott **AppBackend** projektet.
2. Szerezze be azt a rendszerképet, amelyet a felhasználók értesítésére szeretne küldeni, majd a projekt könyvtára egy **IMG** mappájába helyezi.
3. Kattintson a Megoldáskezelő **összes fájl megjelenítése** elemre, majd kattintson a jobb gombbal a **projektbe felvenni**kívánt mappára.
4. Ha a rendszerkép be van jelölve, módosítsa a Build műveletét Tulajdonságok ablak a **beágyazott erőforrásra**.

    ![][IOS2]
5. `Notifications.cs`A alkalmazásban adja hozzá a következő using utasítást:

    ```csharp
    using System.Reflection;
    ```
6. Frissítse a teljes `Notifications` osztályt a következő kóddal. Ügyeljen arra, hogy a helyőrzőket cserélje le az értesítési központ hitelesítő adataival és a képfájl nevével.

    ```csharp
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
    ```

   > [!NOTE]
   > választható A Project-erőforrások hozzáadásával és beszerzésével kapcsolatos további információkért tekintse meg a [Hogyan ágyazhat be és férhet hozzá az erőforrásokhoz a Visual C# használatával](https://support.microsoft.com/kb/319292) .

7. A `NotificationsController.cs`alkalmazásban a NotificationsController az alábbi kódrészletekkel definiálja újra. Ez egy kezdeti csendesen gazdag értesítési azonosítót küld az eszköznek, és lehetővé teszi a rendszerkép ügyféloldali lekérését:

    ```csharp
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
    ```
8. Most újra telepítjük ezt az alkalmazást egy Azure-webhelyre, hogy minden eszközről elérhetővé váljon. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
9. A közzétételi célként válassza az Azure-webhely lehetőséget. Jelentkezzen be az Azure-fiókjával, és válasszon ki egy meglévő vagy új webhelyet, és jegyezze fel a **cél URL** -tulajdonságot a **kapcsolatok** lapon. Ebben az oktatóanyagban a *háttérbeli végpontként* erre az URL-címre fogunk hivatkozni. Kattintson a **Publish** (Közzététel) gombra.

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása

Most, hogy módosította az alkalmazás-hátteret, hogy csak az értesítés *azonosítóját* küldje el, az iOS-alkalmazást fogja használni az azonosító kezeléséhez és a háttérből származó gazdag üzenet lekéréséhez.

1. Nyissa meg az iOS-projektet, és engedélyezze a távoli értesítéseket a **célok** szakaszának fő alkalmazási céljával.
2. Kattintson a **képességek lehetőségre**, kapcsolja be a **háttér üzemmódot**, és jelölje be a **távoli értesítések** jelölőnégyzetet.

    ![][IOS3]
3. Nyissa meg `Main.storyboard`a következőt:, és győződjön meg róla, hogy rendelkezik egy nézet-vezérlővel (a jelen oktatóanyag Kezdőlap nézet vezérlője) az [értesítési felhasználói](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagban.
4. Vegyen fel egy **navigációs vezérlőt** a storyboardba, és a vezérlőre húzással húzza a Kezdőlap nézet vezérlőt, hogy a **legfelső szintű nézetet** adja meg a navigáláshoz. Győződjön meg arról, hogy a csak a navigációs vezérlőhöz van kiválasztva a **kezdeti nézet vezérlő** az attributes Inspector-ben.
5. Adjon hozzá egy **nézet vezérlőt** a storyboardhoz, és adjon hozzá egy **képnézetet**. Ez az oldal a felhasználók számára jelenik meg, ha úgy dönt, hogy további információkat szeretne megtudni az értesítésre kattintva. A történetnek a következőképpen kell kinéznie:

    ![][IOS4]
6. Kattintson a storyboard **Kezdőlap nézet vezérlőre** , és győződjön meg róla, hogy a **homeViewController** az Identity Inspector alatt **Egyéni OSZTÁLYként** és **storyboard-azonosítóként** rendelkezik.
7. Tegye ugyanezt a Képnézet vezérlőre **imageViewController**.
8. Ezután hozzon létre egy új, a **imageViewController** nevű View Controller osztályt az imént létrehozott felhasználói felület kezeléséhez.
9. A **imageViewController. h**-ben adja hozzá a következőt a vezérlő Interface deklarációjában. Ügyeljen rá, hogy a két hely összekapcsolásához húzza a storyboard képnézetből a következő tulajdonságokat:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. A `imageViewController.m`-ben adja hozzá a következőt a `viewDidload`végén:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. Importálja a létrehozott rendszerkép- `AppDelegate.m`vezérlőt:

    ```objc
    #import "imageViewController.h"
    ```
12. Vegyen fel egy Interface szakaszt a következő deklarációval:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. A `AppDelegate`ben ellenőrizze, hogy az alkalmazás regisztrálja-e a `application: didFinishLaunchingWithOptions`csendes értesítéseket a következőben:

    ```objc
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
    ```

14. A következő implementációban `application:didRegisterForRemoteNotificationsWithDeviceToken` helyettesítse be a storyboard felhasználói felületének módosítását:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Ezután adja hozzá a következő metódusokat `AppDelegate.m` a rendszerkép lekéréséhez a végpontból, és küldjön helyi értesítést a beolvasás befejezésekor. Ügyeljen arra, hogy a helyőrzőt `{backend endpoint}` a háttérbeli végponttal helyettesítse:

    ```objc
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
    ```
16. A fenti helyi értesítést úgy kezelheti, ha megnyitja a képnézeti vezérlőt `AppDelegate.m` a következő módszerekkel:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
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
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A XCode-ben futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem fognak működni a szimulátorban).
2. Az iOS-alkalmazás felhasználói felületén adja meg a hitelesítéshez tartozó felhasználónevet és jelszót, majd kattintson a **Bejelentkezés**elemre.
3. Kattintson a **leküldéses küldés** lehetőségre, és megjelenik az alkalmazáson belüli riasztás. Ha a **további**gombra kattint, a rendszer az alkalmazás-háttérbe felvenni kívánt képet fogja elhelyezni.
4. Kattintson a **leküldés küldése** gombra, és azonnal nyomja meg az eszköz Home (Kezdőlap) gombját. Néhány pillanat múlva leküldéses értesítést fog kapni. Ha rákoppint, vagy kattintson a továbbiak lehetőségre, a rendszer az alkalmazást és a gazdag képtartalmat fogja megtekinteni.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
