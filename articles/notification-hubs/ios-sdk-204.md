---
title: Leküldéses értesítések küldése iOS-re az Azure Notification Hubs és az iOS SDK 2.0.4-verziójának használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure Notification Hubs és az Apple push Notification szolgáltatás leküldéses értesítések küldésére iOS-eszközökre (2.0.4-verzió).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: ffa562a734e0e6f898aaff89622362080bf1a053
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318194"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-204"></a>Oktatóanyag: leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs (2.0.4 verzió) használatával

Ez az oktatóanyag bemutatja, hogyan küldhet leküldéses értesítéseket iOS-alkalmazásokba az Azure Notification Hubs használatával az Azure Notification Hubs SDK 2.0.4.

Ez az oktatóanyag a következő lépéseken vezet végig:

- Hozzon létre egy minta iOS-alkalmazást.
- Az iOS-alkalmazás csatlakoztatható az Azure Notification Hubshoz.
- Teszt leküldéses értesítések küldése.
- Ellenőrizze, hogy az alkalmazás fogad-e értesítéseket.

Az oktatóanyag teljes kódját letöltheti a [githubról](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételekre lesz szüksége:

- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy iPhone vagy iPad, amely az iOS 10-es vagy újabb verzióját futtatja.
- A fizikai eszköz regisztrálva van az [Apple Portalon](https://developer.apple.com/), és a tanúsítványhoz van társítva.

Mielőtt továbblépne, mindenképpen tekintse át az [Azure Notification Hubs iOS-alkalmazásokkal](ios-sdk-get-started.md)való használatáról szóló oktatóanyagot, amellyel beállíthatja és konfigurálhatja a leküldéses hitelesítő adatokat az értesítési központban. Ha még nem rendelkezik az iOS-fejlesztéssel kapcsolatos korábbi tapasztalatokkal, kövesse az alábbi lépéseket.

> [!NOTE]
> A leküldéses értesítések konfigurációs követelményei miatt a leküldéses értesítéseket az iOS-emulátor helyett fizikai iOS-eszközön (iPhone vagy iPad) kell üzembe helyeznie és tesztelni.

## <a name="connect-your-ios-app-to-notification-hubs"></a>iOS-alkalmazás összekapcsolása a Notification Hubs szogáltatással

1. A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás**   sablonját.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Sablon kiválasztása":::

2. Ha az új projekt beállításait állítja be, ügyeljen arra, hogy ugyanazt a **terméknév**   -és **szervezeti azonosítót**használja,   amelyet a köteg azonosítójának az Apple fejlesztői portálon való beállításakor használt.

3. A Project Navigator alatt válassza ki a projekt nevét a **célok**területen, majd válassza az **aláírási & képességek**   lapot. Győződjön meg arról, hogy a megfelelő **csapatot**választotta   Apple Developer-fiókjához. Az Xcode a csomagazonosító alapján automatikusan lekéri a korábban létrehozott kiépítési profilt.

   Ha nem jelenik meg az Xcode-ban létrehozott új létesítési profil, frissítse az aláíró identitása profiljait. Kattintson a menüsávban a **Xcode**elemre, kattintson a beállítások elemre,   kattintson a **Preferences** **fiók**   fülre, kattintson a **részletek megtekintése**   gombra, kattintson az aláíró identitására, majd kattintson a jobb alsó sarokban található frissítés gombra.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Sablon kiválasztása":::

4. Az **aláírási & képességek**   lapon válassza a **+ képesség**lehetőséget. Az engedélyezéséhez kattintson duplán a **leküldéses értesítések**elemre   .

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Sablon kiválasztása"
      ```

      - Következő lépésként frissítse a Build függőségeit:

      ```shell
      $ carthage update
      ```

      A Carthage használatával kapcsolatos további információkért tekintse meg a [Carthage GitHub-tárházat](https://github.com/Carthage/Carthage).

   - Integráció a bináris fájlok a projektbe való másolásával: a bináris fájlok a projektbe való másolásával integrálható a következőképpen:

        - Töltse le a zip-fájlként megadott [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-android/releases)   -keretrendszert, és csomagolja ki.

        - A Xcode-ben kattintson a jobb gombbal a projektre, majd kattintson a **fájlok hozzáadása**   lehetőségre, hogy hozzáadja a **WindowsAzureMessaging. Framework**   mappát a Xcode-projekthez. Válassza a **Beállítások lehetőséget**   , és szükség esetén győződjön meg az **elemek másolásáról**   , majd kattintson a **Hozzáadás**gombra.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Sablon kiválasztása":::

6. Vegyen fel egy új header-fájlt a projekt **állandók. h**nevű fájljába. Ehhez kattintson a jobb gombbal a projekt nevére, és válassza az **új fájl.**.. lehetőséget. Ezután válassza ki a **fejléc fájlt**. Ez a fájl tárolja az állandókat az értesítési központhoz. Ezután válassza a **tovább**lehetőséget. Nevezze el a fájl **állandóit. h**.

7. Adja hozzá a következő kódot az állandók. h fájlhoz:

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Adja hozzá a Konstanss. h implementációs fájlt. Ehhez kattintson a jobb gombbal a projekt nevére, és válassza az **új fájl.**.. lehetőséget. Válassza ki a **Objective-C fájlt**, majd kattintson a **tovább**gombra. Nevezze el a fájl **állandóit. m**.

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="Sablon kiválasztása"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. Nyissa meg a Project **AppDelegate. h**   fájlt, és cserélje le a tartalmát a következő kódra:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

11. A Project **AppDelegate. m**   fájlban adja hozzá a következő  `import`   utasításokat:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. A **AppDelegate. m**   fájlban is adja hozzá a következő kódot a  `didFinishLaunchingWithOptions`   metódushoz az iOS-verzió alapján. Ez a kód regisztrálja az eszközleíróját az APNs szolgáltatással:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. Ugyanebben a **AppDelegate. m**   fájlban cserélje le az összes kódot a  `didFinishLaunchingWithOptions`   következő kód után:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
    NSLog(@"Received remote (silent) notification");
    [self logNotificationDetails:userInfo];

    //
    // Let the system know the silent notification has been processed.
    //
    completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
        [tags addObjectsFromArray:tagsArray];
    }

    // Register the device with the Notification Hub.
    // If the device has not already been registered, this will create the registration.
    // If the device has already been registered, this will update the existing registration.
    //
    SBNotificationHub* hub = [self getNotificationHub];
    [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        } else {
            [self showAlert:@"Registered" withTitle:@"Registration Status"];
        }
    }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
    NSLog(@"Received notification while the application is in the foreground");

    // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
    // itself (and potentially modify the default system behavior).

    // Handle the notification by displaying custom UI.
    //
    [self showNotification:notification.request.content.userInfo];

    // Use 'options' to specify which default behaviors to enable.
    // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
    // - UNAuthorizationOptionSound: Play the sound associated with the notification.
    // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
    //
    // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
    //
    completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
    NSLog(@"Received notification while the application is in the background");

    // The system calls this delegate method when the user taps or responds to the system notification.

    // Handle the notification response by displaying custom UI
    //
    [self showNotification:response.notification.request.content.userInfo];

    // Let the system know the response has been processed.
    //
    completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

    return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
    UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

    UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
    [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
        if (error != nil) {
            NSLog(@"Error requesting for authorization: %@", error);
        }
    }];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
    //
    // Unregister the device with the Notification Hub.
    //
    SBNotificationHub *hub = [self getNotificationHub];
    [hub unregisterNativeWithCompletion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error unregistering for push: %@", error);
        } else {
            [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
        }
    }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
    if (userInfo != nil) {
        UIApplicationState state = [UIApplication sharedApplication].applicationState;
        BOOL background = state != UIApplicationStateActive;
        NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
    }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
    if (title == nil) {
        title = @"Alert";
    }
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
    [self logNotificationDetails:userInfo];

    NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Ez a kód a **konstanss. h**-ben megadott kapcsolati adatok használatával csatlakozik az értesítési központhoz. Ezután megadja az eszköz jogkivonatát az értesítési központnak, így a hub értesítéseket küldhet.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController-fejléc fájljának létrehozása

1. Az előző utasításokhoz hasonlóan adjon hozzá egy másik, **NotificationDetailViewController. h**nevű fejlécet. Cserélje le az új header fájl tartalmát a következő kódra:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Adja hozzá a **NotificationDetailViewController. m**implementációs fájlt. Cserélje le a fájl tartalmát a következő kódra, amely megvalósítja a UIViewController metódusokat:

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
    self = [super initWithNibName:@"NotificationDetail" bundle:nil];
    if (self) {
        _userInfo = userInfo;
    }
    return self;
   }

   - (void)viewDidLayoutSubviews {
    [self.titleLabel sizeToFit];
    [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    NSString *title = nil;
    NSString *body = nil;

    NSDictionary *aps = [_userInfo valueForKey:@"aps"];
    NSObject *alertObject = [aps valueForKey:@"alert"];
    if (alertObject != nil) {
        if ([alertObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *alertDict = (NSDictionary *)alertObject;
            title = [alertDict valueForKey:@"title"];
            body = [alertObject valueForKey:@"body"];
        } else if ([alertObject isKindOfClass:[NSString class]]) {
            body = (NSString *)alertObject;
        } else {
            NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
        }
    }

    if (title == nil) {
        title = @"<unset>";
    }

    if (body == nil) {
        body = @"<unset>";
    }

    self.titleLabel.text = title;
    self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. A Project **ViewController. h**   fájlban adja hozzá a következő  `import`   utasításokat:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. A **ViewController. h**-ben is adja hozzá a következő tulajdonság deklarációkat a  `@interface`   deklaráció után:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. A projekt **ViewController. m**   implementációs fájljában cserélje le a fájl tartalmát a következő kódra:

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    // Simple method to dismiss keyboard when user taps outside of the UITextField.
    [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    // Load raw tags text from storage and initialize the text field
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

   // Delegate processing the register action to the app delegate.
   [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. Hozza létre és futtassa az alkalmazást az eszközön az esetleges hibák kereséséhez.

## <a name="send-test-push-notifications"></a>Teszt leküldéses értesítések küldése

A Azure Portal a **küldési teszt**lehetőséggel tesztelheti az alkalmazásban az értesítéseket    [Azure portal](https://portal.azure.com/). A parancs egy leküldéses tesztértesítést küld az eszközre.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Sablon kiválasztása":::

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis könyvtár használatával, mint a Mobile Apps vagy az ASP.NET. Ha egy könyvtár nem érhető el a háttérrendszer számára, akkor a REST API közvetlenül is használhatja az értesítési üzenetek küldéséhez.

Az alábbiakban felsorolunk néhány olyan oktatóanyagot, amelyet érdemes lehet áttekinteni az értesítések küldéséhez:

- Azure Mobile Apps: a [leküldéses értesítések hozzáadása iOS-alkalmazáshoz](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)című témakörből megtudhatja, hogyan küldhet értesítéseket az Notification Hubs-vel integrált Mobile apps háttérből.
- ASP.NET: [a Notification Hubs használatával leküldéses értesítéseket küldhet a felhasználóknak](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: Tekintse meg a Java [-ból való használatról](notification-hubs-java-push-notification-tutorial.md)szóló értesítések a Java Notification Hubs használatával történő küldését ismertető témakört   . Ez az Eclipse-ben lett tesztelve Android-fejlesztéshez.
- PHP: [Notification Hubs használata a PHP-ből](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Annak ellenőrzése, hogy az alkalmazás fogad-e leküldéses értesítéseket

A leküldéses értesítések iOS rendszeren történő teszteléséhez üzembe kell helyezni az alkalmazást egy fizikai iOS-eszközön. Az iOS-szimulátor használatával nem lehet Apple leküldéses értesítéseket küldeni.

1. Futtassa az alkalmazást, és ellenőrizze, hogy a regisztráció sikeres-e, majd nyomja meg **az OK**gombot.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Sablon kiválasztása":::

2. Következő lépésként küldjön egy leküldéses értesítést a [Azure Portalról](https://portal.azure.com/)az előző szakaszban leírtak szerint.

3. A rendszer elküldi a leküldéses értesítést minden olyan eszköznek, amely regisztrálva van az értesítéseknek a megadott értesítési központból való fogadásához.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Sablon kiválasztása":::

## <a name="next-steps"></a>További lépések

Ebben az egyszerű példában leküldéses értesítéseket küld a regisztrált iOS-eszközökre. Ha szeretné megtudni, hogyan küldhet leküldéses értesítéseket adott iOS-eszközökre, folytassa a következő oktatóanyaggal:

[Oktatóanyag: leküldéses értesítések adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

További információkért tekintse át a következő cikkeket:

- [Az Azure Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API-k](/rest/api/notificationhubs/)
- [Notification Hubs SDK a háttérbeli műveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK a GitHubon](https://github.com/Azure/azure-notificationhubs)
- [Regisztrálás az alkalmazás háttérben](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Címkék használata](notification-hubs-tags-segment-push-message.md)
- [Egyéni sablonok használata](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus hozzáférés-vezérlés közös hozzáférési aláírásokkal](../service-bus-messaging/service-bus-sas.md)
- [SAS-tokenek programozott módon történő előállítása](/rest/api/eventhub/generate-sas-token)
- [Apple Security: általános kriptográfia](https://developer.apple.com/security/)
- [UNIX-kor időpontja](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)