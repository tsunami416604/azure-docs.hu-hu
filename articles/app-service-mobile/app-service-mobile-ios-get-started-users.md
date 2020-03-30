---
title: Hitelesítés hozzáadása iOS rendszeren
description: Ismerje meg, hogyan hitelesítheti az iOS-alkalmazás felhasználóit az iOS-alkalmazás felhasználóinak hitelesítésére olyan identitásszolgáltatókon, mint az AAD, a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fd7860053e8c04ca9d5e355a721afd834835a441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459023"
---
# <a name="add-authentication-to-your-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ebben az oktatóanyagban hitelesítést adhat hozzá az [iOS gyorsindítási] projekthez egy támogatott identitásszolgáltató használatával. Ez az oktatóanyag az [iOS gyorsindítási] oktatóanyagán alapul, amelyet először be kell fejeznie.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Az alkalmazás regisztrálása hitelesítésre és az App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz.  Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba.  Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk.  Azonban bármilyen URL-sémát használhat.  Meg kell egyedi a mobil alkalmazás.  Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon]válassza ki az App Service.In the Azure Portal , select your App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Kattintson az **Azure Active Directory** a **hitelesítési szolgáltatók** szakaszban.

4. Állítsa a Felügyeleti módot **Speciális** **módra.**

5. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `appname://easyauth.callback`  A karakterláncban szereplő _alkalmazásnév_ a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

6. Kattintson az **OK** gombra.

7. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Az Xcode-ban nyomja le a **Futtatás** gombot az alkalmazás elindításához. Kivétel takar, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a háttérrendszerhez, de a *TodoItem* tábla hitelesítést igényel.

## <a name="add-authentication-to-app"></a><a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
**C célkitűzés**:

1. Mac en nyissa meg a *QSTodoListViewController.m-et* Xcode-ban, és adja hozzá a következő módszert:

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

    Módosítsa a *Google-t* *microsoftaccountra*, *twitterre*, *facebookra*vagy *windowsazureactivedirectoryra,* ha nem a Google-t használja identitásszolgáltatóként. Ha a Facebookot használod, az alkalmazásodban fel kell [sorolnod a Facebook-domaineket.][1]

    Cserélje le az **urlScheme-et** az alkalmazás egyedi nevére.  Az urlScheme-nek meg kell egyeznie az Azure Portal **on Engedélyezett külső átirányítási URL-címek** mezőben megadott URL-séma protokollal. Az urlScheme a hitelesítési visszahívás segítségével váltson vissza az alkalmazásra a hitelesítési kérelem befejezése után.

2. Cserélje `[self refresh]` `viewDidLoad` le a *QSTodoListViewController.m-ben* a következő kódra:

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

   Adja hozzá ezt a `#pragma mark - Core Data stack`kódot közvetlenül a sor olvasása előtt .  Cserélje le az _alkalmazásnevet_ az 1.

5. Nyissa `AppName-Info.plist` meg a fájlt (cserélje le az AppName-t az alkalmazás nevére), és adja hozzá a következő kódot:

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

    Ezt a kódot az `<dict>` elembelsejébe kell helyezni.  Cserélje le az _alkalmazásnév_ karakterláncot (a **CFBundleURLSchemes**tömbön belül) az 1.  Azt is, hogy ezeket a változtatásokat `AppName-Info.plist` a plist szerkesztő - kattintson a fájlra xcode megnyitni a plist szerkesztő.

    Cserélje `com.microsoft.azure.zumo` le a **CFBundleURLName** karakterláncát az Apple-csomag azonosítójára.

6. Az alkalmazás elindításához nyomja le a *Futtatás* gombot, majd jelentkezzen be. Amikor be van jelentkezve, meg kell tudnia tekinteni a Teendőlistát, és frissítéseket kell készítenie.

**Gyors**:

1. Mac gépén nyissa meg a *ToDoTableViewController.swift-et* xcode-ban, és adja hozzá a következő módszert:

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

    Módosítsa a *Google-t* *microsoftaccountra*, *twitterre*, *facebookra*vagy *windowsazureactivedirectoryra,* ha nem a Google-t használja identitásszolgáltatóként. Ha a Facebookot használod, az alkalmazásodban fel kell [sorolnod a Facebook-domaineket.][1]

    Cserélje le az **urlScheme-et** az alkalmazás egyedi nevére.  Az urlScheme-nek meg kell egyeznie az Azure Portal **on Engedélyezett külső átirányítási URL-címek** mezőben megadott URL-séma protokollal. Az urlScheme a hitelesítési visszahívás segítségével váltson vissza az alkalmazásra a hitelesítési kérelem befejezése után.

2. Távolítsa el `self.refreshControl?.beginRefreshing()` `self.onRefresh(self.refreshControl)` a sorokat, és a `viewDidLoad()` végén a *ToDoTableViewController.swift*. Hívás hozzáadása `loginAndGetData()` a helyükön:

    ```swift
    loginAndGetData()
    ```

3. Nyissa `AppDelegate.swift` meg a fájlt, és `AppDelegate` adja hozzá a következő sort az osztályhoz:

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

    Cserélje le az _alkalmazásnevet_ az 1.

4. Nyissa `AppName-Info.plist` meg a fájlt (cserélje le az AppName-t az alkalmazás nevére), és adja hozzá a következő kódot:

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

    Ezt a kódot az `<dict>` elembelsejébe kell helyezni.  Cserélje le az _alkalmazásnév_ karakterláncot (a **CFBundleURLSchemes**tömbön belül) az 1.  Azt is, hogy ezeket a változtatásokat `AppName-Info.plist` a plist szerkesztő - kattintson a fájlra xcode megnyitni a plist szerkesztő.

    Cserélje `com.microsoft.azure.zumo` le a **CFBundleURLName** karakterláncát az Apple-csomag azonosítójára.

5. Az alkalmazás elindításához nyomja le a *Futtatás* gombot, majd jelentkezzen be. Amikor be van jelentkezve, meg kell tudnia tekinteni a Teendőlistát, és frissítéseket kell készítenie.

Az App Service-hitelesítés az Apples alkalmazásközi kommunikációját használja.  A témával kapcsolatos további részletekért tekintse meg az [Apple dokumentációját][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-portál]: https://portal.azure.com

[iOS gyorsindítás]: app-service-mobile-ios-get-started.md

