---
title: Bevezetés az iOS és a macOS használatába – Microsoft Identity platform | Azure
description: IOS-és macOS-(Swift-) alkalmazások hívhatnak olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform használatával
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cfb61f417597abe52910b012ce3fb79ba48ce97
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902839"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Bejelentkezés a felhasználókba és a Microsoft Graph meghívása iOS-vagy macOS-alkalmazásból

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egy iOS-vagy macOS-alkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, beolvas egy hozzáférési jogkivonatot a Microsoft Graph API meghívásához, és kérelmet készít a Microsoft Graph API-nak.  

Az útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

## <a name="how-this-tutorial-works"></a>Az oktatóanyag működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Az oktatóanyagban szereplő alkalmazás bejelentkezik a felhasználók számára, és az Ön nevében kéri le az adatkérést.  Ezek az adatok egy védett API-val (ebben az esetben Microsoft Graph API-val) lesznek elérhetők, és a Microsoft Identity platform védi a hitelesítést.

Pontosabban:

* Az alkalmazás egy böngészőben vagy a Microsoft Authenticator keresztül fog bejelentkezni a felhasználóba.
* A végfelhasználó el fogja fogadni az alkalmazás által kért engedélyeket.
* Az alkalmazás egy hozzáférési jogkivonatot fog kiadni a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot a rendszer a webes API-hoz tartozó HTTP-kérelemben fogja tartalmazni.
* Dolgozza fel a Microsoft Graph választ.

Ez a példa a Microsoft Authentication Library (MSAL) használatával valósítja meg a hitelesítést. A MSAL automatikusan megújítja a tokeneket, egyszeri bejelentkezést (SSO) tesz elérhetővé az eszköz más alkalmazásai között, és felügyeli a fiók (oka) t.

Ez az oktatóanyag az iOS-és macOS-alkalmazásokra is érvényes. Vegye figyelembe, hogy egyes lépések eltérnek a két platform között. 

## <a name="prerequisites"></a>Előfeltételek

