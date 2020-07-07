---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060490"
---
### <a name="configure-required-ios-packages"></a>A szükséges iOS-csomagok konfigurálása

A csomag [automatikusan össze van kapcsolva](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) az alkalmazás létrehozásakor. A natív hüvelyek telepítéséhez mindössze annyit kell tennie:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Az info. plist és a jogosultságok konfigurálása. plist

1. Lépjen be a "PushDemo/iOS" mappába, és nyissa meg a "PushDemo. xcworkspace" munkaterületet, válassza ki a felső "PushDemo" projektet, és válassza az "aláírás & képességek" fület.

1. A csomag azonosítójának frissítése a létesítési profilban használt értéknek megfelelően.

1. Vegyen fel két új funkciót a-"+" gomb használatával:

    - Háttér mód funkció és a távoli értesítések ketyegve.
    - Leküldéses értesítések funkció

### <a name="handle-push-notifications-for-ios"></a>Leküldéses értesítések kezelése iOS-hez

1. Nyissa meg a "AppDelegate. h" parancsot, és adja hozzá a következő importálást:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. A "AppDelegate" által támogatott protokollok listájának frissítése a következő hozzáadásával `UNUserNotificationCenterDelegate` :

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Nyissa meg a "AppDelegate. m" kifejezést, és konfigurálja az összes szükséges iOS-visszahívást:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
