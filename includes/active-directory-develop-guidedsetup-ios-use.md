---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: d5a38d19541e59e0e2815362c0181a8e317a5d0f
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203503"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft-hitelesítési tár (MSAL) használatával egy token beszerzése a Microsoft Graph API-hoz

Nyissa meg `ViewController.swift` , és cserélje le a kódot:

```swift
import UIKit
import MSAL

// A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {

    // Replace Your_Application_Id_Here with the client ID you received in the portal. The below is for running the demo only.
    let kClientID = "Your_Application_Id_Here"

    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"

    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }

    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

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

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
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

<!--start-collapse-->
### <a name="more-information"></a>További információ

#### <a name="getting-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Hívása a `acquireToken` módszer eredményezi egy kéri a felhasználót, hogy jelentkezzen be a böngészőablakot. Az alkalmazásoknak általában egy felhasználó először egy védett erőforrás eléréséhez szükséges interaktív bejelentkezéshez, vagy ha egy figyelő művelet beszerezni egy token sikertelen (például a felhasználó jelszava lejárt).

#### <a name="getting-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután `acquireToken` hajtja végre az első alkalommal `acquireTokenSilent` hívások kérelem vagy token megújítása csendes módban végzett hívások – a védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer.

Végül `acquireTokenSilent` sikertelen lesz, – például a felhasználó kijelentkeztetése rendelkezik, vagy megváltoztatta a jelszavát egy másik eszközön. Ha az MSAL észleli, hogy a probléma megoldhatók egy interaktív intézkedést kér, akkor aktiválódik egy `MSALErrorCode.interactionRequired` kivétel. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

1. Győződjön meg arról, egy hívást kell végrehajtanunk `acquireToken` azonnal, aminek eredményeképpen kéri a felhasználót, hogy jelentkezzen be. Az online alkalmazások általában használják ezt a mintát, ha ott nem nem offline tartalma az alkalmazásban a felhasználó számára elérhető. Az interaktív telepítés által létrehozott mintaalkalmazás használja ezt a mintát: láthatja az alkalmazás végrehajtása az első művelet időben. Nincs felhasználó bármikor használták az alkalmazást, mert `applicationContext.allAccounts().first` fogja tartalmazni a null értéket, és a egy ` MSALErrorCode.interactionRequired ` , a rendszer hibajelzést. A kód a minta ezután kezeli a kivételt meghívásával `acquireToken` eredményez, amely felszólítja a felhasználót a bejelentkezéshez.

2. Alkalmazások tudja végrehajtani a vizuális jelzés a felhasználót, hogy egy interaktív bejelentkezési szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás megpróbálhatja `acquireTokenSilent` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó használhatja az alkalmazás egyéb funkciók anélkül, hogy szakadhat meg – például nincs az offline tartalma elérhető az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, ha szeretnének bejelentkezni a védett erőforrás elérésére, vagy a frissítés az elavult adatokat, vagy az alkalmazás dönt, hogy újra `acquireTokenSilent` amikor hálózati visszaállítása után folyamatban átmenetileg nem érhető el.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API segítségével közvetlenül megszerzett jogkivonattal hívása

Adja hozzá az új metódust alábbi `ViewController.swift`. Győződjön meg arról, hogy ezt a módszert használják egy `GET` kérelmet a Microsoft Graph API használatával egy *HTTP engedélyeztetési fejléc*:

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

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Így a védett API egy REST-hívással

A mintaalkalmazásban a `getContentWithToken()` , hogy a HTTP módszert `GET` kérelmek ellen védett erőforrás, amely a jogkivonat szükséges, és majd a tartalom térjen vissza a hívónak. Ez a metódus hozzáadja a beszerzett lexikális elem szerepel a *HTTP engedélyeztetési fejléc*. Ebben a példában az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Kijelentkezés beállítása

Adja hozzá a következő metódust `ViewController.swift` Kijelentkezés a felhasználói:

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```

<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>További információ a Kijelentkezés

A `signoutButton` módszer eltávolítja a felhasználót az MSAL felhasználók gyorsítótárból – Ez hatékonyan közli az aktuális felhasználó elfelejtette a jogkivonat-beszerzési jövőbeli kérést csak akkor sikeres, ha interaktív történik, így az MSAL.

Bár ebben a példában az alkalmazás támogatja az egy-egy felhasználóhoz, az MSAL forgatókönyvek, ahol több fiókot is bejelentkezhet a egyidejűleg – Ha egy felhasználó jogosult-e több fiókot lehet például egy e-mailek alkalmazás támogatja.
<!--end-collapse-->

## <a name="register-the-callback"></a>A visszahívás regisztrálása

Miután a felhasználó hitelesíti magát, a böngésző átirányítja a felhasználót az alkalmazásnak. Regisztráljon a visszahívás az alábbi lépésekkel:

1. Nyissa meg `AppDelegate.swift` és MSAL importálása:

```swift
import MSAL
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Adja hozzá a következő metódust a <code>AppDelegate</code> osztályt, amely visszahívások kezeli:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
