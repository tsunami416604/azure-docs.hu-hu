---
title: IOS-és macOS-alapú Microsoft Identity platform | Azure
description: Megtudhatja, hogyan jelentkezhet be a felhasználók és a lekérdezési Microsoft Graph egy iOS-vagy macOS-alkalmazásba.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 25e37d27805adb47fae19bdcc0e94253f8c4ccf2
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703456"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Gyors útmutató: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása iOS-vagy macOS-alkalmazásból

Ez a rövid útmutató egy kódot tartalmaz, amely bemutatja, hogyan használhatja a natív iOS-vagy macOS-alkalmazások a Microsoft Identity platformot személyes, munkahelyi és iskolai fiókokhoz való bejelentkezéshez, hozzáférési token beszerzéséhez és a Microsoft Graph API meghívásához.

Ez a rövid útmutató az iOS-és macOS-alkalmazásokra is vonatkozik. Néhány lépésre csak az iOS-alkalmazások esetében van szükség. Ezek a lépések meghívja, hogy csak az iOS rendszerhez készültek.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * XCode 10 +
> * iOS 10 + 
> * macOS 10.12 +

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>1\. lehetőség: az alkalmazás regisztrálása és automatikus konfigurálása, majd a mintakód letöltése
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://aka.ms/MobileAppReg) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára, amikor bejelentkeznek vagy beleegyeznek az alkalmazásba.
>      - Egyéb konfigurációk kihagyása ezen a lapon.
>      - Válassza a(z) `Register` lehetőséget.
> 1. A **kezelés** szakaszban válassza a `Authentication` > `Add Platform` > `iOS`lehetőséget.
>      - Adja meg az alkalmazáshoz tartozó ***köteg azonosítóját*** . A köteg azonosítója csak egy egyedi karakterlánc, amely egyedileg azonosítja az alkalmazást, például `com.<yourname>.identitysample.MSALMacOS`. Jegyezze fel a használt értéket.
>      - Vegye figyelembe, hogy az iOS-konfiguráció a macOS-alkalmazásokra is érvényes.
> 1. Válassza a `Configure` lehetőséget, majd mentse a ***MSAL-konfiguráció*** részleteit a rövid útmutató későbbi részében.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1\. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy átirányítási URI-t, amely kompatibilis a hitelesítési közvetítővel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-ios/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal

#### <a name="step-2-download-the-sample-project"></a>2\. lépés: a minta projekt letöltése

