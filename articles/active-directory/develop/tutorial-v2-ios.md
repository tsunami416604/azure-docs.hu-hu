---
title: MSAL for iOS & macOS tutorial - Microsoft identity platform | Azure
description: Megtudhatja, hogy az iOS- és a macOS-alkalmazások hogyan hívhatnak meg hozzáférési jogkivonatokat igénylő API-t a Microsoft identity platform használatával
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abf083aacbdc643d780a8061b405752f36e27e45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129937"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Jelentkezzen be a felhasználókhoz, és hívja fel a Microsoft Graphot iOS vagy macOS alkalmazásból

Ebben az oktatóanyagban megtudhatja, hogyan integrálhat egy iOS- vagy macOS-alkalmazást a Microsoft identitásplatformral. Az alkalmazás bejelentkezik egy felhasználó, kap egy hozzáférési jogkivonatot a Microsoft Graph API hívásához, és kérést küld a Microsoft Graph API-hoz.  

Miután befejezte az útmutatót, az alkalmazás elfogadja a személyes Microsoft-fiókok (beleértve a outlook.com, live.com és másokat) és az Azure Active Directoryt használó vállalatok vagy szervezetek munkahelyi vagy iskolai fiókjainak bejelentkezését.

>[!NOTE]
> Ha most ismeri a Microsoft identity platformot, javasoljuk, hogy kezdje a [Bejelentkezési felhasználókkal, és hívja meg a Microsoft Graph API-t egy iOS vagy macOS alkalmazásból.](quickstart-v2-ios.md)

## <a name="how-this-tutorial-works"></a>Hogyan működik ez a bemutató?

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Az oktatóanyagban található alkalmazás bejelentkezik a felhasználókhoz, és adatokat kap a nevükben.  Ezeket az adatokat egy védett API-n (ebben az esetben a Microsoft Graph API-n) keresztül fogják elérni, amely hez engedély szükséges, és amelyet a Microsoft identity platform véd.

Pontosabban:

* Az alkalmazás böngészőn vagy a Microsoft hitelesítőn keresztül jelentkezik be a felhasználóhoz.
* A végfelhasználó elfogadja az alkalmazás által kért engedélyeket.
* Az alkalmazás kap egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonat szerepelni fog a webes API HTTP-kérelemben.
* Dolgozza fel a Microsoft Graph válaszát.

Ez a minta a Microsoft authentication függvénytárat (MSAL) használja a hitelesítés megvalósításához. Az MSAL automatikusan megújítja a jogkivonatokat, egyszeri bejelentkezést (SSO) biztosít az eszközön lévő többi alkalmazás között, és kezeli a fiók(oka)t.

Ez az oktatóanyag az iOS és a macOS alkalmazásokra egyaránt alkalmazható. Vegye figyelembe, hogy a két platform között néhány lépés eltérő. 

## <a name="prerequisites"></a>Előfeltételek

