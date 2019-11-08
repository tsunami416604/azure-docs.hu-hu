---
title: Leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogy miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez iOS-alkalmazások esetén.
services: notification-hubs
documentationcenter: ios
keywords: leküldéses értesítés,leküldéses értesítések,ios leküldéses értesítések
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 452ccfc796fcd2a390c7380f4c6b2ced2057dc3b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822355"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Oktatóanyag: Leküldéses értesítések küldése iOS-alkalmazásokba az Azure Notification Hubs használatával

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Ebben az oktatóanyagban az Azure Notification Hubs használatával leküldéses értesítéseket küld egy iOS-alkalmazásnak. Létre fog hozni egy üres iOS-alkalmazást, amely leküldéses értesítéseket fogad az [Apple Push Notification szolgáltatás (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) használatával.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A tanúsítvány-aláírási kérelem fájljának létrehozása
> * Az alkalmazás regisztrálása leküldéses értesítésekhez
> * Üzembe helyezési profil létrehozása az alkalmazáshoz
> * Az értesítési központ konfigurálása iOS leküldéses értesítésekhez
> * Az iOS-alkalmazás összekapcsolása a Notification Hubs szolgáltatással
> * Teszt leküldéses értesítések küldése
> * Annak ellenőrzése, hogy az alkalmazás fogad-e értesítéseket

Az oktatóanyag teljes kódja [a githubon](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples)érhető el.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free).
* [Microsoft Azure üzenetkezelési keretrendszer]
* Az [Xcode] legújabb verziója
* IOS 10-es verzió (vagy újabb)-kompatibilis eszköz
* Tagság az [Apple fejlesztői programjában](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > A leküldéses értesítések konfigurációs követelményei miatt a leküldéses értesítéseket az iOS-szimulátor helyett egy fizikai iOS-eszközön (iPhone-on vagy iPaden) kell üzembe helyeznie és tesztelnie.
  
Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, iOS-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>iOS-alkalmazás összekapcsolása a Notification Hubs szogáltatással

1. Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Single View Application** (Egynézetes alkalmazás) sablont.

    ![Xcode – Egynézetes alkalmazás][8]

2. Amikor megadja az új projekt beállításait, győződjön meg arról, hogy a **Terméknév** és a **Szervezetazonosító** ugyanaz, mint amit az Apple fejlesztői portálján a csomagazonosító beállításakor használt.

    ![Xcode – projektbeállítások][11]

3. A Projektkezelőben kattintson a projekt nevére, kattintson az **Általános** lapra, majd keresse meg az **Aláírás** elemet. Győződjön meg arról, hogy a megfelelő csoportot választja ki az Apple Developer-fiókjához. Az Xcode a csomagazonosító alapján automatikusan lekéri a korábban létrehozott kiépítési profilt.

    Ha nem jelenik meg az Xcode-ban létrehozott új létesítési profil, frissítse az aláíró identitása profiljait. A menüsoron kattintson az **Xcode** elemre, majd a **Preferences** (Beállítások) lehetőségre, az **Account** (Fiók) lapra és a **View Details** (Részletek megtekintése) gombra. Ezután kattintson az aláírási identitására, majd kattintson a frissítési gombra a jobb alsó sarokban.

    ![Xcode – Létesítési profil][9]

4. Válassza a **Képességek** lapot, és engedélyezze a leküldéses értesítéseket.

    ![Xcode – leküldési képességek][12]

5. Adja hozzá az Azure Notification Hubs SDK-modulokat.

   Az Azure Notification Hubs SDK-t integrálhatja az alkalmazásba a [Cocoapods](https://cocoapods.org) használatával, vagy manuálisan is hozzáadhatja a bináris fájlokat a projekthez.

   - Integráció a Cocoapods-on keresztül

     Adja hozzá az alábbi függőségeket a `podfile`hoz, hogy az Azure Notification Hubs SDK-t az alkalmazásba foglalja.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Futtassa `pod install` az újonnan definiált Pod telepítéséhez, és nyissa meg a `.xcworkspace`.

     > [!NOTE]
     > Ha a következő hibaüzenetet látja: **[!] Nem található a AzureNotificationHubs-iOS-hez tartozó specifikáció** , `pod install`futtatása közben, futtassa `pod repo update`, hogy a Cocoapods-tárházból beolvassa a legújabb hüvelyeket, majd futtassa `pod install`.

   - Integráció a Carthage használatával

     Adja hozzá az alábbi függőségeket a `Cartfile`hoz, hogy az Azure Notification Hubs SDK-t az alkalmazásba foglalja.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Következő, frissítési és létrehozási függőségek:

     ```shell
     $ carthage update
     ```

     A Carthage használatával kapcsolatos további információkért tekintse meg a [Carthage GitHub-tárházat](https://github.com/Carthage/Carthage).

   - Integráció a bináris fájlok a projektbe való másolásával

     1. Töltse le a zip-fájlként megadott [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) -keretrendszert, és csomagolja ki.

     2. Az Xcode-ban kattintson a jobb gombbal a projektjére, majd kattintson az **Add Files to** (Fájlok hozzáadása a következőhöz:) lehetőségre a **WindowsAzureMessaging.framework** mappa az Xcode-projektjéhez adásához. Válassza a **Beállítások** lehetőséget, és győződjön meg arról, hogy az **Elemek másolása, ha szükséges** elem be van jelölve, majd kattintson a **Hozzáadás** elemre.

        ![Az Azure SDK kicsomagolása][10]

6. Adjon hozzá egy `HubInfo.h` nevű új fejlécfájlt a projektjéhez. Ez a fájl tárolja az állandókat az értesítési központhoz. Adja hozzá a következő definíciókat, és cserélje le a szövegkonstans helyőrzőit a *központ nevére* és a korábban feljegyzett *DefaultListenSharedAccessSignature* változóra.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

    #define HUBNAME @"<Enter the name of your hub>"
    #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Nyissa meg az `AppDelegate.h` fájlt, és adja hozzá a következő importálási irányelveket:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```

8. A `AppDelegate.m` fájlban adja hozzá a következő kódot a `didFinishLaunchingWithOptions` metódushoz az iOS-verzió alapján. Ez a kód regisztrálja az eszközleíróját az APNs szolgáltatással:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. Ugyanebben a fájlban adja hozzá a következő metódusokat:

    ```objc
    (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
     SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                 notificationHubPath:HUBNAME];

     [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
         if (error != nil) {
             NSLog(@"Error registering for notifications: %@", error);
         }
         else {
             [self MessageBox:@"Registration Status" message:@"Registered"];
         }
     }];
     }

    (void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Ez a kód a HubInfo.h-ban megadott kapcsolati információk használatával kapcsolódik az értesítési központhoz. Ezután megadja az eszköz jogkivonatát az értesítési központnak, így az képes értesítéseket küldeni.

10. Ugyanebben a fájlban adja hozzá a következő metódust egy **UIAlert** megjelenítéséhez, ha értesítés érkezik, amikor az alkalmazás aktív:

    ```objc
    (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
      NSLog(@"%@", userInfo);
      [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Hozza létre és futtassa az alkalmazást az eszközön az esetleges hibák kereséséhez.

## <a name="send-test-push-notifications"></a>Teszt leküldéses értesítések küldése

Az *Azure Portal* [Azure Portal] lehetőségével tesztelheti az alkalmazásban az értesítések fogadását. A parancs egy leküldéses tesztértesítést küld az eszközre.

![Azure Portal – Küldés tesztelése][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Annak ellenőrzése, hogy az alkalmazás fogad-e leküldéses értesítéseket

A leküldéses értesítések iOS rendszeren történő teszteléséhez üzembe kell helyezni az alkalmazást egy fizikai iOS-eszközön. Nem lehet Apple leküldéses értesítéseket küldeni az iOS-szimulátor használatával.

1. Futtassa az alkalmazást és ellenőrizze, hogy a regisztráció sikeres-e, majd nyomja meg az **OK** gombot.

    ![iOS-alkalmazás leküldésesértesítés-regisztrációs tesztje][33]

2. Ezután egy leküldéses tesztértesítést küld az [Azure Portal] az előző szakaszban leírtak szerint.

3. A leküldéses értesítést a rendszer az összes, az adott értesítési központból érkező értesítések fogadására regisztrált eszközre elküldi.

    ![iOS-alkalmazás leküldésesértesítés-fogadási tesztje][35]

## <a name="next-steps"></a>További lépések

Ebben az egyszerű példában leküldéses értesítéseket küldött az összes regisztrált iOS-eszközre. Ha szeretné megtudni, hogy hogyan küldhet értesítéseket adott iOS-eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Microsoft Azure üzenetkezelési keretrendszer]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