- [Az iOS-hez készült kód mintájának letöltése](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Töltse le a macOS rendszerhez készült kód mintáját](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>3\. lépés: a függőségek telepítése

Egy terminál ablakban navigáljon a letöltött mintakód nevű mappához, és futtassa `pod install` a legújabb MSAL-könyvtár telepítéséhez.

#### <a name="step-4-configure-your-project"></a>4\. lépés: a projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti 1. lehetőséget választotta, akkor kihagyhatja ezeket a lépéseket. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Szerkessze a **ViewController. Swift** -et, és cserélje le a "Let kClientID" kezdetű sort a következő kódrészlettel. Ne felejtse el frissíteni a `kClientID` értékét azon ügyfél-AZONOSÍTÓval, amelyet a rövid útmutatóban korábban a portálon az alkalmazás regisztrálásakor mentett:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Nyissa meg a projekt beállításait. Az **identitás** szakaszban adja meg a portálon megadott **köteg-azonosítót** .
> 1. Csak iOS esetén kattintson a jobb gombbal az **info. plist** fájlra, és válassza **a Megnyitás** > **forráskódként**lehetőséget.
> 1. Csak iOS esetén a dict gyökérszintű csomópont alatt cserélje le a `CFBundleURLSchemes`t a portálon megadott ***csomag-azonosítóra*** .
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
>    ```
> 1. Build & az alkalmazás futtatása!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Szerkessze a **ViewController. Swift** -et, és cserélje le a "Let kClientID" kezdetű sort a következő kódrészlettel. Ne felejtse el frissíteni a `kClientID` értékét azon clientID, amelyet a rövid útmutatóban korábban a portálon az alkalmazás regisztrálásakor mentett:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Nyissa meg a projekt beállításait. Az **identitás** szakaszban adja meg a portálon megadott **köteg-azonosítót** .
> 1. Csak iOS esetén kattintson a jobb gombbal az **info. plist** fájlra, és válassza **a Megnyitás** > **forráskódként**lehetőséget.
> 1. Csak iOS esetén a dict gyökérszintű csomópont alatt cserélje le a `Enter_the_bundle_Id_Here`t a portálon használt ***csomag-azonosítóra*** .
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
> 1. Build & az alkalmazás futtatása! 

## <a name="more-information"></a>További információ

Az erre a rövid útmutatóra vonatkozó további információért tekintse meg ezeket a szakaszokat.

### <a name="get-msal"></a>MSAL beolvasása

A MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Az MSAL-t az alábbi eljárással adhatja hozzá az alkalmazáshoz:

```
$ vi Podfile

```
Adja hozzá a következőt ehhez a cocoapods (a projekt céljával):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

CocoaPods-telepítési parancs futtatása:

```pod install```

### <a name="initialize-msal"></a>MSAL inicializálása

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

> |Helyszín: ||
> |---------|---------|
> | `clientId` | A *portal.azure.com* webhelyen regisztrált alkalmazásból származó alkalmazásazonosító |
> | `authority` | A Microsoft Identity platform végpontja. Ez a legtöbb esetben *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Az alkalmazás átirányítási URI-ja. Az alapértelmezett értéket vagy az egyéni átirányítási URI-t a "Nil" értékre lehet átadni. |

### <a name="for-ios-only-additional-app-requirements"></a>Csak iOS esetén, további alkalmazásokra vonatkozó követelmények

Az alkalmazásnak a következőkre is szükség van a `AppDelegate`ban. Ez lehetővé teszi, hogy a MSAL SDK kezelje a jogkivonat választ az Auth Broker alkalmazástól a hitelesítés során.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Ha `UIApplicationDelegate`helyett a `UISceneDelegate` elfogadja az iOS 13 + verziót, helyezze el ezt a kódot a `scene:openURLContexts:` callback-be (lásd az [Apple dokumentációját](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, a MSAL visszahívást mindkét helyre kell helyezni.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Végül az alkalmazásnak rendelkeznie kell egy `LSApplicationQueriesSchemes` bejegyzéssel az ***info. plist*** fájlban a `CFBundleURLTypes`mellett. Ehhez a mintához tartozik. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Bejelentkezési felhasználók & kérelmek jogkivonatai

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilent`

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: token beszerzése interaktív módon

Bizonyos helyzetekben a felhasználóknak a Microsoft Identity platformmal való interakcióra van szükségük. Ezekben az esetekben előfordulhat, hogy a végfelhasználónak ki kell választania a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás engedélyeit. Például: 

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat 
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Helyszín:||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azaz `[ "user.read" ]` az egyéni webes API-khoz Microsoft Graph vagy `[ "<Application ID URL>/scope" ]`hoz (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: hozzáférési token lekérése csendesen

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük a felhasználóktól, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, ez a módszer lehetővé teszi, hogy az alkalmazások csendesen igényeljenek jogkivonatokat. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Helyszín: ||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azaz `[ "user.read" ]` az egyéni webes API-khoz Microsoft Graph vagy `[ "<Application ID URL>/scope" ]`hoz (`api://<Application ID>/access_as_user`) |
> | `account` | Az a fiók, amelynek a tokenjét kérik. Ez a rövid útmutató egyetlen Account-alkalmazásról szól. Ha többfiókos alkalmazást szeretne létrehozni, meg kell határoznia azt a logikát, amely meghatározza, hogy melyik fiókot használja a jogkivonat-kérelmekhez `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Következő lépések

Próbálja ki az iOS-oktatóanyagot, amely részletes útmutatót nyújt az alkalmazások létrehozásához, beleértve a gyors útmutató részletes ismertetését is.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Ismerje meg, hogyan hozhatja létre az ebben a rövid útmutatóban használt alkalmazást

> [!div class="nextstepaction"]
> [A Graph API meghívása – iOS-oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
