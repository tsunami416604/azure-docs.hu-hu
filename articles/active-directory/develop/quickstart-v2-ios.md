---
title: A Microsoft identity platform IOS-es rövid útmutató |} Az Azure
description: Ismerje meg, hogyan jelentkeztethet be felhasználókat és kérdezheti le a Microsoft Graph-ot egy natív iOS-alkalmazásból.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6340e0f349d66ecf6baaca481722396a6d786c5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496129"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>Gyors útmutató: A felhasználók és a natív iOS-alkalmazások a Microsoft Graph API meghívása

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban található kódmintán azt mutatjuk be, hogyan tud egy natív iOS-alkalmazás bejelentkezni személyes, munkahelyi vagy iskolai fiókokba, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Regisztráció és letöltés
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Az alkalmazás és a kódminta regisztrálása és konfigurálása
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:
> 1. Az alkalmazás regisztrálásához lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).
> 1. Az **Alkalmazás neve** mezőben nevezze el az alkalmazását.
> 1. Ügyeljen arra, hogy az **Irányított beállítás** jelölőnégyzet ne legyen bejelölve, majd válassza a **Létrehozás** lehetőséget.
> 1. Válassza a **Platform hozzáadása**, a **Natív alkalmazás**, majd a **Mentés** lehetőséget.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> Az ebben a rövid működéséhez kódminta található, a válasz URL-cím hozzá kell `msal<AppId>://auth` (ahol az msal\<AppId > az alkalmazás azonosítója).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás végrehajtása nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-ios/green-check.png) az alkalmazás ezzel az attribútummal van konfigurálva.

#### <a name="step-2-download-your-web-server-or-project"></a>2. lépés: Töltse le a webes vagy projekt

- [Töltse le az XCode-projektben](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3. lépés: A projekt konfigurálása

1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
1. Szerkessze a **ViewController.swift** fájlt, és cserélje le a „let kClientID” kezdetű sort a következő kódrészletre:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. A Control billentyűt lenyomva kattintson az **Info.plist** elemre a helyi menü megjelenítéséhez, majd válassza az **Open As** > **Source Code** (Megnyitás forráskódként) lehetőséget.
1. A könyvtár gyökércsomópontja alatt adja meg a következő kódot:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span> Az `<ENTER_THE_APPLICATION_ID_HERE>` sztringet cserélje le az alkalmazás *azonosítójára*. Az *alkalmazásazonosítót* az *áttekintési* oldalon találja.

## <a name="more-information"></a>További információ

Az erre a rövid útmutatóra vonatkozó további információért tekintse meg ezeket a szakaszokat.

### <a name="msal"></a>MSAL

Az MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) egy olyan kódtár, amely felhasználók beléptetéséhez és egy Microsoft Azure Active Directory által védett API-hoz való hozzáféréshez használt jogkivonatok kéréséhez használható. Az MSAL-t az alábbi eljárással adhatja hozzá az alkalmazáshoz:

```
$ vi Podfile
```
Adja hozzá a következőt a pod-fájlhoz:

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```swift
import MSAL
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `clientId` | A *portal.azure.com* webhelyen regisztrált alkalmazásból származó alkalmazásazonosító |
> | `authority` | A Microsoft identity platform végpontot. Ez a legtöbb esetben *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilent`

#### <a name="getting-an-access-token-interactively"></a>Hozzáférési jogkivonat interaktív lekérése

Bizonyos helyzetekben szükséges együttműködhet a Microsoft identity platform végpont, amely egy helyi kapcsoló azt eredményezi, a rendszer böngészőben vagy a felhasználók a fiók hitelesítő adatainak érvényesítéséhez, vagy beleegyezést a felhasználók. Néhány példa:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
* Amikor az alkalmazás olyan erőforráshoz kér hozzáférést, amelyhez szükséges a felhasználó hozzájárulása
* Ha kétfaktoros hitelesítésre van szükség

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `forScopes` | Tartalmazza a kért hatóköröket (azaz `[ "user.read" ]` Microsoft Graph és `[ "<Application ID URL>/scope" ]` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |

#### <a name="getting-an-access-token-silently"></a>Hozzáférési jogkivonat csendes lekérése

Nem ajánlott minden egyes alkalommal megkövetelni a felhasználóktól a hitelesítő adatok érvényesítését, amikor hozzá kell férniük egy erőforráshoz. Általában szerencsésebb, ha a jogkivonatok beszerzéséhez és megújításához nincs szükség felhasználói beavatkozásra. Kezdetben használja az `acquireToken` metódust, majd a védett erőforrásokhoz való hozzáféréshez szükséges jogkivonatok beszerzéséhez az `acquireTokenSilent` metódust használhatja:

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `forScopes` | Tartalmazza a kért hatóköröket (azaz `[ "user.read" ]` Microsoft Graph és `[ "<Application ID URL>/scope" ]` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `account` | A jogkivonatot kérő fiók (az MSAL több fiókot támogat egyetlen alkalmazásban). E rövid útmutató esetében az érték az első fiókra mutat a gyorsítótárban (`applicationContext.allAccounts().first`). |

## <a name="next-steps"></a>További lépések

Próbálja ki az iOS-hez készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

> [!div class="nextstepaction"]
> [IOS-oktatóanyag Graph API meghívása](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
