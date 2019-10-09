---
title: Hitelesítés hozzáadása az iOS-ben az Azure Mobile Apps
description: Ismerje meg, hogyan hitelesítheti az iOS-alkalmazások felhasználóit az Azure Mobile Apps használatával különböző identitás-szolgáltatók, például a HRE, a Google, a Facebook, a Twitter és a Microsoft segítségével.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 674d5f783f43011ba154b668cea4ec41f6a945f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025283"
---
# <a name="add-authentication-to-your-ios-app"></a>Hitelesítés hozzáadása iOS-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Ebben az oktatóanyagban egy támogatott identitás-szolgáltató használatával adja hozzá a hitelesítést az [iOS – első lépések] projekthez. Ez az oktatóanyag az [iOS – első lépések] oktatóanyagán alapul, amelyet először végre kell hajtania.

## <a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz.  Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után.  Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben.  Használhat azonban bármely kiválasztott URL-címet.  Egyedinek kell lennie a Mobile-alkalmazás számára.  Az átirányítás engedélyezése a következő kiszolgálóoldali oldalon:

1. A [Azure Portal]válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Kattintson **Azure Active Directory** a **hitelesítési szolgáltatók** szakaszban.

4. Állítsa a **felügyeleti módot** a **speciális**értékre.

5. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `appname://easyauth.callback` értéket.  A karakterláncban szereplő _AppName_ a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

6. Kattintson az **OK** gombra.

7. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A Xcode-ben kattintson a **Futtatás** gombra az alkalmazás elindításához. Kivétel keletkezik, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a háttérhez, de a *TodoItem* -táblához már hitelesítés szükséges.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
**Objective-C**:

1. A Mac számítógépen nyissa meg a *QSTodoListViewController. m* Xcode, és adja hozzá a következő metódust:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Változtassa meg a *Google* -t *MicrosoftAccount*, *Twitter*-, *Facebook*-vagy *windowsazureactivedirectory* , ha nem a Google-t használja az identitás-szolgáltatóként. Ha a Facebookot használja, az alkalmazásban engedélyeznie kell a [Facebook-tartományokat][1] .

    Cserélje le a **urlScheme** az alkalmazás egyedi nevére.  A urlScheme meg kell egyeznie a Azure Portal **engedélyezett külső átirányítási URL-címek** mezőjében megadott URL-séma protokollal. A hitelesítési visszahívás a urlScheme használja, hogy visszaváltson az alkalmazásra a hitelesítési kérelem befejeződése után.

2. Cserélje le a `[self refresh]` értéket a *QSTodoListViewController. m* `viewDidLoad` értékre a következő kóddal:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Nyissa meg a `QSAppDelegate.h` fájlt, és adja hozzá a következő kódot:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Nyissa meg a `QSAppDelegate.m` fájlt, és adja hozzá a következő kódot:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Adja hozzá ezt a kódot közvetlenül a `#pragma mark - Core Data stack` olvasását megelőzően.  Cserélje le a _AppName_ az 1. lépésben használt urlScheme értékre.

5. Nyissa meg a `AppName-Info.plist` fájlt (cserélje le az AppName az alkalmazás nevére), és adja hozzá a következő kódot:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ezt a kódot a `<dict>` elemen belül kell elhelyezni.  Cserélje le a _AppName_ karakterláncot (az **CFBundleURLSchemes**tömbön belül) az 1. lépésben kiválasztott alkalmazás nevével.  Ezeket a módosításokat a plist-szerkesztőben is elvégezheti – kattintson a XCode található `AppName-Info.plist` fájlra a plist-szerkesztő megnyitásához.

    Cserélje le a `com.microsoft.azure.zumo` karakterláncot a **CFBundleURLName** az Apple Bundle azonosítójával.

6. Kattintson a *Futtatás* gombra az alkalmazás elindításához, majd jelentkezzen be. Amikor bejelentkezett, meg kell tudnia tekinteni a teendők listáját, és frissítéseket kell készítenie.

**Swift**:

1. A Mac számítógépen nyissa meg a *ToDoTableViewController. Swift* Xcode, és adja hozzá a következő metódust:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Változtassa meg a *Google* -t *MicrosoftAccount*, *Twitter*-, *Facebook*-vagy *windowsazureactivedirectory* , ha nem a Google-t használja az identitás-szolgáltatóként. Ha a Facebookot használja, az alkalmazásban engedélyeznie kell a [Facebook-tartományokat][1] .

    Cserélje le a **urlScheme** az alkalmazás egyedi nevére.  A urlScheme meg kell egyeznie a Azure Portal **engedélyezett külső átirányítási URL-címek** mezőjében megadott URL-séma protokollal. A hitelesítési visszahívás a urlScheme használja, hogy visszaváltson az alkalmazásra a hitelesítési kérelem befejeződése után.

2. Távolítsa el a `self.refreshControl?.beginRefreshing()` és a `self.onRefresh(self.refreshControl)` sorokat a *ToDoTableViewController. swift*`viewDidLoad()` végén. Vegyen fel egy hívást a `loginAndGetData()` helyére:

    ```swift
    loginAndGetData()
    ```

3. Nyissa meg a `AppDelegate.swift` fájlt, és adja hozzá a következő sort a `AppDelegate` osztályhoz:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Cserélje le a _AppName_ az 1. lépésben használt urlScheme értékre.

4. Nyissa meg a `AppName-Info.plist` fájlt (cserélje le az AppName az alkalmazás nevére), és adja hozzá a következő kódot:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ezt a kódot a `<dict>` elemen belül kell elhelyezni.  Cserélje le a _AppName_ karakterláncot (az **CFBundleURLSchemes**tömbön belül) az 1. lépésben kiválasztott alkalmazás nevével.  Ezeket a módosításokat a plist-szerkesztőben is elvégezheti – kattintson a XCode található `AppName-Info.plist` fájlra a plist-szerkesztő megnyitásához.

    Cserélje le a `com.microsoft.azure.zumo` karakterláncot a **CFBundleURLName** az Apple Bundle azonosítójával.

5. Kattintson a *Futtatás* gombra az alkalmazás elindításához, majd jelentkezzen be. Amikor bejelentkezett, meg kell tudnia tekinteni a teendők listáját, és frissítéseket kell készítenie.

App Service a hitelesítés az Apple-alkalmazások közötti kommunikációt használ.  A témával kapcsolatos további részletekért tekintse meg az [Apple dokumentációját][2] .
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[iOS – első lépések]: app-service-mobile-ios-get-started.md

