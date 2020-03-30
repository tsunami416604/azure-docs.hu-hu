---
title: Microsoft identity platform iOS és macOS rövid útmutató | Azure
description: Megtudhatja, hogy miként jelentkezik be a felhasználókhoz, és hogyan kérdezheti le a Microsoft Graphot iOS vagy macOS-alkalmazásban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 090f59c4074ca2613c3bd32030b0869a1cd4e9d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80129030"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Rövid útmutató: Jelentkezzen be a felhasználókba, és hívja fel a Microsoft Graph API-t iOS vagy macOS alkalmazásból

Ez a rövid útmutató egy kódmintát tartalmaz, amely bemutatja, hogy egy natív iOS- vagy macOS-alkalmazás hogyan használhatja a Microsoft identity platformot személyes, munkahelyi és iskolai fiókok bejelentkezésére, hozzáférési jogkivonat beszerzésére és a Microsoft Graph API hívására.

Ez a rövid útmutató az iOS- és a macOS-alkalmazásokra egyaránt vonatkozik. Néhány lépés csak az iOS-alkalmazások esetében szükséges. Ezek a lépések azt kiáltják, hogy csak iOS-re szolgálnak.

![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * XCode 10+
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>1. lehetőség: Regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kódmintát
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához
> 1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://aka.ms/MobileAppReg) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely akkor jelenik meg az alkalmazás felhasználói számára, amikor bejelentkeznek, vagy hozzájárulnak az alkalmazáshoz.
>      - A lap egyéb konfigurációinak kihagyása.
>      - Válassza a(z) `Register` lehetőséget.
> 1. A **Kezelés** csoportban `Authentication`  >  `Add Platform`  >  `iOS`válassza a lehetőséget.
>      - Adja meg az alkalmazás ***csomagazonosítóját.*** A csomagazonosító csak egy egyedi karakterlánc, amely egyedileg `com.<yourname>.identitysample.MSALMacOS`azonosítja az alkalmazást, például . Jegyezze fel a használt értéket.
>      - Vegye figyelembe, hogy az iOS-konfiguráció a macOS-alkalmazásokra is alkalmazható.
> 1. Jelölje `Configure` ki és mentse az ***MSAL-konfiguráció*** részleteit a rövid útmutató későbbi részéhez.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> A kódminta a gyorsindítás működéséhez, hozzá kell adnia egy átirányítási URI kompatibilis az Auth broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-ios/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal

#### <a name="step-2-download-the-sample-project"></a>2. lépés: A mintaprojekt letöltése

- [Az iOS-kódminta letöltése](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [A macOS-kódminta letöltése](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>3. lépés: Függőségek telepítése

A terminálablakban keresse meg a letöltött kódmintát `pod install` tartalmazó mappát, és futtassa a legújabb MSAL-könyvtár telepítéséhez.

#### <a name="step-4-configure-your-project"></a>4. lépés: A projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti 1. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Edit **ViewController.swift** és cserélje ki a sort kezdve a "let kClientID" a következő kódrészlet. Ne felejtse el `kClientID` frissíteni az alkalmazás nak a rövid útmutató korábbi során a portálon történő regisztrálásakor mentett ügyfélazonosítóértékét:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Edit **ViewController.swift** és cserélje ki a sort kezdve "let kAuthority" a következő kódrészlet:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Edit **ViewController.swift** és cserélje ki a sort kezdve a "let kGraphEndpoint" a következő kódrészlet:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Nyissa meg a projektbeállításokat. Az **Identitás** szakaszban adja meg a portálra megadott **csomagazonosítót.**
> 1. Csak iOS rendszerben kattintson a jobb gombbal **az Info.plist elemre,** és válassza **a Nyitott forráskódként** > **parancsot.**
> 1. Csak iOS esetén a dikta `CFBundleURLSchemes` gyökércsomópont alatt cserélje le a portálon megadott ***bundleazonosítóval.***
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
> 1. Építsd & futtassa az alkalmazást!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
> [!div renderon="docs"]
>
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Edit **ViewController.swift** és cserélje ki a sort kezdve a "let kClientID" a következő kódrészlet. Ne felejtse el `kClientID` frissíteni az alkalmazás regisztrálásakor a portálon korábban, a rövid útmutatóban mentett ügyfélazonosító értékét:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Ha az [Azure AD nemzeti felhőihez](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)egy alkalmazást hoz fel, cserélje le a "let kGraphEndpoint" és a "let kAuthority" kezdetű vonalat a megfelelő végpontokkal. Globális hozzáférés esetén használja az alapértelmezett értékeket:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. A többi végpont itt van [dokumentálva](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Ha például az Azure AD Germany szolgáltatással szeretné futtatni a rövid útmutatót, használja a következőket:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Nyissa meg a projektbeállításokat. Az **Identitás** szakaszban adja meg a portálra megadott **csomagazonosítót.**
> 1. Csak iOS rendszerben kattintson a jobb gombbal **az Info.plist elemre,** és válassza **a Nyitott forráskódként** > **parancsot.**
> 1. Csak iOS esetén a dikta `Enter_the_bundle_Id_Here` gyökércsomópont alatt cserélje ki a portálon használt ***bundle-azonosítót.***
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
> 1. Építsd & futtassa az alkalmazást! 

## <a name="more-information"></a>További információ

Az erre a rövid útmutatóra vonatkozó további információért tekintse meg ezeket a szakaszokat.

### <a name="get-msal"></a>MSAL beszerezni

Az MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) a Microsoft identity platform által védett API-k eléréséhez használt felhasználók bejelentkezésére és tokenek kérelmezésére használt tár. Az MSAL-t az alábbi eljárással adhatja hozzá az alkalmazáshoz:

```
$ vi Podfile

```
Adja hozzá a következőket a podfilefájlhoz (a projekt céljával):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Futtassa a CocoaPods telepítési parancsát:

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

> |Az elemek magyarázata: ||
> |---------|---------|
> | `clientId` | A *portal.azure.com* webhelyen regisztrált alkalmazásból származó alkalmazásazonosító |
> | `authority` | A Microsoft identity platform végpontja. Ez a legtöbb esetben *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Az alkalmazás átirányítási URI-ja. Átadhatja a "nulla" értéket az alapértelmezett érték használatához, vagy az egyéni átirányítási URI-t. |

### <a name="for-ios-only-additional-app-requirements"></a>Csak iOS esetén további alkalmazáskövetelmények

Az alkalmazásnak a következővel `AppDelegate`is rendelkeznie kell a alkalmazásban. Ez lehetővé teszi, hogy az MSAL SDK kezelje a token választ az Auth broker alkalmazásból a hitelesítés során.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Az iOS 13+ rendszerben, `UISceneDelegate` `UIApplicationDelegate`ha a helyett, `scene:openURLContexts:` helyezze ezt a kódot a visszahívás helyett (lásd az [Apple dokumentációját](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Ha támogatja az UISceneDelegate és az UIApplicationDelegate programot a régebbi iOS-rendszerrel való kompatibilitáshoz, az MSAL-visszahívást mindkét helyre el kell helyezni.

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

Végül az alkalmazásnak `LSApplicationQueriesSchemes` rendelkeznie kell egy bejegyzéssel `CFBundleURLTypes`az ***Info.plist-ben*** a mellett. A minta ezzel együtt érkezik. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Tokenek igénylése & felhasználók bejelentkezése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilent`

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Token interaktív beszerzése

Bizonyos helyzetekben a felhasználóknak kapcsolatba kell lépniük a Microsoft identity platformmal. Ezekben az esetekben előfordulhat, hogy a végfelhasználónak ki kell választania a fiókját, meg kell adnia a hitelesítő adatait, vagy hozzá kell járulnia az alkalmazás engedélyeihez. Például: 

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha egy felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adatait 
* Amikor az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha többes fa- vagy más feltételes hozzáférési házirendre van szükség

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza `[ "user.read" ]` (azaz `[ "<Application ID URL>/scope" ]` a Microsoft Graph`api://<Application ID>/access_as_user`vagy az egyéni webes API-khoz ( ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Egy hozzáférési jogkivonat csendes beszerzése

Az alkalmazások nem követelhetik meg a felhasználóktól, hogy minden alkalommal jelentkezzenek be, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, ez a módszer lehetővé teszi, hogy az alkalmazások csendben kérjenek jogkivonatokat. 

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in
            
   guard let account = currentAccount else {
      return
   }
            
   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Az elemek magyarázata: ||
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza `[ "user.read" ]` (azaz `[ "<Application ID URL>/scope" ]` a Microsoft Graph`api://<Application ID>/access_as_user`vagy az egyéni webes API-khoz ( ) |
> | `account` | A számlakivonathoz szükséges fiók. Ez a rövid útmutató egyetlen fiókalkalmazásról szól. Ha többfiókos alkalmazást szeretne építeni, logika definiálása szükséges a tokenkérelmekhez `accountsFromDeviceForParameters:completionBlock:` használandó és helyes átadáshoz használandó fiók azonosításához.`accountIdentifier` |

## <a name="next-steps"></a>További lépések

Próbálja ki az iOS és macOS rendszerhez készült oktatóanyagot az alkalmazások készítéséhez szükséges részletes útmutatóért, beleértve a rövid útmutató teljes magyarázatát.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Ismerje meg, hogyan hozhat létre az ebben a rövid útmutatóban használt alkalmazást

> [!div class="nextstepaction"]
> [A Call Graph API oktatóanyaga iOS-re és macOS-re](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
