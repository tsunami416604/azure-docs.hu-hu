---
title: Hitelesítés hozzáadása iOS rendszeren
description: Ismerje meg, hogyan hitelesítheti az iOS-alkalmazás felhasználóit az Azure Mobile Apps használatával, például a HRE, a Google, a Facebook, a Twitter és a Microsoft segítségével.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 925894cab00537cb9aeb03ca05c9699bb4bf6a84
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668458"
---
# <a name="add-authentication-to-your-ios-app"></a>Hitelesítés hozzáadása iOS-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Ebben az oktatóanyagban egy támogatott identitás-szolgáltató használatával adja hozzá a hitelesítést az [iOS – első lépések] projekthez. Ez az oktatóanyag az [iOS – első lépések] oktatóanyagán alapul, amelyet először végre kell hajtania.

## <a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz.  Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után.  Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben.  Használhat azonban bármely kiválasztott URL-címet.  Egyedinek kell lennie a Mobile-alkalmazás számára.  Az átirányítás engedélyezése a következő kiszolgálóoldali oldalon:

1. A [Azure Portalra]válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Kattintson **Azure Active Directory** a **hitelesítési szolgáltatók** szakaszban.

4. Állítsa a **felügyeleti módot** a **speciális**értékre.

5. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `appname://easyauth.callback`.  A karakterláncban szereplő _AppName_ a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

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

2. Cserélje le a *QSTodoListViewController. m* `viewDidLoad` `[self refresh]` a következő kódra:

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

   Adja hozzá ezt a kódot közvetlenül a sor olvasása előtt `#pragma mark - Core Data stack`.  Cserélje le a _AppName_ az 1. lépésben használt urlScheme értékre.

5. Nyissa meg a `AppName-Info.plist` fájlt (a AppName helyére írja be az alkalmazás nevét), és adja hozzá a következő kódot:

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

    Ezt a kódot a `<dict>` elemen belül kell elhelyezni.  Cserélje le a _AppName_ karakterláncot (az **CFBundleURLSchemes**tömbön belül) az 1. lépésben kiválasztott alkalmazás nevével.  Ezeket a módosításokat a plist-szerkesztőben is elvégezheti – a XCode `AppName-Info.plist` fájlra kattintva megnyithatja a plist szerkesztőt.

    Cserélje le a **CFBundleURLName** `com.microsoft.azure.zumo` karakterláncát az Apple Bundle-azonosítójával.

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

2. Távolítsa el az *ToDoTableViewController. swift*`viewDidLoad()` végén `self.refreshControl?.beginRefreshing()` és `self.onRefresh(self.refreshControl)` sorokat. Vegyen fel egy hívást a `loginAndGetData()` helyére:

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

4. Nyissa meg a `AppName-Info.plist` fájlt (a AppName helyére írja be az alkalmazás nevét), és adja hozzá a következő kódot:

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

    Ezt a kódot a `<dict>` elemen belül kell elhelyezni.  Cserélje le a _AppName_ karakterláncot (az **CFBundleURLSchemes**tömbön belül) az 1. lépésben kiválasztott alkalmazás nevével.  Ezeket a módosításokat a plist-szerkesztőben is elvégezheti – a XCode `AppName-Info.plist` fájlra kattintva megnyithatja a plist szerkesztőt.

    Cserélje le a **CFBundleURLName** `com.microsoft.azure.zumo` karakterláncát az Apple Bundle-azonosítójával.

5. Kattintson a *Futtatás* gombra az alkalmazás elindításához, majd jelentkezzen be. Amikor bejelentkezett, meg kell tudnia tekinteni a teendők listáját, és frissítéseket kell készítenie.

App Service a hitelesítés az Apple-alkalmazások közötti kommunikációt használ.  A témával kapcsolatos további részletekért tekintse meg az [Apple dokumentációját][2] .
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portalra]: https://portal.azure.com

[iOS – első lépések]: app-service-mobile-ios-get-started.md

