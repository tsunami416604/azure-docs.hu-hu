---
title: Az Azure értesítési központok bővített leküldéses
description: Megtudhatja, hogy miként küldhet gazdag leküldéses értesítéseket egy iOS-alkalmazásba az Azure-ból. A C és C# célkitűzéssel írt kódminták.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212190"
---
# <a name="azure-notification-hubs-rich-push"></a>Az Azure értesítési központok bővített leküldéses

## <a name="overview"></a>Áttekintés

Annak érdekében, hogy a felhasználók azonnali gazdag tartalommal rendelkezők legyenek, előfordulhat, hogy egy alkalmazás túl szeretne lépni az egyszerű szövegen. Ezek az értesítések elősegítik a felhasználói interakciókat, és olyan tartalmakat mutatnak be, mint például URL-ek, hangok, képek/kuponok stb. Ez az oktatóanyag a Felhasználók értesítése témakörre épül, és [bemutatja,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) hogyan küldhet leküldéses értesítéseket, amelyek hasznos adatokat (például képet) tartalmaznak.

Ez a bemutató kompatibilis az iOS 7 & 8.

  ![][IOS1]

Magas szinten:

1. Az alkalmazás háttér- játéktere:
   * A bővített hasznos adat (ebben az esetben a lemezkép) a háttéradatbázisban/helyi tárolóban tárolja
   * A bővített értesítés azonosítójának küldése az eszközre
2. Alkalmazás az eszközön:
   * Felveszi a kapcsolatot a háttér-irányítóval, amely a gazdag rakományt kéri a kapott azonosítóval
   * Értesítéseket küld a felhasználóknak az eszközön, amikor az adatok lekérése befejeződött, és azonnal megjeleníti a hasznos terhet, amikor a felhasználók koppintanak, hogy többet megtudjanak

## <a name="webapi-project"></a>WebAPI projekt

