---
title: Bevezetés az iOS használatába – Microsoft Identity platform | Azure
description: Az iOS (Swift) alkalmazások meghívhatnak olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform használatával
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2968ae229c52cdd2b36111710f0aa6c0dc4fce4c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852414"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Bejelentkezés a felhasználókba és a Microsoft Graph meghívása iOS-alkalmazásból

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egy iOS-alkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, beolvas egy hozzáférési jogkivonatot a Microsoft Graph API meghívásához, és kérelmet készít a Microsoft Graph API-nak.  

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

## <a name="prerequisites"></a>Előfeltételek

- Ebben az útmutatóban a XCode 10. x verziója szükséges az alkalmazás létrehozásához. Letöltheti a XCode az [iTunes webhelyről](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Xcode letöltési URL-címéről").
- Microsoft hitelesítési függvénytár ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Használhatja a függőség-kezelőt, vagy manuálisan is hozzáadhatja a könyvtárat. Az alábbi utasítások bemutatják, hogyan.

Ez az oktatóanyag egy új projektet fog létrehozni. Ha ehelyett a kész oktatóanyagot szeretné letölteni, [töltse le a kódot](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg a Xcode, és válassza **az új Xcode-projekt létrehozása**lehetőséget.
2. Válassza az **iOS** > egynézetes**alkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.
3. Adja meg a terméknév nevét.
4. Állítsa a **nyelvet** a **Swift** értékre, és válassza a **tovább**lehetőséget.
5. Válasszon egy mappát az alkalmazás létrehozásához, majd kattintson a **Létrehozás**gombra.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt](https://aka.ms/MobileAppReg)
2. Nyissa meg a [Alkalmazásregisztrációk](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) panelt, és kattintson az **+ új regisztráció**elemre.
3. Adja meg az alkalmazás **nevét** , majd az átirányítási URI beállítása nélkül kattintson a **regisztráció**elemre.
4. A megjelenő panel **kezelés** szakaszában válassza a **hitelesítés**lehetőséget.
5. Kattintson a képernyő felső részén található **új felület** lehetőségre az új alkalmazás regisztrációs felületének megnyitásához, majd kattintson az **+ új regisztráció** >  **+ platform** > hozzáadása**iOS**elemre.
    - Adja meg a projekt köteg-AZONOSÍTÓját. Ha letöltötte a kódot, akkor ez `com.microsoft.identitysample.MSALiOS`a következő:. Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.
6. Kattintson `Configure` és mentse az **iOS-konfigurációs** lapon megjelenő **MSAL** -konfigurációt, így megadhatja, amikor később konfigurálja az alkalmazást.  Kattintson a **Done** (Kész) gombra.

## <a name="add-msal"></a>MSAL hozzáadása

Válassza ki az alábbi módszerek egyikét a MSAL-könyvtár telepítéséhez az alkalmazásban:

### <a name="cocoapods"></a>CocoaPods

1. Ha `MSAL` `podfile` [a CocoaPods](https://cocoapods.org/)-t használja, először hozzon létre egy üres fájlt, amely ugyanabban a mappában található, mint a projekt fájlja.`.xcodeproj` Adja hozzá a következőket `podfile`a következőhöz:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Cserélje `<your-target-here>` le a helyére a projekt nevét.
3. Egy terminál ablakban navigáljon a létrehozott és futtatott `podfile` `pod install` mappához a MSAL-könyvtár telepítéséhez.
4. A Xcode bezárásához `<your project name>.xcworkspace` és megnyitásához nyissa meg újra a projektet a Xcode-ben.

### <a name="carthage"></a>Carthage

Ha a [Carthage](https://github.com/Carthage/Carthage)-t használja, `MSAL` a telepítéshez `Cartfile`adja hozzá a következőt:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Manuálisan

A git almodult is használhatja, vagy megtekintheti a legújabb kiadást, amelyet keretrendszerként használhat az alkalmazásában.

## <a name="add-your-app-registration"></a>Az alkalmazás regisztrációjának hozzáadása

Ezután hozzáadjuk az alkalmazás regisztrációját a kódhoz. 

Először adja hozzá a következő importálási utasítást a `ViewController.swift` és `AppDelegate.swift` a fájlok elejéhez:

```swift
import MSAL
```

Ezután adja hozzá a következő kódot `ViewController.swift` a `viewDidLoad()`előtt:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Módosítsa az alkalmazás `kClientID`-azonosítóhoz rendelt értéket. Ez az érték azon MSAL-konfigurációs adatmennyiség részét képezi, amelyet az oktatóanyag elején a lépés során mentett, hogy regisztrálja az alkalmazást a Azure Portalban.

## <a name="configure-url-schemes"></a>URL-sémák konfigurálása

Ebben a lépésben regisztrálni `CFBundleURLSchemes` fogja, hogy a felhasználó átirányítható legyen az alkalmazásba a bejelentkezés után. A módon lehetővé teszi `LSApplicationQueriesSchemes` , hogy az alkalmazás használja a Microsoft Authenticator.

A Xcode nyissa `Info.plist` meg a forrásként `<dict>` szolgáló fájlt, és adja hozzá a következőt a szakaszhoz. Cserélje `[BUNDLE_ID]` le a értéket a Azure Portal használt értékre, amely (ha letöltötte a kódot `com.microsoft.identitysample.MSALiOS`) a. Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.

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

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felületének létrehozása

Most hozzon létre egy felhasználói felületet, amely tartalmaz egy gombot a Microsoft Graph API meghívásához, egy másikat a kijelentkezéshez, valamint egy szöveges nézetet, hogy a `ViewController`következő kódot adja hozzá a osztályhoz:

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

Ezután az `ViewController` osztályban belül is cserélje le a `viewDidLoad()` metódust a következőre:

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

## <a name="use-msal"></a>MSAL használata

### <a name="initialize-msal"></a>Initialize MSAL

Adja hozzá a `InitMSAL` következő metódust `ViewController` a osztályhoz:

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

### <a name="handle-the-sign-in-callback"></a>A bejelentkezési visszahívás kezelése

Nyissa meg az `AppDelegate.swift` fájlt. Ha a bejelentkezés után szeretné kezelni a visszahívást `MSALPublicClientApplication.handleMSALResponse` , vegye `appDelegate` fel a következőhöz hasonló osztályba:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Jogkivonatok beszerzése

Most megvalósíthatja az alkalmazás felhasználói felületének feldolgozási logikáját, és interaktív módon lekérheti a jogkivonatokat a MSAL használatával.

A MSAL két elsődleges módszert tesz elérhetővé a tokenek `acquireTokenSilently()` beszerzéséhez: és `acquireTokenInteractively()`: 

- `acquireTokenSilently()`megpróbál bejelentkezni egy felhasználóba, és felhasználói beavatkozás nélkül kap jogkivonatokat, amennyiben van ilyen fiók.

- `acquireTokenInteractively()`mindig a felhasználói FELÜLETET jeleníti meg, amikor megpróbál bejelentkezni a felhasználóba. A böngészőben vagy a Microsoft hitelesítő egyik fiókjában munkamenet-cookie-kat is használhat interaktív egyszeri bejelentkezéses felhasználói élmény biztosításához.

Adja hozzá a következő kódot a `ViewController` osztályhoz:

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

#### <a name="get-a-token-interactively"></a>Token interaktív beszerzése

Az alábbi kód első alkalommal beolvas egy jogkivonatot egy `MSALInteractiveTokenParameters` objektum létrehozásával és meghívásával. `acquireToken` Ezután a következő kódot fogja hozzáadni:

1. Hatókörökkel jön létre `MSALInteractiveTokenParameters` .
2. Meghívja `acquireToken()` a létrehozott paramétereket.
3. Kezeli a hibákat. További részletekért tekintse meg az [iOS-hibák kezelésére szolgáló útmutatót](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Kezeli a sikeres esetet.

Adja hozzá a következő kódot a `ViewController` osztályhoz.

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



#### <a name="get-a-token-silently"></a>Token lekérése csendesen

A frissített jogkivonat csendes beszerzéséhez adja hozzá a következő kódot a `ViewController` osztályhoz. Létrehoz egy `MSALSilentTokenParameters` objektumot és hívásokat `acquireTokenSilent()`:

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

| fejléc kulcsa    | value                 |
| ------------- | --------------------- |
| Authorization | Tulajdonosi \<hozzáférés – token > |

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

A kijelentkezési képesség hozzáadásához adja hozzá a következő kódot az `ViewController` osztályban belül. Ez a módszer az összes fiókra váltást hajt végre, és eltávolítja azokat:

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

### <a name="enable-token-caching"></a>Jogkivonat-gyorsítótárazás engedélyezése

Alapértelmezés szerint a MSAL az iOS-kulcstartóban gyorsítótárazza az alkalmazás jogkivonatait. 

A jogkivonat-gyorsítótárazás engedélyezése:
1. Lépjen a Xcode-projekt beállításai > **képességek lapon** > a**kulcstartó megosztásának engedélyezése** lehetőségre.
2. Kattintson **+** és adja `com.microsoft.adalcache` meg a **kulcstartó csoportok** bejegyzését.

### <a name="add-helper-methods"></a>Segítő metódusok hozzáadása

Adja hozzá a következő segítő metódusokat `ViewController` a osztályhoz a minta végrehajtásához:

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

### <a name="multi-account-applications"></a>Több fiókból álló alkalmazások

Ez az alkalmazás egyetlen fiókra épül. A MSAL támogatja a többfiókos forgatókönyveket is, de az alkalmazások további munkája szükséges. Létre kell hoznia egy felhasználói FELÜLETET, amellyel a felhasználó kiválaszthatja, hogy melyik fiókot szeretné használni a jogkivonatokat igénylő műveletekhez. Azt is megteheti, hogy az alkalmazás egy heurisztikus módszert alkalmaz, amellyel kiválaszthatja, hogy melyik fiókot szeretné használni a `getAccounts()` metódus segítségével.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése tesztelési eszközön vagy emulátoron. A bejelentkezéshez és az Azure AD-vagy személyes Microsoft-fiókokhoz tartozó jogkivonatok beszerzéséhez be kell tudnia jelentkezni.

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft Identity a kért engedélyekkel való beleegyező jogosultságot kér.  Míg a legtöbb felhasználó képes hozzájárulni, néhány Azure AD-bérlő letiltotta a felhasználói beleegyezését, amely megköveteli, hogy a rendszergazdák az összes felhasználó nevében hozzájárulásukat adjanak. A forgatókönyv támogatásához regisztrálja az alkalmazás hatóköreit a Azure Portalban.

A bejelentkezést követően az alkalmazás megjeleníti az Microsoft Graph `/me` végpont által visszaadott adatok megjelenítését.

## <a name="get-help"></a>Segítségkérés

Ha problémája van az Oktatóanyaggal vagy a Microsoft Identity platformmal, látogasson el a [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) webhelyre.

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)