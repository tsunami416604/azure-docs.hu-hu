---
title: IOS – a Microsoft identity platform – első lépések |} Az Azure
description: Hogyan iOS (Swift) alkalmazások hívhatják egy API-t, amely szükséges a Microsoft identity platform használatával hozzáférési jogkivonatok
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e84d97a4ef389981fa30aa08433fef1662367ae
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935947"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>A felhasználók és a Microsoft Graph meghívása iOS-alkalmazások

Ebben az oktatóanyagban megismerheti, hogyan hozhat létre egy iOS-alkalmazás, és integrálják őket, a Microsoft identity platform lesz. Pontosabban az alkalmazás fogja bejelentkeztetni egy felhasználót, a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon és indítson egy alapszintű a Microsoft Graph API.  

Az útmutató befejezése után, az alkalmazás fogad a bejelentkezések a személyes Microsoft-fiókok (beleértve az Outlook.com-os, live.com, és mások), és a munkahelyi vagy iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.

## <a name="how-this-guide-works"></a>Az útmutató működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Ebben a példában az alkalmazás a felhasználók, és a adatok beolvasása a felhasználók nevében.  Ezeket az adatokat egy szükséges engedély és is védi a Microsoft identity platform védett API (ebben az esetben a Microsoft Graph API) keresztül kell elérnie.

Pontosabban:

* Az alkalmazás fogja jelentkezzen be a felhasználó egy böngészőben vagy a Microsoft Authenticator felületén keresztül.
* A felhasználó elfogadja az alkalmazás igényelt engedélyeket. 
* Az alkalmazás fogja kiállítani egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot fog szerepelni a HTTP-kérést a webes API-hoz.
* A Microsoft Graph-válasz feldolgozása.

Ebben a példában a Microsoft Authentication library (MSAL) Outlookhoz megvalósítása Az MSAL automatikusan token megújítása, egyszeri Bejelentkezést biztosít az eszközön lévő más alkalmazások között, és a fiókok kezelése.

## <a name="prerequisites"></a>Előfeltételek