1. A Visual Studióban nyissa meg a [Felhasználók értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagban létrehozott **AppBackend** projektet.
2. Szerezzen be egy képet, amelyről értesíteni szeretné a felhasználókat, és helyezze el egy **img** mappába a projekt könyvtárában.
3. Kattintson az Összes fájl megjelenítése a Megoldáskezelőben **parancsra,** és kattintson a jobb gombbal a **Projectbe beszeretne vonni**kívánt mappára.
4. Ha a kép ki van jelölve, módosítsa a Build Action in Properties ablakot **Beágyazott erőforrásra.**

    ![][IOS2]
5. A `Notifications.cs`alkalmazásban adja hozzá a következőutasítást:

    ```csharp
    using System.Reflection;
    ```
6. Frissítse az `Notifications` egész osztályt a következő kóddal. Ügyeljen arra, hogy cserélje le a helyőrzők az értesítési központ hitelesítő adatait és a képfájl nevét.

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
   > (nem kötelező) A Projekterőforrások hozzáadásáról és beszerzéséről további információt az [erőforrások beágyazása és elérése a Visual C# használatával](https://support.microsoft.com/kb/319292) című útmutatóban talál.

7. A `NotificationsController.cs`alkalmazásban definiálja újra az 'NotificationsController a következő kódrészleteket. Ez egy kezdeti csendes, bővített értesítési azonosítót küld az eszközre, és lehetővé teszi a lemezkép ügyféloldali lekérését:

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
8. Most újra telepítjük ezt az alkalmazást egy Azure-webhelyre annak érdekében, hogy minden eszközről elérhetővé tesszük. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
9. Válassza ki az Azure-webhelyet közzétételi célként. Jelentkezzen be Azure-fiókjával, és válasszon ki egy meglévő vagy új webhelyet, és jegyezze fel a **cél URL-tulajdonságát** a **Kapcsolat** lapon. Ezt az URL-címet az oktatóanyag későbbi részében *a háttérvégpontként* fogjuk hivatkozni. Kattintson a **Publish** (Közzététel) gombra.

## <a name="modify-the-ios-project"></a>Az iOS-projekt módosítása

Most, hogy módosította az alkalmazás háttérszámítógépét, hogy csak egy értesítés *azonosítóját* küldje el, meg fogja változtatni az iOS-alkalmazást az azonosító kezeléséhez, és lekéri a bővített üzenetet a háttérrendszerből.

1. Nyissa meg az iOS-projektet, és engedélyezze a távoli értesítéseket a Fő alkalmazáscél megnyitásával a **Célok** szakaszban.
2. Kattintson a **Képességek gombra,** kapcsolja be a **Háttérmódokat,** és jelölje be a **Távoli értesítések** jelölőnégyzetet.

    ![][IOS3]
3. Nyissa `Main.storyboard`meg a programot, és győződjön meg arról, hogy rendelkezik egy nézetvezérlővel (az oktatóanyaghome nézetvezérlője) a [Felhasználó értesítése](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) oktatóanyagból.
4. Vegyen fel egy **navigációs vezérlőt** a történetbe, és húzza a vezérlőt a Kezdőlap nézet vezérlőre, hogy az a navigáció **gyökérnézeté** legyen. Győződjön meg arról, hogy az **Attribútumellenőr kezdeti nézetvezérlője** csak a navigációs vezérlőhöz van kiválasztva.
5. **Nézetvezérlő** hozzáadása a történethez és **képnézet hozzáadása**. Ez az az oldal, amelyet a felhasználók látni fognak, ha úgy döntenek, hogy többet szeretnenak megtudni az értesítésre kattintva. A történetnek a következőképpen kell kinéznie:

    ![][IOS4]
6. Kattintson a **Home View Controller** a storyboard, és győződjön meg arról, hogy **homeViewController,** mint az **egyéni osztály** és **storyboard azonosító** alatt Identity inspector.
7. Tegye ugyanezt a Képnézet-vezérlővel is, mint **a imageViewController**.
8. Ezután hozzon létre egy új View Controller osztály című **imageViewController** kezelni a felhasználói felület az imént létrehozott.
9. Az **imageViewController.h**alkalmazásban adja hozzá a következőket a vezérlő illesztődeklarációihoz. Győződjön meg arról, hogy a vezérlő-húzás a történet kép nézete a következő tulajdonságokat, hogy csatolja a két:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. A `imageViewController.m`alkalmazásban a következőt `viewDidload`kell a következőre tenni:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. A `AppDelegate.m`alkalmazásban importálja a létrehozott képvezérlőt:

    ```objc
    #import "imageViewController.h"
    ```
12. Adjon hozzá egy felületi szakaszt a következő nyilatkozattal:

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
13. A `AppDelegate`alkalmazásban győződjön meg arról, `application: didFinishLaunchingWithOptions`hogy az alkalmazás regisztrálja a csendes értesítéseket a következő ben:

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

14. Helyettesítse a `application:didRegisterForRemoteNotificationsWithDeviceToken` következő implementációt a történet felhasználói felületének változásainak figyelembevételéhez:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Ezután adja hozzá `AppDelegate.m` a következő módszereket a lemezkép végpontról való lekéréséhez, és helyi értesítést küldjön, amikor a lekérés befejeződött. Győződjön meg arról, hogy a helyőrzőt `{backend endpoint}` a háttérvégpontkal helyettesíti:

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
16. Kezelje a fenti helyi értesítést a képnézet-vezérlő `AppDelegate.m` megnyitásával a következő módszerekkel:

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

1. Az XCode-ban futtassa az alkalmazást egy fizikai iOS-eszközön (a leküldéses értesítések nem fognak működni a szimulátorban).
2. Az iOS alkalmazás felhasználói felületén adjon meg egy azonos értékű felhasználónevet és jelszót a hitelesítéshez, és kattintson a **Bejelentkezés**gombra.
3. Kattintson **a Küldés küldése gombra,** és meg kell jelennie egy alkalmazáson belüli riasztásnak. Ha az **Egyebek**gombra kattint, akkor az alkalmazás háttérrendszerébe felvenni kívánt képre kerül.
4. A **Lenyomás küldése gombra** kattintva azonnal megnyomhatja a készülék otthoni gombját. Néhány percen belül leküldéses értesítést kap. Ha megérinti, vagy az Egyebek gombra kattint, a rendszer az alkalmazásba és a bővített képtartalomba kerül.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
