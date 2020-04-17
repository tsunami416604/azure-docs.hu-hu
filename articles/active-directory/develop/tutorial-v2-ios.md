---
title: MSAL for iOS & macOS tutorial - Microsoft identity platform | Azure
description: Megtudhatja, hogy az iOS- és a macOS-alkalmazások hogyan hívhatnak meg hozzáférési jogkivonatokat igénylő API-t a Microsoft identity platform használatával
services: active-directory
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
ms.openlocfilehash: 1251049add8c9d3c71b6ba13aff24e086613e84b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450955"
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

- XCode 11.x vagy újabb verzió szükséges, hogy az alkalmazás ebben az útmutatóban. Az XCode letölthető az [iTunes webhelyéről.](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-címe")
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
2. Nyissa meg az Alkalmazásregisztrációk panelt, és kattintson **az +Új regisztráció gombra.**
3. Adja meg az alkalmazás **nevét,** majd az átirányítási URI beállítása nélkül.
4. Válassza ki **a fiókokat bármely szervezeti címtárban (Bármilyen Azure AD-címtárban – többbérlős) és személyes Microsoft-fiókokban (pl. Skype, Xbox)** a **Támogatott fióktípusok** csoportban.
5. Kattintson a **Regisztráció gombra**
6. A megjelenő ablaktábla **Kezelés** szakaszában válassza a **Hitelesítés**lehetőséget.

7. Kattintson a Képernyő felső részén található **új élmény** kipróbálása elemre az új alkalmazásregisztrációs felület megnyitásához, majd kattintson az **+Új regisztráció** > **+ Platform** > hozzáadása**iOS/macOS**lehetőségre.
    - Adja meg a projekt bundle-azonosítóját. Ha letöltötte a kódot, ez `com.microsoft.identitysample.MSALiOS`a . Ha saját projektet hoz létre, válassza ki a projektet az Xcode-ban, és nyissa meg az **Általános** lapot. A csomagazonosító megjelenik az **Identitás** szakaszban.
8. Kattintson az `Configure` **MSAL konfigurációs** lapján megjelenő **MSAL-konfigurációra,** és mentse azt, hogy később beírhassa azt az alkalmazás későbbi konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

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

Először adja hozzá a következő importálási utasítást `AppDelegate.swift` `SceneDelegate.swift` a, valamint a `ViewController.swift`fájlok hozadékához:

```swift
import MSAL
```

Ezután adja hozzá `ViewController.swift` a `viewDidLoad()`következő kódot a következőhöz:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
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
var usernameLabel: UILabel!

func initUI() {
        
    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right
        
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
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
        
    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)
        
    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {
                
    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)
        
}
    
@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS felhasználói felület

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!
    
var usernameLabel: NSTextField!

func initUI() {
        
    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
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

func platformViewDidLoadSetup() {}

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
        
        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
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
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
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

- `acquireTokenSilently()`megkísérli a bejelentkezést és a jogkivonatokat felhasználói beavatkozás nélkül, amíg egy fiók jelen van. `acquireTokenSilently()`érvényes, `MSALAccount` az MSAL-fiók enumerálási API-k valamelyikével beolvasható érvényes. Ez a `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` minta az aktuális számla lekéréséhez használatos. 

- `acquireTokenInteractively()`mindig a felhasználói felületet jeleníti meg, amikor megpróbál bejelentkezni a felhasználóba. Használhatja a munkamenet-cookie-kat a böngészőben, vagy a Microsoft hitelesítőjének egy fiókját, hogy interaktív Egyszeri bejelentkezést biztosítson.

Adja hozzá a `ViewController` következő kódot az osztályhoz:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {
        
        self.loadCurrentAccount { (account) in
            
            guard let currentAccount = account else {
                
                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }
            
            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main
                
        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }
            
            if let currentAccount = currentAccount {
                
                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")
                
                self.updateCurrentAccount(account: currentAccount)
                
                if let completion = completion {
                    completion(self.currentAccount)
                }
                
                return
            }
            
            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)
            
            if let completion = completion {
                completion(nil)
            }
        })
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
    parameters.promptType = .selectAccount
        
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
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Token csendes beszerezni

A frissített jogkivonat csendes beszerzéséhez adja `ViewController` hozzá a következő kódot az osztályhoz. Létrehoz egy `MSALSilentTokenParameters` objektumot, és felhívja: `acquireTokenSilent()`

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        /**
         
         Acquire a token for an existing account silently
         
         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */
        
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
            
            if let error = error {
                
                let nsError = error as NSError
                
                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                
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
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
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
> Az MSAL-lal való kijelentkezés eltávolítja a felhasználóösszes ismert információját az alkalmazásból, valamint eltávolítja az eszköz aktív munkamenetét, ha az eszköz konfigurációja engedélyezi. A felhasználót is kijelentkeztetheti a böngészőből.

Kijelentkezési képesség hozzáadásához adja hozzá `ViewController` a következő kódot az osztályon belül. 

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview
            
            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }
                
                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })
            
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
    
    func updateAccountLabel() {
        
        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }
        
        self.usernameLabel.text = currentAccount.username
    }
    
    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS felhasználói felület:

```swift
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
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
    
     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Csak iOS esetén szerezzen be további eszközinformációkat

A következő kóddal olvashatja le az aktuális eszközkonfigurációt, beleértve azt is, hogy az eszköz megosztottként van-e konfigurálva:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {
        
        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }
                
                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Többfiókos alkalmazások

Ez az alkalmazás egyetlen fiókforgatókönyvhöz készült. Az MSAL támogatja a többfiókos forgatókönyveket is, de további munkát igényel az alkalmazásokból. Felhasználói felületet kell létrehoznia, hogy a felhasználók kiválaszthassák, melyik fiókot kívánják használni a jogkivonatokat igénylő minden egyes művelethez. Másik lehetőségként az alkalmazás megvalósíthat egy heurisztikus, hogy melyik fiókot használja az MSAL összes fiókjának lekérdezésével. Például lásd: `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése egy teszteszközre vagy szimulátorra. Képesnek kell lennie arra, hogy jelentkezzen be, és szerezzen be jogkivonatokat az Azure AD-hez vagy a személyes Microsoft-fiókokhoz.

Amikor egy felhasználó először jelentkezik be az alkalmazásba, a Microsoft identitása kérni fogja, hogy járuljon hozzá a kért engedélyekhez.  Bár a legtöbb felhasználó képes a beleegyezésre, néhány Azure AD-bérlők letiltotta a felhasználói beleegyezést, amely megköveteli a rendszergazdák beleegyezését az összes felhasználó nevében. A forgatókönyv támogatásához regisztrálja az alkalmazás hatókörét az Azure Portalon.

Miután bejelentkezett, az alkalmazás megjeleníti a Microsoft `/me` Graph-végpontról visszaadott adatokat.

## <a name="get-help"></a>Segítségkérés

Keresse fel [a Súgót és a támogatást,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) ha problémája van ezzel az oktatóanyaggal vagy a Microsoft identitásplatformjával.