- XCode-verzió 10.x a jelen útmutatóban létrehozott minta megadása kötelező. Az XCode letöltheti a [iTunes webhely](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode letöltési URL-cím").
- A Microsoft-hitelesítési tár ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Függőségkezelő használata, vagy manuálisan adja hozzá. Az alábbi szakaszban van [bővebben](#add-msal). 

## <a name="set-up-your-project"></a>A projekt beállítása

Ebben az oktatóanyagban létrehoz egy új projektet. Ha ehelyett töltse le a befejezett oktatóanyaggal szeretné [letölteni a kódot](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg az xcode-ban, és válassza ki **hozzon létre egy új Xcode-projektben**.
2. Válassza ki **iOS > egynézetes alkalmazás** válassza **tovább**.
3. Adja meg a termék nevét, és válassza ki **tovább**.
4. Adja meg az alkalmazás létrehozása, és kattintson a *létrehozás*.

## <a name="register-your-application"></a>Alkalmazás regisztrálása 

Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="register-your-app"></a>Alkalmazás regisztrálása

1. Nyissa meg a [az Azure portal](https://aka.ms/MobileAppReg) > Válasszon `New registration`. 
2. Adjon meg egy **neve** az alkalmazás > `Register`. **Ne állítson be egy átirányítási URI-t ezen a ponton**. 
3. Az a `Manage` nyissa meg a szakaszban `Authentication` > `Add a platform` > `iOS`
    - Adja meg a projekt alkalmazáscsomag-azonosítót. Ha letöltötte a kódot, akkor ez `com.microsoft.identitysample.MSALiOS`.
4. Találati `Configure` és tárolja a `MSAL Configuration` későbbi használatra. 

## <a name="add-msal"></a>Add MSAL

### <a name="get-msal"></a>Az MSAL beolvasása

#### <a name="cocoapods"></a>CocoaPods

Használhat [CocoaPods](http://cocoapods.org/) telepítéséhez `MSAL` hozzáadásával, hogy a `Podfile` cél alatt:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Használhat [Carthage](https://github.com/Carthage/Carthage) telepítéséhez `MSAL` hozzáadásával, hogy a `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manuálisan

Meg is Git Submodule használja, vagy tekintse meg a legfrissebb kiadás és keretet másként használhatja az alkalmazásban.

### <a name="add-your-app-registration"></a>Az alkalmazásregisztráció hozzáadása

Ezután adja hozzá az alkalmazás regisztrációját a kódot. Adja hozzá a ***ügyfél / alkalmazás azonosítója*** való `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>URL-sémákat konfigurálása

Regisztráljon `CFBundleURLSchemes` , hogy egy visszahívást, így a felhasználó átirányítható vissza az alkalmazás a bejelentkezés után.

`LSApplicationQueriesSchemes` lehetővé teszi, hogy az alkalmazás használatát a Microsoft Authenticator használata, ha elérhető. 

Ehhez nyissa meg a `Info.plist` forrásként kódját, és adja hozzá a következőket, és cserélje le a ***BUNDLE_ID*** együtt az Azure Portalon konfigurált.

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

### <a name="import-msal"></a>Import MSAL

Az importálás MSAL keretrendszer `ViewController.swift` és `AppDelegate.swift` fájlok:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felület létrehozása

A jelen oktatóanyag esetében szeretne létrehozni:

- Gombra a Graph API meghívása
- Jelentkezzen ki a gomb
- Naplózás textview

A `ViewController.swift`, tulajdonságainak definiálása és `initUI()` módon:

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

Ezután adja hozzá `viewDidLoad()` , ViewController.swift:

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

Először is szeretne létrehozni egy `MSALPublicClientApplication` példányával folytatott `MSALPublicClientConfiguration` az alkalmazás:

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

### <a name="handle-the-callback"></a>A visszahívás kezeléséhez

A visszahívás kezeléséhez a bejelentkezést követően, adjon hozzá `MSALPublicClientApplication.handleMSALResponse` a `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Token beszerzése

Most hogy valósítható meg az alkalmazás felhasználói felületén feldolgozó logika és a jogkivonatok interaktív módon az MSAL használatával. 

Az MSAL jogkivonatok lekérésének lépéseiről két elsődleges módszerét mutatja: `acquireTokenSilently` és `acquireTokenInteractively`.  

`acquireTokenSilently()` megpróbálja bejelentkeztetni egy felhasználót, és tokenekhez felhasználói beavatkozás nélkül történik, ha egy fiók található.

`acquireTokenInteractively` minden esetben megjelenik felhasználói felület, amikor a felhasználók bejelentkeztetése és tokenekhez; próbál azonban előfordulhat, hogy használjon a munkamenet-cookie-kat a böngészőben, vagy egy fiókot a Microsoft Authenticator alkalmazást az interaktív egyszeri bejelentkezési felületet biztosíthat. 

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

Egy token beszerzéséhez az első alkalommal, meg kell hozni egy `MSALInteractiveTokenParameters` hívja `acquireToken`.

1. Hozzon létre `MSALInteractiveTokenParameters` hatókörökkel.
2. Hívás `acquireToken` a létrehozott paraméterekkel.
3. Ennek megfelelően kezelni a hibát. További részletekért tekintse meg a [iOS hibakezelés útmutató](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. A sikeres helyzet kezelésére. 

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

Csendes beszerezni a frissített tokent, meg kell hozni egy `MSALSilentTokenParameters` hívja `acquireTokenSilent`:

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

Miután megkapta a jogkivonatot keresztül `self.accessToken`, az alkalmazás használhatja ezt az értéket a HTTP-fejlécben használatával indítson egy arra jogosult a Microsoft Graph:

| Fejléc kulcs    | value                 |
| ------------- | --------------------- |
| Engedélyezés | Tulajdonosi < hozzáférési jogkivonat > |

Adja hozzá a következőt `ViewController.swift`:

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

Tudjon meg többet a [Microsoft Graph API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Használható az MSAL a Kijelentkezés

Ezután akár, hozzáadjuk támogatása kijelentkezés, az alkalmazásnak. 

Fontos megjegyezni, kijelentkezési az MSAL eltávolítja egy felhasználó összes ismert adatait az alkalmazás, de továbbra is rendelkezni fog egy aktív munkamenet az eszközön. Ha a felhasználó megpróbál bejelentkezni újra előfordulhat, hogy tekintse meg a kapcsolati, hanem előfordulhat, hogy nem újra meg kell adnia a hitelesítő adatok miatt az eszköz munkamenet nem aktív. 

Kijelentkezés hozzáadása, másolja be a következő metódust a `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
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

Token-gyorsítótárazási engedélyezéséhez nyissa meg az Xcode projekt beállítások > `Capabilities tab`  >  `Enable Keychain Sharing` > kattintson `Plus` > Enter **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Segédmetódusokat hozzáadása

Adja hozzá ezeket a minta végrehajtásához segédmetódusokat:

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

Ez az alkalmazás egyetlen fiók esetén épül. Az MSAL, valamint több fiók-forgatókönyveket teszi lehetővé, de alkalmazásokból néhány további munkát igényel. Szüksége lesz a felhasználói felület segítségével a felhasználó számára a jogkivonatok igénylő műveletek használni kívánja, hogy melyik fiók létrehozása. Azt is megteheti, az alkalmazás valósíthat meg, válassza ki, melyik fiókot szeretné használni a keresztül heurisztikát a `getAllAccounts(...)` metódust.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Ha követte a fenti kód, próbálja meg hozhat létre és telepítse az alkalmazást a vizsgálati eszközt vagy emulátort a. Meg kell tudni jelentkezzen be, és a jogkivonatok lekérésére, az Azure ad vagy személyes Microsoft-fiókok! Után a felhasználó bejelentkezik, az alkalmazás megjeleníti az adatokat a Microsoft Graph által visszaadott `/me` végpont. 

Ha problémája van, nyugodtan nyissa meg ezt a dokumentumot, vagy az MSAL erőforrástárban problémát, és ossza meg velünk. 

### <a name="consent-to-your-app"></a>Hozzájárulás az alkalmazáshoz

Bármely felhasználó jelentkezik be az alkalmazáshoz, első alkalommal kéri a Microsoft identity hogy engedélyt adjanak az igényelt engedélyeket.  A felhasználók többsége képes a küldőnek, amelyek minden egyes Azure AD-bérlőre letiltotta a felhasználói beleegyezés - igénylő rendszergazdai jóváhagyást az összes felhasználó nevében.  Ez a forgatókönyv támogatása érdekében ügyeljen arra, az alkalmazás hatókörök regisztrálása az Azure Portalon.

## <a name="help-and-support"></a>Súgó és támogatás

Volt ebben az oktatóanyagban vagy a Microsoft identity platform bármilyen hiba? Lásd: [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

