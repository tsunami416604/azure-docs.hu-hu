
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft hitelesítési könyvtár (MSAL) segítségével szolgáltatáshitelesítést egy token a Microsoft Graph API-hoz.

Nyissa meg `ViewController.swift` , és cserélje le a kódot:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>További információ
#### <a name="getting-a-user-token-interactively"></a>A felhasználói token első interaktív módon
Hívja a `acquireToken` módszer eredményezi egy böngészőablakban, jelentkezzen be a felhasználótól. Az alkalmazásoknak általában egy felhasználó a védett erőforrások eléréséhez szükséges először interaktív bejelentkezéshez, vagy ha egy figyelő művelet szerezzen be egy tokent sikertelen (pl. a jelszó lejárt).

#### <a name="getting-a-user-token-silently"></a>A felhasználói token első beavatkozás nélkül
A `acquireTokenSilent` metódus kezeli a token kérése és -megújítás felhasználói beavatkozás nélkül. Miután `acquireToken` végrehajtása az első alkalommal `acquireTokenSilent` az beszerzése a jogkivonatokat, mivel kérelmezése vagy megújítása jogkivonatok hívásainak beavatkozás nélkül történik a további hívások - védett erőforrások eléréséhez használt általánosan használt módszer.

Végül `acquireTokenSilent` sikertelen lesz, – például a felhasználó rendelkezik-e kijelentkezteti, vagy egy másik eszközön a jelszó megváltozott. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MSALErrorCode.interactionRequired` kivétel. Az alkalmazás kezeli ezt a kivételt, két módon:

1.  Hívás elleni `acquireToken` azonnal, ennek eredményeképpen a felhasználótól a bejelentkezéshez. Ebben a mintában általában az online alkalmazások használják, ha nincs kapcsolat nélküli tartalom az alkalmazásban a felhasználó számára elérhető. A mintaalkalmazás az interaktív telepítő által létrehozott használja ezt a mintát: tekintheti meg a kérelem végrehajtása az első művelet időben. Mivel a felhasználó nem használta az alkalmazás `applicationContext.users().first` fogja tartalmazni null értékű, és egy ` MSALErrorCode.interactionRequired ` kivételt fog jelezni. A kód a minta majd kezeli a kivételt meghívásával `acquireToken` eredményezve jelentkezzen be a felhasználótól.

2.  Alkalmazások is végezhet egy visual arra utal, hogy a felhasználót, hogy egy interaktív bejelentkezés szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás újra `acquireTokenSilent` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó éppen megszakad nélkül tudja használni az alkalmazás egyéb funkciók – például nincs offline tartalma elérhető az alkalmazásban. Ebben az esetben a felhasználó eldöntheti, ha szeretne jelentkezzen be a védett erőforrások eléréséhez, vagy frissítse a elavult adatait, vagy eldöntheti, hogy az alkalmazást, majd ismételje meg `acquireTokenSilent` amikor hálózati átmenetileg nem érhető el állapota után állítják vissza.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API használatával csak megszerzett jogkivonattal hívható

Adja hozzá az új metódust alatt `ViewController.swift`. Ez a módszer biztosítja a `GET` kérelmet a Microsoft Graph API segítségével egy *HTTP Authorization fejlécet*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Így egy REST-hívást ellen védett API

Ebben a minta az alkalmazásban a `getContentWithToken()` módszer használható HTTP `GET` kérelem ellen védett erőforrás jogkivonat szükséges, és térjen vissza a tartalom a hívó. Ez a módszer hozzáadja a megszerzett lexikális elem szerepel a *HTTP Authorization fejlécet*. Ez a minta az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Kijelentkezési beállítása

Adja hozzá a következő metódust `ViewController.swift` a felhasználó kijelentkezik:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>További információ a kijelentkezési

A `signoutButton` metódus eltávolítja a felhasználót a MSAL felhasználói gyorsítótárból – Ez hatékonyan jelzi az aktuális felhasználó törlésére, így a későbbi kérelmek egy jogkivonat csak akkor sikeres, ha történik, hogy interaktív MSAL.

Bár ez a példa az alkalmazás támogatja az egy-egy felhasználóhoz, MSAL szituációkat ahol több fiók is bejelentkezhet a egyszerre – Ha a felhasználó rendelkezik-e a több fiók például e-mail alkalmazást.
<!--end-collapse-->

## <a name="register-the-callback"></a>A visszahívási regisztrálása

Miután a felhasználó hitelesíti magát, a böngésző átirányítja a felhasználót az alkalmazásnak. A visszahívási regisztrálni az alábbi lépésekkel:

1.  Nyissa meg `AppDelegate.swift` és MSAL importálása:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Adja hozzá a következő metódust a <code>AppDelegate</code> osztály visszahívások kezeléséhez:
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

