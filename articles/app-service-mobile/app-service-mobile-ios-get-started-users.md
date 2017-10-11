---
title: "Hitelesítés hozzáadása az Azure Mobile Apps IOS"
description: "Útmutató az Azure Mobile Apps segítségével hitelesíti a felhasználókat identitás-szolgáltatóktól, beleértve az aad-ben, a Google, a Facebook, a Twitter és a Microsoft számos az iOS-alkalmazás."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: glenga
ms.openlocfilehash: 21a2cc6c1eaf4b34cbe8c2d7c4dbb69c8730cf32
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ebben az oktatóanyagban a hitelesítés hozzáadása a [iOS quick start] projekt támogatott identitásszolgáltató használatával. Ez az oktatóanyag alapul a [iOS quick start] oktatóanyag, amely először el kell végeznie.

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és az App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Biztonságos hitelesítéshez az szükséges, hogy az alkalmazás adja meg egy új URL-sémát.  Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejezése után.  Ebben az oktatóanyagban az URL-séma használjuk _appname_ egész.  Bármely választja URL-sémát is használhatja.  A mobilalkalmazás egyedinek kell lennie.  Az átirányítás th kiszolgálóoldalon engedélyezése:

1. Az a [Azure-portálon], válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüjét.

3. Kattintson a **Azure Active Directory** alatt a **hitelesítésszolgáltatókat** szakasz.

4. Állítsa be a **üzemmód** való **speciális**.

5. Az a **engedélyezett külső átirányítási URL-t**, adja meg `appname://easyauth.callback`.  A _appname_ ezt a karakterláncot a rendszer az URL-séma, a mobilalkalmazás.  Akkor érdemes követnie normál URL-cím meghatározása (használata betűket és számokat csak, és betűvel kezdődik) protokoll.  Meg kell győződnie, jegyezze fel a karakterláncot, amely úgy dönt, mivel úgy, hogy az URL-séma több helyen a mobilalkalmazás kódot kell.

6. Kattintson az **OK** gombra.

7. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Az xcode-ban, nyomja le az **futtatása** az alkalmazás indításához. Egy kivétel következik be, mert az alkalmazás megpróbál hozzáférni egy nem hitelesített felhasználóként, háttér, de a *TodoItem* tábla most hitelesítést igényel.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
**Objective-C**:

1. Nyissa meg a Mac *QSTodoListViewController.m* az xcode-ban, és adja hozzá a következő metódust:

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

    Változás *google* való *microsoftaccount*, *twitter*, *facebook*, vagy *windowsazureactivedirectory* használatakor nem Google az identitás-szolgáltatóként. Facebook használatakor meg kell [engedélyezett Facebook tartományok] [ 1] az alkalmazásban.

    Cserélje le a **urlScheme** rendelkező egy egyedi nevet az alkalmazásnak.  A urlScheme meg kell egyeznie a megadott URL-séma protokollként a **engedélyezett külső átirányítási URL-t** mező mellett az Azure-portálon. A urlScheme váltson az alkalmazás, a hitelesítési kérelem befejeződése után a hitelesítés-visszahívás használják.

2. Cserélje le `[self refresh]` a `viewDidLoad` a *QSTodoListViewController.m* az alábbi kódra:

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

   Adja hozzá a kódot közvetlenül a sor olvasása előtt `#pragma mark - Core Data stack`.  Cserélje le a _appname_ egyidejűleg az 1. lépésben használt urlScheme értékét.

5. Nyissa meg a `AppName-Info.plist` (tagjára AppName nevű, az alkalmazás) fájlt, és adja hozzá a következő kódot:

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

    Ezt a kódot kell elhelyezni a `<dict>` elemet.  Cserélje le a _appname_ karakterlánc (a tömbön belüli **CFBundleURLSchemes**) az 1. lépésben kiválasztott alkalmazás névvel.  Is tehet ezeket a módosításokat a plist-szerkesztő - kattintson arra a `AppName-Info.plist` fájlt az xcode-ban nyissa meg a plist-szerkesztőt.

    Cserélje le a `com.microsoft.azure.zumo` karakterlánc- **CFBundleURLName** az Apple csomagot azonosítója.

6. Nyomja le az *futtatása* , és indítsa el az alkalmazást, majd jelentkezzen be. Jelentkezett be, amikor a teendőlista és módosításokat kell lennie.

**SWIFT**:

1. Nyissa meg a Mac *ToDoTableViewController.swift* az xcode-ban, és adja hozzá a következő metódust:

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

    Változás *google* való *microsoftaccount*, *twitter*, *facebook*, vagy *windowsazureactivedirectory* használatakor nem Google az identitás-szolgáltatóként. Facebook használatakor meg kell [engedélyezett Facebook tartományok] [ 1] az alkalmazásban.

    Cserélje le a **urlScheme** rendelkező egy egyedi nevet az alkalmazásnak.  A urlScheme meg kell egyeznie a megadott URL-séma protokollként a **engedélyezett külső átirányítási URL-t** mező mellett az Azure-portálon. A urlScheme váltson az alkalmazás, a hitelesítési kérelem befejeződése után a hitelesítés-visszahívás használják.

2. A sorok eltávolításához `self.refreshControl?.beginRefreshing()` és `self.onRefresh(self.refreshControl)` végén `viewDidLoad()` a *ToDoTableViewController.swift*. Adjon hozzá egy `loginAndGetData()` azok:

    ```swift
    loginAndGetData()
    ```

3. Nyissa meg a `AppDelegate.swift` fájlt, és adja hozzá a következő sort a `AppDelegate` osztály:

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

    Cserélje le a _appname_ egyidejűleg az 1. lépésben használt urlScheme értékét.

4. Nyissa meg a `AppName-Info.plist` (tagjára AppName nevű, az alkalmazás) fájlt, és adja hozzá a következő kódot:

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

    Ezt a kódot kell elhelyezni a `<dict>` elemet.  Cserélje le a _appname_ karakterlánc (a tömbön belüli **CFBundleURLSchemes**) az 1. lépésben kiválasztott alkalmazás névvel.  Is tehet ezeket a módosításokat a plist-szerkesztő - kattintson arra a `AppName-Info.plist` fájlt az xcode-ban nyissa meg a plist-szerkesztőt.

    Cserélje le a `com.microsoft.azure.zumo` karakterlánc- **CFBundleURLName** az Apple csomagot azonosítója.

5. Nyomja le az *futtatása* , és indítsa el az alkalmazást, majd jelentkezzen be. Jelentkezett be, amikor a teendőlista és módosításokat kell lennie.

App Service hitelesítés almák Inter alkalmazáson belüli kommunikációhoz használ.  A témáról további részletekért tekintse meg a [Apple dokumentáció][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portálon]: https://portal.azure.com

[iOS quick start]: app-service-mobile-ios-get-started.md

