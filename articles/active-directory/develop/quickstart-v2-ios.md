---
title: A Microsoft identity platform IOS-es rövid útmutató |} Az Azure
description: Ismerje meg, hogyan bejelentkezési felhasználók és a lekérdezés a Microsoft Graph egy iOS-alkalmazásban.
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
ms.date: 04/18/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32cc373421e6b04737f40dc987b10c6ace858e17
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937334"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Gyors útmutató: A felhasználók és a Microsoft Graph API meghívása iOS-alkalmazások

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban található kódmintán azt mutatjuk be, hogyan tud egy natív iOS-alkalmazás bejelentkezni személyes, munkahelyi vagy iskolai fiókokba, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * XCode 10 +
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://aka.ms/MobileAppReg) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - Az a **neve** területén adjon meg egy kifejező alkalmazásnevet fog megjelenni a felhasználók számára az alkalmazás, ha beleegyezik abba, hogy az alkalmazás, például vagy jelentkezzen be a `iOSQuickstart`.
>      - Egyéb konfigurációk kihagyja ezen az oldalon. 
>      - Nyomja le az `Register` gombra.
> 1. Kattintson az új alkalmazás a > lépjen a `Authentication`  >  `Add Platform`  >  `iOS`.    
>      - Adja meg a ***Bundle Identifier*** az alkalmazáshoz. 
> 1. Válassza ki `Configure` , és mentse a ***MSAL konfigurációs*** részletei később. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> Az ebben a rövid működéséhez kódmintájához hozzá kell átirányítási URI-t a hitelesítési ügynök kompatibilis. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-ios/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal

#### <a name="step-2-download-your-web-server-or-project"></a>2. lépés: Töltse le a webes vagy projekt

- [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3. lépés: A projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti beállítás 1, akkor kihagyhatja ezeket a lépéseket. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Szerkessze a **ViewController.swift** fájlt, és cserélje le a „let kClientID” kezdetű sort a következő kódrészletre:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Kattintson a jobb gombbal **Info.plist** válassza **Megnyitás másként** > **forráskód**.
> 1. Dict gyökércsomópontja alatt cserélje le a ***Alkalmazásköteg-azonosító***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Hozhat létre, és futtassa az alkalmazást! 

> [!div renderon="docs"]
>
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Szerkesztés **ViewController.swift** , és cserélje le a "let kClientID" a következő kódrészletet a kezdetű sort:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Kattintson a jobb gombbal **Info.plist** válassza **Megnyitás másként** > **forráskód**.
> 1. Dict gyökércsomópontja alatt cserélje le a ***Alkalmazásköteg-azonosító***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Hozhat létre, és futtassa az alkalmazást! 

## <a name="more-information"></a>További információ

Az erre a rövid útmutatóra vonatkozó további információért tekintse meg ezeket a szakaszokat.

### <a name="getting-msal"></a>Az MSAL beolvasása

Az MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) segítségével a felhasználók, és egy API-t a Microsoft identity platform által védett eléréséhez használt jogkivonatokat kérhetnek a könyvtár. Az MSAL-t az alábbi eljárással adhatja hozzá az alkalmazáshoz:

```
$ vi Podfile

```
Adja hozzá a következőt a pod-fájlhoz (a cél a projekt):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```swift
import MSAL
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `clientId` | A *portal.azure.com* webhelyen regisztrált alkalmazásból származó alkalmazásazonosító |
> | `authority` | A Microsoft identity platform végpontot. Ez a legtöbb esetben *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Az átirányítási URI-ját az alkalmazás. "Üres" az alapértelmezett érték, vagy az egyéni átirányítási URI-t adhat át. |

### <a name="additional-app-requirements"></a>További alkalmazásokra vonatkozó követelmények  

Az alkalmazás is rendelkeznie kell a következőt a `AppDelegate`. Ez lehetővé teszi az MSAL SDK-t a hitelesítési közvetítő alkalmazás token válaszát kezelésére, amikor hitelesítést hajt végre.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Végezetül az alkalmazás rendelkezik kell egy `LSApplicationQueriesSchemes` bejegyzést a ***Info.plist*** mellett a `CFBundleURLTypes`. Ez tartalmazza a minta tartalmaz. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>A felhasználók & jogkivonatok kérése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilent`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Interaktív módon jogkivonatának beolvasása

Bizonyos helyzetekben megköveteli a felhasználóktól, együttműködhet a Microsoft identitásplatformjához. Ezekben az esetekben a végfelhasználók kiválaszthatják a fiókjukat, adja meg a hitelesítő adataik és hozzájárul az Alkalmazásengedélyek szükséges lehet. Például: 

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, kell adnia a hitelesítő adatait 
* Ha az alkalmazás erőforrásokhoz való hozzáférést az első alkalommal
* Ha az MFA vagy más feltételes hozzáférési házirendek szükségesek

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azt jelenti, `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Hozzáférési jogkivonat csendes lekérése

Alkalmazások a felhasználók jelentkezhetnek be minden alkalommal, amikor a jogkivonat kérnek elvégzéséhez nem szükséges. Ha a felhasználó már bejelentkezett, ez a módszer engedélyezi, hogy csendes kérelem jogkivonatokat. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azt jelenti, `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (`api://<Application ID>/access_as_user`) |
> | `account` | A fiók egy jogkivonatot a kérnek. Ebben a rövid útmutatóban egy alkalmazás, amely egyetlen fiók, ha kell, melyik fiókot szeretné használni a jogkivonat-kérelmeket azonosítása logika definiálható több fiók alkalmazás készítése `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>További lépések

Próbálja ki az iOS-oktatóanyag befejezése lépésenkénti útmutató az alkalmazások, beleértve a rövid útmutató egy teljes leírását.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

> [!div class="nextstepaction"]
> [A Graph API meghívása – iOS-oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]