- XCode 10.x vagy újabb verzió szükséges, hogy az alkalmazás ebben az útmutatóban. Az XCode letölthető az [iTunes webhelyéről.](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-címe")
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Használhatja a függőségkezelőt, vagy manuálisan is hozzáadhatja a tárat. Az alábbi utasítások megmutatják, hogyan.

Ez az oktatóanyag új projektet hoz létre. Ha ehelyett le szeretné tölteni a befejezett oktatóanyagot, töltse le a kódot:
- [iOS mintakód](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS mintakód](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg az Xcode programot, és válassza **az Új Xcode projekt létrehozása**lehetőséget.
2. IOS-alkalmazások esetén válassza az **iOS** > **egynézetes alkalmazást,** majd a **Tovább**gombot.
3. MacOS-alkalmazások esetén válassza a **macOS** > **Cocoa App** lehetőséget, és válassza a **Tovább**gombot.
4. Adja meg a termék nevét.
5. Állítsa a **nyelvet** **Swift** beállításra, és válassza a **Tovább gombot.**
6. Jelöljön ki egy mappát az alkalmazás létrehozásához, és kattintson a **Létrehozás gombra.**

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt](https://aka.ms/MobileAppReg)
2. Nyissa meg az [Alkalmazásregisztrációk panelt,](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) és kattintson **az +Új regisztráció gombra.**
3. Adja meg az alkalmazás **nevét,** majd az átirányítási URI beállítása nélkül kattintson a **Regisztráció gombra.**
4. A megjelenő ablaktábla **Kezelés** szakaszában válassza a **Hitelesítés**lehetőséget.

5. Kattintson a Képernyő felső részén található **új élmény** kipróbálása elemre az új alkalmazásregisztrációs felület megnyitásához, majd kattintson az **+Új regisztráció** > + Platform > **hozzáadása****iOS**lehetőségre.
    - Adja meg a projekt bundle-azonosítóját. Ha letöltötte a kódot, ez `com.microsoft.identitysample.MSALiOS`a . Ha saját projektet hoz létre, válassza ki a projektet az Xcode-ban, és nyissa meg az **Általános** lapot. A csomagazonosító megjelenik az **Identitás** szakaszban.
    - Ne feledje, hogy a macOS rendszerben az iOS-élményt is használnia kell. 
6. Kattintson az `Configure` **iOS konfigurációs** lapján megjelenő **MSAL-konfigurációra,** és mentse azt, hogy később beírhassa azt az alkalmazás későbbi konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

## <a name="add-msal"></a>MSAL hozzáadása

Az msal-könyvtár alkalmazásba való telepítésének az alábbi módjai közül választhat:

### <a name="cocoapods"></a>Kakaós

1. Ha [CocoaPods-ot](https://cocoapods.org/)használ, `MSAL` telepítse először egy `podfile` üres fájlt, amelyet `.xcodeproj` ugyanabban a mappában hívnak meg, mint a projekt fájlját. A következő `podfile`szövega:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Cserélje `<your-target-here>` le a projekt nevére.
3. A terminálablakban keresse meg azt `podfile` a mappát, amely az MSAL-könyvtár telepítéséhez tartalmazza a létrehozott és futtatott `pod install` mappát.
4. Zárja be az `<your project name>.xcworkspace` Xcode-ot, és nyissa meg a projekt újratöltését az Xcode-ban.

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage)használata esetén telepítse `MSAL` a következőhöz `Cartfile`való hozzáadásával:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Egy terminálablakból a frissített `Cartfile`könyvtárban a következő parancsot futtatva a Carthage frissítse a projekt függőségeit.

iOS:

```bash
carthage update --platform iOS
```

Macos:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuálisan

Git almodult is használhat, vagy tekintse meg a legújabb kiadást, amelyet keretrendszerként használhat az alkalmazásban.

## <a name="add-your-app-registration"></a>Az alkalmazásregisztráció hozzáadása

Ezután hozzáadjuk az alkalmazásregisztrációját a kódhoz. 

Először adja hozzá a következő importálási utasítást a `ViewController.swift` fájlok és `AppDelegate.swift` fájlok tetejéhez:

```swift
import MSAL
```

Ezután adja hozzá `ViewController.swift` a `viewDidLoad()`következő kódot a következőhöz:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

A fenti értéket csak az `kClientID` [alkalmazásazonosítóhoz](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id)rendelt értékként kell módosítania. Ez az érték az MSAL konfigurációs adatok, amelyek az oktatóanyag elején az alkalmazás regisztrálásához az oktatóanyag elején mentett lépés során része.

## <a name="for-ios-only-configure-url-schemes"></a>Csak iOS rendszerben konfigurálja az URL-sémákat

Ebben a lépésben `CFBundleURLSchemes` regisztrálni fog, hogy a felhasználó bejelentkezés után visszairányítható az alkalmazásba. By the `LSApplicationQueriesSchemes` way, azt is lehetővé teszi, hogy az alkalmazás, hogy használja a Microsoft Hitelesítő.

Az Xcode-ban nyissa meg `Info.plist` forráskódfájlként, és `<dict>` adja hozzá a szakaszon belül a következőt. Cserélje `[BUNDLE_ID]` le az Azure Portalon használt értékre, amely `com.microsoft.identitysample.MSALiOS`a kód letöltése esetén a . Ha saját projektet hoz létre, válassza ki a projektet az Xcode-ban, és nyissa meg az **Általános** lapot. A csomagazonosító megjelenik az **Identitás** szakaszban.

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

## <a name="for-macos-only-configure-app-sandbox"></a>Csak macOS rendszerben konfigurálja az Alkalmazássandboxot

1. Nyissa meg az Xcode Projektbeállítások > **Képességek lapot** > **Alkalmazás sandbox**
2. Jelölje be **a Kimenő kapcsolatok (ügyfél)** jelölőnégyzetet. 

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felületének létrehozása

Most hozzon létre egy felhasználói felületet, amely tartalmaz egy gombot a Microsoft Graph API hívásához, `ViewController`egy másikat a kijelentkezéshez, és egy szöveges nézetet, hogy megtekinthesse na a kimeneteket a következő kód hozzáadásával az osztályhoz:

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

Ezután az `ViewController` osztályon belül `viewDidLoad()` is cserélje ki a módszert a következőkre:

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

## <a name="use-msal"></a>Az MSAL használata

### <a name="initialize-msal"></a>MSAL inicializálása

Adja hozzá `initMSAL` a `ViewController` következő módszert az osztályhoz:

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

Adja hozzá `initMSAL` a következő `ViewController` módszert az osztályhoz.

### <a name="ios-code"></a>iOS-kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Csak iOS esetén kezelje a bejelentkezési visszahívást

Nyissa meg az `AppDelegate.swift` fájlt. A bejelentkezés utáni visszahívás kezeléséhez adja `MSALPublicClientApplication.handleMSALResponse` hozzá `appDelegate` az osztályhoz a következőket:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Ha Xcode 11-et használ,** az MSAL `SceneDelegate.swift` visszahívását inkább helyezze be.
Ha támogatja mind az UISceneDelegate, mind az UIApplicationDelegate programot a régebbi iOS rendszerrel való kompatibilitásérdekében, az MSAL-visszahívást mindkét fájlba be kell helyezni.

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

#### <a name="acquire-tokens"></a>Tokenek beszerzése

Most már megvalósíthatjuk az alkalmazás felhasználói felületének feldolgozási logikáját, és interaktív módon kaphatunk jogkivonatokat az MSAL-on keresztül.

Az MSAL két elsődleges módszert fed `acquireTokenSilently()` `acquireTokenInteractively()`fel a tokenek megszerzéséhez: és : 

- `acquireTokenSilently()`megkísérli a bejelentkezést és a jogkivonatokat felhasználói beavatkozás nélkül, amíg egy fiók jelen van.

- `acquireTokenInteractively()`mindig a felhasználói felületet jeleníti meg, amikor megpróbál bejelentkezni a felhasználóba. Használhatja a munkamenet-cookie-kat a böngészőben, vagy a Microsoft hitelesítőjének egy fiókját, hogy interaktív Egyszeri bejelentkezést biztosítson.

Adja hozzá a `ViewController` következő kódot az osztályhoz:

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

#### <a name="get-a-token-interactively"></a>Token interaktív beszereznie

Az alábbi kód első alkalommal kap jogkivonatot `MSALInteractiveTokenParameters` `acquireToken`egy objektum létrehozásával és a hívással. Ezután hozzá fog adni kódot, hogy:

1. Hatókörökkel hoz létre. `MSALInteractiveTokenParameters`
2. Hívások `acquireToken()` a létrehozott paraméterekkel.
3. Kezeli a hibákat. További részletekért olvassa el az [MSAL for iOS és macOS hibakezelési útmutatót.](msal-handling-exceptions.md)
4. Kezeli a sikeres esetet.

Adja hozzá a `ViewController` következő kódot az osztályhoz.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
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


#### <a name="get-a-token-silently"></a>Token csendes beszerezni

A frissített jogkivonat csendes beszerzéséhez adja `ViewController` hozzá a következő kódot az osztályhoz. Létrehoz egy `MSALSilentTokenParameters` objektumot, és felhívja: `acquireTokenSilent()`

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

### <a name="call-the-microsoft-graph-api"></a>Hívja a Microsoft Graph API-t 

Miután rendelkezik egy jogkivonatkal, az alkalmazás használhatja azt a HTTP fejlécben, hogy engedélyezett kérést küldjön a Microsoft Graph-nak:

| fejléckulcs    | érték                 |
| ------------- | --------------------- |
| Engedélyezés | \<Tulajdonosi hozzáférési jogkivonat> |

Adja hozzá a `ViewController` következő kódot az osztályhoz:

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

A [Microsoft Graph API-ról](https://graph.microsoft.com) további információért tekintse meg a Microsoft Graph API-t.

### <a name="use-msal-for-sign-out"></a>Az MSAL használata a kijelentkezéshez

Ezután adja hozzá a kijelentkezés támogatását.

> [!Important]
> Az MSAL-lal való kijelentkezés eltávolítja a felhasználóösszes ismert információját az alkalmazásból, de a felhasználó nak továbbra is lesz aktív munkamenete az eszközön. Ha a felhasználó ismét megpróbál bejelentkezni, előfordulhat, hogy bejelentkező felhasználói felületet lát, de nem kell újra megadnia a hitelesítő adatait, mert az eszközmunkamenet még aktív.

Kijelentkezési képesség hozzáadásához adja hozzá `ViewController` a következő kódot az osztályon belül. Ez a módszer végighalad az összes fiókon, és eltávolítja őket:

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

### <a name="enable-token-caching"></a>Token-gyorsítótárazás engedélyezése

Alapértelmezés szerint az MSAL gyorsítótárazza az alkalmazás tokenjeit az iOS vagy a macOS kulcskarikában. 

Token-gyorsítótárazás engedélyezése:
1. Az alkalmazás megfelelő aláírása
2. Nyissa meg az Xcode Projektbeállítások > **képességek lapot** > **Kulcskarika megosztásának engedélyezése**
3. Kattintson **+** a következő Kulcskarika csoportok bejegyzésre, és `com.microsoft.adalcache` írja be a következő **billentyűt:** 3.a IOS esetén írja be a 3.b A macOS-t írja be`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Segítő metódusok hozzáadása
Adja hozzá a következő `ViewController` segítő módszereket az osztályhoz a minta befejezéséhez.

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



### <a name="multi-account-applications"></a>Többfiókos alkalmazások

Ez az alkalmazás egyetlen fiókforgatókönyvhöz készült. Az MSAL támogatja a többfiókos forgatókönyveket is, de további munkát igényel az alkalmazásokból. Felhasználói felületet kell létrehoznia, hogy a felhasználók kiválaszthassák, melyik fiókot kívánják használni a jogkivonatokat igénylő minden egyes művelethez. Másik lehetőségként az alkalmazás egy heurisztikát is `getAccounts()` megvalósíthat, hogy kiválassza, melyik fiókot használja a módszerrel.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése egy teszteszközre vagy szimulátorra. Képesnek kell lennie arra, hogy jelentkezzen be, és szerezzen be jogkivonatokat az Azure AD-hez vagy a személyes Microsoft-fiókokhoz.

Amikor egy felhasználó először jelentkezik be az alkalmazásba, a Microsoft identitása kérni fogja, hogy járuljon hozzá a kért engedélyekhez.  Bár a legtöbb felhasználó képes a beleegyezésre, néhány Azure AD-bérlők letiltotta a felhasználói beleegyezést, amely megköveteli a rendszergazdák beleegyezését az összes felhasználó nevében. A forgatókönyv támogatásához regisztrálja az alkalmazás hatókörét az Azure Portalon.

Miután bejelentkezett, az alkalmazás megjeleníti a Microsoft `/me` Graph-végpontról visszaadott adatokat.

## <a name="get-help"></a>Segítségkérés

Keresse fel [a Súgót és a támogatást,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) ha problémája van ezzel az oktatóanyaggal vagy a Microsoft identitásplatformjával.

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