- Ebben az útmutatóban a XCode 10. x vagy újabb verziója szükséges az alkalmazás létrehozásához. A XCode letöltése az [iTunes webhelyről](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-címe")végezhető el.
- Microsoft hitelesítési függvénytár ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Használhatja a függőség-kezelőt, vagy manuálisan is hozzáadhatja a könyvtárat. Az alábbi utasítások bemutatják, hogyan.

Ez az oktatóanyag egy új projektet fog létrehozni. Ha ehelyett a kész oktatóanyagot szeretné letölteni, töltse le a következő kódot:
- [iOS-mintakód](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS-mintakód](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg a Xcode, és válassza **az új Xcode-projekt létrehozása**lehetőséget.
2. IOS-alkalmazások esetén válassza az **ios** > **Egynézetes alkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.
3. MacOS-alkalmazások esetén válassza a **macos** > **kakaó-alkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.
4. Adja meg a terméknév nevét.
5. Állítsa a **nyelvet** a **Swift** értékre, és válassza a **tovább**lehetőséget.
6. Válasszon egy mappát az alkalmazás létrehozásához, majd kattintson a **Létrehozás**gombra.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt](https://aka.ms/MobileAppReg)
2. Nyissa meg a [Alkalmazásregisztrációk](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) panelt, és kattintson az **+ új regisztráció**elemre.
3. Adja meg az alkalmazás **nevét** , majd az átirányítási URI beállítása nélkül kattintson a **regisztráció**elemre.
4. A megjelenő panel **kezelés** szakaszában válassza a **hitelesítés**lehetőséget.

5. Kattintson a képernyő felső részén található **új felület** lehetőségre, hogy megnyissa az új alkalmazás regisztrációs élményét, majd kattintson az **+ új regisztráció** >  **+ platform hozzáadása** > **iOS**elemre.
    - Adja meg a projekt köteg-AZONOSÍTÓját. Ha letöltötte a kódot, `com.microsoft.identitysample.MSALiOS`. Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.
    - Vegye figyelembe, hogy macOS esetén iOS-élményt is kell használnia. 
6. Kattintson a `Configure` elemre, és mentse az iOS- **konfigurációs** lapon megjelenő **MSAL-konfigurációt** , hogy később is megadhatja azt az alkalmazás konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

## <a name="add-msal"></a>MSAL hozzáadása

Válassza ki az alábbi módszerek egyikét a MSAL-könyvtár telepítéséhez az alkalmazásban:

### <a name="cocoapods"></a>CocoaPods

1. Ha a [CocoaPods](https://cocoapods.org/)`MSAL`-t használja, akkor először a `podfile` nevű üres fájlt hozzon létre a projekt `.xcodeproj` fájllal megegyező mappában. Adja hozzá a következőt a `podfile`hoz:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Cserélje le a `<your-target-here>`t a projekt nevére.
3. Egy terminál ablakban navigáljon ahhoz a mappához, amely a létrehozott `podfile` tartalmazza, és futtassa `pod install` a MSAL-könyvtár telepítéséhez.
4. A Xcode bezárásához és a `<your project name>.xcworkspace` megnyitásához nyissa meg újra a projektet a Xcode-ben.

### <a name="carthage"></a>Carthage

Ha a [Carthage](https://github.com/Carthage/Carthage)-t használja, telepítse a `MSAL`t úgy, hogy hozzáadja a `Cartfile`hoz:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Futtassa a következő parancsot egy olyan terminál-ablakból, amely a frissített `Cartfile`könyvtárban található, hogy a Carthage frissítse a projekt függőségeit.

iOS

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuálisan

A git almodult is használhatja, vagy megtekintheti a legújabb kiadást, amelyet keretrendszerként használhat az alkalmazásában.

## <a name="add-your-app-registration"></a>Az alkalmazás regisztrációjának hozzáadása

Ezután hozzáadjuk az alkalmazás regisztrációját a kódhoz. 

Először adja hozzá a következő importálási utasítást a `ViewController.swift` és `AppDelegate.swift` fájlok elejéhez:

```swift
import MSAL
```

Ezután adja hozzá a következő kódot a `ViewController.swift`ához `viewDidLoad()`előtt:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParamaters : MSALWebviewParameters?
```

A fenti módosításhoz csak az [alkalmazás-azonosítóhoz](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id)`kClientID`hozzárendelt értéket kell megadni. Ez az érték azon MSAL-konfigurációs adatmennyiség részét képezi, amelyet az oktatóanyag elején a lépés során mentett, hogy regisztrálja az alkalmazást a Azure Portalban.

## <a name="for-ios-only-configure-url-schemes"></a>Csak iOS esetén konfigurálja az URL-sémákat

Ebben a lépésben regisztrálni fogja `CFBundleURLSchemes`, hogy a felhasználó a bejelentkezés után visszairányítható legyen az alkalmazásba. A `LSApplicationQueriesSchemes` azt is lehetővé teszi, hogy az alkalmazás használja a Microsoft Authenticator.

A Xcode-ben nyissa meg `Info.plist` forráskód-fájlként, és adja hozzá a következőt a `<dict>` szakaszhoz. Cserélje le a `[BUNDLE_ID]` értéket a Azure Portalben használt értékre, amely a kód letöltésekor `com.microsoft.identitysample.MSALiOS`. Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Csak macOS esetén konfigurálja az alkalmazási homokozót

1. Nyissa meg a Xcode-projekt beállításai > **képességek fület** > **alkalmazás-homokozó**
2. Válassza a **Kimenő kapcsolatok (ügyfél)** jelölőnégyzetet. 

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felületének létrehozása

Most hozzon létre egy felhasználói felületet, amely tartalmaz egy gombot a Microsoft Graph API meghívásához, egy másikat a kijelentkezéshez, és egy szöveges nézetet, hogy a következő kódot adja hozzá a `ViewController`osztályhoz:

### <a name="ios-ui"></a>iOS felhasználói felület

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>macOS felhasználói felület

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Ezután a `ViewController` osztályon belül is cserélje le a `viewDidLoad()` metódust a következőre:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>MSAL használata

### <a name="initialize-msal"></a>MSAL inicializálása

Adja hozzá a következő `initMSAL` metódust a `ViewController` osztályhoz:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Adja hozzá a következőt, miután `initMSAL` metódust a `ViewController` osztályhoz.

### <a name="ios-code"></a>iOS-kód:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kód:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters()
        self.webViewParamaters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Csak iOS esetén kezelje a bejelentkezési visszahívást

Nyissa meg az `AppDelegate.swift` fájlt. Ha a bejelentkezés után szeretné kezelni a visszahívást, adja hozzá `MSALPublicClientApplication.handleMSALResponse` a `appDelegate` osztályhoz, a következőhöz hasonló módon:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Ha a Xcode 11**-et használja, helyette a MSAL visszahívást kell helyeznie a `SceneDelegate.swift`ba.
Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, akkor a MSAL visszahívást mindkét fájlba be kell helyezni.

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

#### <a name="acquire-tokens"></a>Jogkivonatok beszerzése

Most megvalósíthatja az alkalmazás felhasználói felületének feldolgozási logikáját, és interaktív módon lekérheti a jogkivonatokat a MSAL használatával.

A MSAL két elsődleges módszert tesz elérhetővé a tokenek beszerzéséhez: `acquireTokenSilently()` és `acquireTokenInteractively()`: 

- `acquireTokenSilently()` megpróbál bejelentkezni egy felhasználóba, és felhasználói beavatkozás nélkül kap jogkivonatokat, amíg van egy fiók.

- `acquireTokenInteractively()` mindig megjeleníti a felhasználói FELÜLETET, amikor megpróbál bejelentkezni a felhasználóba. A böngészőben vagy a Microsoft hitelesítő egyik fiókjában munkamenet-cookie-kat is használhat interaktív egyszeri bejelentkezéses felhasználói élmény biztosításához.

Adja hozzá a következő kódot a `ViewController` osztályhoz:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Token interaktív beszerzése

Az alábbi kód első alkalommal beolvas egy jogkivonatot egy `MSALInteractiveTokenParameters` objektum létrehozásával és `acquireToken`meghívásával. Ezután a következő kódot fogja hozzáadni:

1. Hatókörökkel rendelkező `MSALInteractiveTokenParameters` hoz létre.
2. Meghívja az `acquireToken()` a létrehozott paraméterekkel.
3. Kezeli a hibákat. További részletekért tekintse meg a [MSAL for iOS és a MacOS hibakezelés útmutatóját](msal-handling-exceptions.md).
4. Kezeli a sikeres esetet.

Adja hozzá a következő kódot a `ViewController` osztályhoz.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParamaters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Token lekérése csendesen

A frissített jogkivonat csendes beszerzéséhez adja hozzá a következő kódot a `ViewController` osztályhoz. Létrehoz egy `MSALSilentTokenParameters` objektumot, és meghívja a `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>A Microsoft Graph API meghívása 

Ha rendelkezik jogkivonattal, az alkalmazás a HTTP-fejlécben felhasználhatja, hogy jogosult kérést készítsen a Microsoft Graph:

| fejléc kulcsa    | érték                 |
| ------------- | --------------------- |
| Engedélyezés | Tulajdonos \<hozzáférés-token > |

Adja hozzá a következő kódot a `ViewController` osztályhoz:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

A Microsoft Graph API-val kapcsolatos további tudnivalókért tekintse meg [Microsoft Graph API](https://graph.microsoft.com) -t.

### <a name="use-msal-for-sign-out"></a>MSAL használata a kijelentkezéshez

Ezután vegyen fel támogatást a kijelentkezéshez.

> [!Important]
> A MSAL-ből való kijelentkezés eltávolítja az alkalmazással kapcsolatos összes ismert információt, de a felhasználó továbbra is aktív munkamenettel fog rendelkezni az eszközön. Ha a felhasználó ismét megkísérli a bejelentkezést, láthatják a bejelentkezési felhasználói felületet, de előfordulhat, hogy nem kell újra megadniuk a hitelesítő adataikat, mert az eszköz munkamenete még aktív.

A kijelentkezési képesség hozzáadásához adja hozzá a következő kódot a `ViewController` osztályon belül. Ez a módszer az összes fiókra váltást hajt végre, és eltávolítja azokat:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Jogkivonat-gyorsítótárazás engedélyezése

Alapértelmezés szerint a MSAL az iOS-vagy macOS-kulcstartóban gyorsítótárazza az alkalmazás jogkivonatait. 

A jogkivonat-gyorsítótárazás engedélyezése:
1. Győződjön meg arról, hogy az alkalmazás megfelelően van aláírva
2. Nyissa meg a Xcode-projekt beállításai > **képességek lapon** > a **kulcstartó megosztásának engedélyezése** lehetőséget.
3. Kattintson a **+** elemre, és adja meg a következő **kulcstartó-csoportok** bejegyzést: 3. a for iOS, írja be a `com.microsoft.adalcache` 3. b MacOS-be `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Segítő metódusok hozzáadása
A minta végrehajtásához adja hozzá a következő segítő metódusokat a `ViewController` osztályhoz.

### <a name="ios-ui"></a>iOS felhasználói felület:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>macOS felhasználói felület:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Több fiókból álló alkalmazások

Ez az alkalmazás egyetlen fiókra épül. A MSAL támogatja a többfiókos forgatókönyveket is, de az alkalmazások további munkája szükséges. Létre kell hoznia egy felhasználói felületet, amely segítségével a felhasználók kiválaszthatják, hogy melyik fiókot szeretnék használni a tokeneket igénylő műveletekhez. Azt is megteheti, hogy az alkalmazás egy heurisztikus eszközt is megvalósíthat, hogy kiválassza, melyik fiókot szeretné használni a `getAccounts()` metódus segítségével.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése tesztelési eszközön vagy szimulátoron. A bejelentkezéshez és az Azure AD-vagy személyes Microsoft-fiókokhoz tartozó jogkivonatok beszerzéséhez be kell tudnia jelentkezni.

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft Identity a kért engedélyekkel való beleegyező jogosultságot kér.  Míg a legtöbb felhasználó képes hozzájárulni, néhány Azure AD-bérlő letiltotta a felhasználói beleegyezését, amely megköveteli, hogy a rendszergazdák az összes felhasználó nevében hozzájárulásukat adjanak. A forgatókönyv támogatásához regisztrálja az alkalmazás hatóköreit a Azure Portalban.

A bejelentkezést követően az alkalmazás megjeleníti a Microsoft Graph `/me` végpont által visszaadott adatok megjelenítését.

## <a name="get-help"></a>Segítségkérés

Ha problémája van az Oktatóanyaggal vagy a Microsoft Identity platformmal, látogasson el a [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) webhelyre.

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
