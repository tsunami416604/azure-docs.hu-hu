---
title: Hitelesítés hozzáadása az Azure Mobile Apps iOS rendszeren
description: Ismerje meg, hogyan hitelesítheti a felhasználókat az IOS-es alkalmazás identitás-szolgáltatóktól, többek között az aad-ben, Google, Facebook, Twitter és a Microsoft számos az Azure Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445908"
---
# <a name="add-authentication-to-your-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ebben az oktatóanyagban a hitelesítés hozzáadása az [iOS – gyorsútmutató] projekt támogatott identitásszolgáltatóval. Ez az oktatóanyag alapján a [iOS – gyorsútmutató] oktatóanyaggal, amely először el kell végeznie.

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service-ben
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Hitelesítésre van szükség, hogy az alkalmazás egy új URL-séma meghatározása.  Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejeződése után.  Ebben az oktatóanyagban azt használja az URL-séma _appname_ során.  Bármely URL-séma választja is használhatja.  A mobilalkalmazás egyedinek kell lennie.  Th kiszolgálói oldalon az átirányítás engedélyezése:

1. Az a [Azure Portal], válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüpont.

3. Kattintson a **Azure Active Directory** alatt a **hitelesítésszolgáltatók** szakaszban.

4. Állítsa be a **felügyeleti mód** való **speciális**.

5. Az a **engedélyezett külső átirányítási URL-címeket**, adja meg `appname://easyauth.callback`.  A _appname_ Ez a karakterlánc a következő az URL-séma esetében.  Normál URL-cím-specifikáció (használata betűket és csak számokból állhat, és betűvel kezdődhet) protokoll, érdemes követnie.  Meg kell jegyezze fel a karakterlánc, amely úgy dönt, mert szüksége lesz a mobilalkalmazás-kód az URL-séma több helyen való beállításához.

6. Kattintson az **OK** gombra.

7. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Az xcode-ban, nyomja le az ENTER **futtatása** az alkalmazás elindításához. Kivétel jelenik meg, mert az alkalmazást próbál meg hozzáférni a háttérrendszer nem hitelesített felhasználóként, de a *TodoItem* táblázat most már hitelesítést igényel.

## <a name="add-authentication"></a>Hitelesítés hozzáadása alkalmazáshoz
**Objective-C**:

1. A Mac számítógépén nyissa meg a *QSTodoListViewController.m* az xcode-ban, és adja hozzá a következő metódust:

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

    Változás *google* való *microsoftaccount*, *twitter*, *facebook*, vagy *windowsazureactivedirectory* használatakor nem Google az identitás-szolgáltatóként. Ha használja a Facebookot, meg kell [engedélyezési lista Facebook tartományok] [ 1] az alkalmazásban.

    Cserélje le a **urlScheme** és a egy egyedi nevet az alkalmazásnak.  A urlScheme legyen ugyanaz, mint a megadott URL-séma protokoll a **engedélyezett külső átirányítási URL-címeket** mezőt az Azure Portalon. A urlScheme a hitelesítés-visszahívás használják a hitelesítési kérelem befejeződése után az alkalmazásnak váltani.

2. Cserélje le `[self refresh]` a `viewDidLoad` a *QSTodoListViewController.m* a következő kóddal:

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

   Adja ezt a kódot közvetlenül a sor olvasása előtt `#pragma mark - Core Data stack`.  Cserélje le a _appname_ a urlScheme értékkel, amely az 1. lépésben használt.

5. Nyissa meg a `AppName-Info.plist` (az alkalmazás nevével cseréjét AppName) fájlt, és adja hozzá a következő kódot:

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

    Ez a kód belül kell helyezni a `<dict>` elemet.  Cserélje le a _appname_ karakterlánc (az a tömbön belüli **CFBundleURLSchemes**) az 1. lépésben kiválasztott névre.  Végezhet is ezeket a módosításokat a plist-fájlt a szerkesztő – a gombra kattintva a `AppName-Info.plist` fájlt az xcode-ban nyissa meg a plist-szerkesztőt.

    Cserélje le a `com.microsoft.azure.zumo` karakterlánc- **CFBundleURLName** az Apple-lel csomagazonosítója.

6. Nyomja meg *futtatása* indítsa el az alkalmazást, és ezután jelentkezzen be. Ha bejelentkezett, láthatja a teendőlistában és frissítéseket kell lennie.

**SWIFT**:

1. A Mac számítógépén nyissa meg a *ToDoTableViewController.swift* az xcode-ban, és adja hozzá a következő metódust:

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

    Változás *google* való *microsoftaccount*, *twitter*, *facebook*, vagy *windowsazureactivedirectory* használatakor nem Google az identitás-szolgáltatóként. Ha használja a Facebookot, meg kell [engedélyezési lista Facebook tartományok] [ 1] az alkalmazásban.

    Cserélje le a **urlScheme** és a egy egyedi nevet az alkalmazásnak.  A urlScheme legyen ugyanaz, mint a megadott URL-séma protokoll a **engedélyezett külső átirányítási URL-címeket** mezőt az Azure Portalon. A urlScheme a hitelesítés-visszahívás használják a hitelesítési kérelem befejeződése után az alkalmazásnak váltani.

2. Távolítsa el a sorokat `self.refreshControl?.beginRefreshing()` és `self.onRefresh(self.refreshControl)` végén `viewDidLoad()` a *ToDoTableViewController.swift*. Adja hozzá egy hívást `loginAndGetData()` a helyen:

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

    Cserélje le a _appname_ a urlScheme értékkel, amely az 1. lépésben használt.

4. Nyissa meg a `AppName-Info.plist` (az alkalmazás nevével cseréjét AppName) fájlt, és adja hozzá a következő kódot:

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

    Ez a kód belül kell helyezni a `<dict>` elemet.  Cserélje le a _appname_ karakterlánc (az a tömbön belüli **CFBundleURLSchemes**) az 1. lépésben kiválasztott névre.  Végezhet is ezeket a módosításokat a plist-fájlt a szerkesztő – a gombra kattintva a `AppName-Info.plist` fájlt az xcode-ban nyissa meg a plist-szerkesztőt.

    Cserélje le a `com.microsoft.azure.zumo` karakterlánc- **CFBundleURLName** az Apple-lel csomagazonosítója.

5. Nyomja meg *futtatása* indítsa el az alkalmazást, és ezután jelentkezzen be. Ha bejelentkezett, láthatja a teendőlistában és frissítéseket kell lennie.

App Service-hitelesítés alma Inter-alkalmazás kommunikációt használ.  A témáról további részletekért tekintse meg a [Apple dokumentációja][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[iOS – gyorsútmutató]: app-service-mobile-ios-get-started.md

