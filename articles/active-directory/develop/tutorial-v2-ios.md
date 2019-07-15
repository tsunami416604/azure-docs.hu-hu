---
title: IOS – a Microsoft identity platform – első lépések |} Az Azure
description: Hogyan iOS (Swift) alkalmazások hívhatják egy API-t, amely szükséges a Microsoft identity platform használatával hozzáférési jogkivonatok
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872092"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>A felhasználók és a Microsoft Graph meghívása iOS-alkalmazások

Ebben az oktatóanyagban elsajátíthatja a iOS-alkalmazások integrálása a Microsoft identity platform lesz. Az alkalmazás bejelentkeztetni egy felhasználót, a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon, és indítson egy Microsoft Graph API-t.  

Az útmutató befejezése után, az alkalmazás fogad a bejelentkezések a személyes Microsoft-fiókok (beleértve az Outlook.com-os, live.com, és mások), és a munkahelyi vagy iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Ebben az oktatóanyagban működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Ebben az oktatóanyagban az alkalmazás a felhasználók, és a adatok beolvasása a felhasználók nevében.  Ezeket az adatokat egy védett API (ebben az esetben a Microsoft Graph API), amely engedélyt igényel, és a Microsoft identity platform által védett keresztül kell elérnie.

Pontosabban:

* Az alkalmazás fogja jelentkezzen be a felhasználó egy böngészőben vagy a Microsoft Authenticator felületén keresztül.
* A felhasználó elfogadja az alkalmazás igényelt engedélyeket.
* Az alkalmazás fogja kiállítani egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot fog szerepelni a HTTP-kérést a webes API-hoz.
* A Microsoft Graph-válasz feldolgozása.

Ez a minta a Microsoft Authentication library (MSAL) használja a hitelesítés végrehajtásához. Az MSAL automatikusan token megújítása, egyszeri bejelentkezés (SSO) az eszközön lévő más alkalmazások között szállítja és kezeli a fiók(ok) lesz.

## <a name="prerequisites"></a>Előfeltételek

- XCode-verzió 10.x van szükség ebben az útmutatóban az alkalmazás elkészítésére. Az XCode letöltheti a [iTunes webhely](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-cím").
- A Microsoft-hitelesítési tár ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Egy Függőségkezelő használata, vagy manuálisan adja hozzá a könyvtárban. Az alábbi utasítások bemutatják, hogyan.

Ebben az oktatóanyagban létrehoz egy új projektet. Ha ehelyett töltse le a befejezett oktatóanyaggal szeretné [letölteni a kódot](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg az xcode-ban, és válassza ki **hozzon létre egy új Xcode-projektben**.
2. Válassza ki **iOS** > **egynézetes alkalmazás** válassza **tovább**.
3. Adja meg a termék nevét.
4. Állítsa be a **nyelvi** való **Swift** válassza **tovább**.
5. Adja meg az alkalmazás létrehozása, és kattintson a **létrehozás**.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt](https://aka.ms/MobileAppReg)
2. Nyissa meg a [regisztrációk panelére](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) kattintson **+ új regisztrációs**.
3. Adjon meg egy **neve** , az alkalmazást, majd egy átirányítási URI megadása nélkül, kattintson a **regisztrálása**.
4. Az a **kezelés** a panel, amely akkor jelenik meg, válassza ki a szakasz **hitelesítési**.
5. Kattintson a **az új funkció** nyissa meg az új alkalmazás regisztrációs felhasználói felülete, és kattintson a képernyő tetején **+ új regisztrációs** >  **+ hozzáadása egy platform**  >  **iOS**.
    - Adja meg a projekt alkalmazáscsomag-azonosítót. Ha letöltötte a kódot, akkor ez `com.microsoft.identitysample.MSALiOS`. Ha saját projektet hoz létre, jelölje ki a projektet az xcode-ban, és nyissa meg a **általános** fülre. Megjelenik a csomagazonosítót az **identitás** szakaszban.
6. Kattintson a `Configure` , és mentse a **MSAL konfigurációs** , amely megjelenik a **iOS konfigurációs** lapon, hogy meg lehessen adni azt az alkalmazás későbbi konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

## <a name="add-msal"></a>Add MSAL

Válassza ki az MSAL erőforrástár telepítése az alkalmazásban a következő módszerek valamelyikével:

### <a name="cocoapods"></a>CocoaPods

1. Ha használ [CocoaPods](https://cocoapods.org/), telepítse `MSAL` először hozzon létre egy üres fájlt nevű `podfile` a projekt ugyanabban a mappában `.xcodeproj` fájl. Adja hozzá a következőt `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Cserélje le `<your-target-here>` a projekt nevét.
3. Egy terminálablakban keresse meg a mappát, amely tartalmazza a `podfile` Ön által létrehozott és futtatott `pod install` az MSAL erőforrástár telepítése.
4. Zárja be az xcode-ban, és nyissa meg a `<your project name>.xcworkspace` betölti újból a projektet az xcode-ban.

### <a name="carthage"></a>Carthage

Ha használ [Carthage](https://github.com/Carthage/Carthage), telepítse `MSAL` hozzáadásával, hogy a `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Manuálisan

Git Submodule használja, vagy tekintse meg a legújabb kiadást használja, mint egy keretrendszer, az alkalmazásban való is.

## <a name="add-your-app-registration"></a>Az alkalmazásregisztráció hozzáadása

Ezután az alkalmazás regisztrációját kell hozzáadni a kódhoz. 

Először adja hozzá a következő importálási utasítást elejéhez a `ViewController.swift` és `AppDelegate.swift` fájlok:

```swift
import MSAL
```

Majd adja hozzá a következő kódot a `ViewController.swift` előtt `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Módosítani hozzárendelt érték `kClientID`kell az alkalmazás azonosítóját. Ez az érték az MSAL konfigurációs adatokat, hogy az alkalmazás regisztrálása az Azure Portalon az oktatóanyag elején lépés során mentett részét képezi.

## <a name="configure-url-schemes"></a>URL-sémákat konfigurálása

Ebben a lépésben regisztrálni fogja `CFBundleURLSchemes` úgy, hogy a felhasználó átirányítható vissza az alkalmazás a bejelentkezés után. Egyébként `LSApplicationQueriesSchemes` is lehetővé teszi, hogy az alkalmazások a Microsoft Authenticator használják.

Az xcode-ban nyissa meg a `Info.plist` mint a forráskód fájlt, és adja hozzá a következő található a `<dict>` szakaszban. Cserélje le `[BUNDLE_ID]` értékkel használja az Azure Portalon, azaz, ha letöltötte a kódot `com.microsoft.identitysample.MSALiOS`. Ha saját projektet hoz létre, jelölje ki a projektet az xcode-ban, és nyissa meg a **általános** fülre. Megjelenik a csomagazonosítót az **identitás** szakaszban.

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felület létrehozása

Most hozzon létre egy felhasználói felület, amely tartalmazza az hívja a Microsoft Graph API, jelentkezzen ki, hogy egy másik egy gombot, és egy szöveges megtekintése néhány kimenet megtekintéséhez adja hozzá a következő kódot a `ViewController`osztály:

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

Ezután is belül a `ViewController` osztály, cserélje le a `viewDidLoad()` metódust:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Használható az MSAL

### <a name="initialize-msal"></a>Initialize MSAL

Adja hozzá a következő `InitMSAL` metódust a `ViewController` osztály:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>A bejelentkezési visszahívás kezeléséhez

Nyissa meg az `AppDelegate.swift` fájlt. A visszahívás kezeléséhez a bejelentkezést követően, adjon hozzá `MSALPublicClientApplication.handleMSALResponse` , a `appDelegate` osztály ehhez hasonló:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Token beszerzése

Most azt az alkalmazás felhasználói felületén feldolgozása a logikát alkalmazzák, és az MSAL használatával interaktív módon tokenekhez.

Az MSAL jogkivonatok lekérésének lépéseiről két elsődleges módszerét mutatja: `acquireTokenSilently()` és `acquireTokenInteractively()`: 

- `acquireTokenSilently()` megpróbálja bejelentkeztetni egy felhasználót és felhasználói beavatkozás nélkül tokenekhez, mindaddig, amíg egy fiók megtalálható.

- `acquireTokenInteractively()` felhasználói felület mindig látható, amikor a felhasználó bejelentkezési kísérlet. Munkamenet-cookie-kat a böngészőben vagy a fiókot a Microsoft Authenticator alkalmazást is felhasználhatja, interaktív egyszeri Bejelentkezést biztosít.

Adja hozzá a következő kódot a `ViewController` osztály:

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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

#### <a name="get-a-token-interactively"></a>Interaktív módon egy token beszerzése

Az alábbi kódot kér le egy tokent először hozzon létre egy `MSALInteractiveTokenParameters` objektum és a hívó `acquireToken`. A következő kódot, amely fel fog venni:

1. Hozza létre `MSALInteractiveTokenParameters` hatókörökkel.
2. Hívások `acquireToken()` a létrehozott paraméterekkel.
3. Kezeli a hibákat. További részletekért tekintse meg a [iOS hibakezelés útmutató](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. A sikeres eset kezeli.

Adja hozzá a következő kódot a `ViewController` osztály.

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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



#### <a name="get-a-token-silently"></a>Csendes egy token beszerzése

Csendes beszerezni a frissített tokent, adja hozzá a következő kódot a `ViewController` osztály. Létrehoz egy `MSALSilentTokenParameters` objektum és a hívások `acquireTokenSilent()`:

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

Ha már rendelkezik egy tokent, az alkalmazás használhatja azt a HTTP-fejlécben használatával indítson egy arra jogosult a Microsoft Graph:

| Fejléc kulcs    | value                 |
| ------------- | --------------------- |
| Authorization | Tulajdonosi \<hozzáférési jogkivonat > |

Adja hozzá a következő kódot a `ViewController` osztály:

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

Lásd: [Microsoft Graph API](https://graph.microsoft.com) tudhat meg többet a Microsoft Graph API-val.

### <a name="use-msal-for-sign-out"></a>Használható az MSAL a Kijelentkezés

Ezután adja hozzá a támogatása kijelentkezést.

> [!Important]
> A felhasználó az összes ismert információ lehet kijelentkezni az MSAL eltávolítja az alkalmazást, de továbbra is rendelkezni fog egy aktív munkamenet az eszköz. Ha a felhasználó megpróbál bejelentkezni újra előfordulhat, hogy tekintse meg a bejelentkezési felhasználói felület, hanem előfordulhat, hogy nem kell a hitelesítő adatait adja meg újból, mert az eszköz munkamenet továbbra is aktív.

Kijelentkezési képesség felvételéhez adja hozzá a következő kódot a `ViewController` osztály. Ez a módszer Váltás az összes fiókot, és eltávolítja azokat a:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Token gyorsítótárazásának engedélyezése

Alapértelmezés szerint a MSAL az alkalmazási jogkivonatok meg az IOS-es kulcsláncban gyorsítótárazza. 

A token gyorsítótárazásának engedélyezése:
1. Nyissa meg az Xcode projekt beállítások > **képességek lapon** > **kulcslánc megosztásának engedélyezése**
2. Kattintson a **+** , és adja meg `com.microsoft.adalcache` , egy **Kulcslánccsoportok** bejegyzés.

### <a name="add-helper-methods"></a>Segédmetódusokat hozzáadása

Vegye fel a következő segítő módszert a `ViewController` osztály a minta végrehajtásához:

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

### <a name="multi-account-applications"></a>Több fiók alkalmazások

Ez az alkalmazás egyetlen fiók esetén épül. Az MSAL is több fiók-forgatókönyveket teszi lehetővé, de alkalmazásokból néhány további munkát igényel. Szüksége lesz a felhasználói felület segítségével a felhasználó számára a jogkivonatok igénylő műveletek használni kívánja, hogy melyik fiók létrehozása. Azt is megteheti, az alkalmazás valósíthat meg, válassza ki, melyik fiókot szeretné használni a keresztül heurisztikát a `getAccounts()` metódust.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Hozhat létre és telepítse az alkalmazást a vizsgálati eszközt vagy emulátort a. Meg kell tudni jelentkezzen be, és a jogkivonatok lekérésére, az Azure ad vagy személyes Microsoft-fiókok.

Egy felhasználó jelentkezik be az alkalmazáshoz, első alkalommal kéri a Microsoft identity kért engedélyeket jóváhagyást.  A felhasználók többsége képes a küldőnek, amelyek minden egyes Azure AD-bérlőre letiltotta a felhasználói beleegyezés, amelyhez szükség van a rendszergazdai jóváhagyást az összes felhasználó nevében. Ez a forgatókönyv támogatása érdekében az alkalmazás hatókörök regisztrálja az Azure Portalon.

Miután bejelentkezett, az alkalmazás megjeleníti az adatokat a Microsoft Graph által visszaadott `/me` végpont.

## <a name="get-help"></a>Segítségkérés

Látogasson el [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Ha nem ez az oktatóanyag vagy a Microsoft identitásplatformjához.
