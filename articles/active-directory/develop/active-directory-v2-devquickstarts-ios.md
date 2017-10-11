---
title: "Bejelentkezés hozzáadása egy iOS-alkalmazást az Azure AD v2.0-végponttól |} Microsoft Docs"
description: "Egy iOS-alkalmazást, amely képes bejelentkeztetni a felhasználókat, és mindkét személyes Microsoft-fiók létrehozása és a munkahelyi vagy iskolai fiókok külső könyvtárak használatával."
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf1455dc3d55ea3581195f7a315556d134c23a26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Bejelentkezés hozzáadása egy iOS-alkalmazást használ egy külső könyvtár Graph API-t használ a v2.0-végpontra
A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. A fejlesztők a függvénytárat, hogy integrálni szeretne a szolgáltatások. Segítségével a fejlesztők a platformot használja a többi könyvtárak, azt korábban írt bemutatják, hogyan lehet kapcsolódni a Microsoft identity platform külső szalagtárak konfigurálása a jelen szoftverhez hasonló néhány forgatókönyvek. A legtöbb tárak, amelyek megvalósítják az [a RFC6749 OAuth2 spec](https://tools.ietf.org/html/rfc6749) csatlakozni tud-e a Microsoft identity platform.

Ez a forgatókönyv hoz létre az alkalmazással felhasználók jelentkezzen be a szervezet és majd keresse meg a többi a szervezetek a Graph API használatával.

Ha most ismerkedik az OAuth2 vagy az OpenID Connect, ez a minta konfigurálási nem célszerű Önnek. Azt javasoljuk, hogy olvassa el [v2.0 protokoll - OAuth 2.0 hitelesítési kód Flow](active-directory-v2-protocols-oauth-code.md) a háttérben.

> [!NOTE]
> Egyes szolgáltatások, amelyek rendelkeznek egy kifejezést a OAuth2 vagy az OpenID Connect szabványok, például a feltételes hozzáférés és a Csoportházirend kezelése Intune-ban, a platform kell használni a Microsoft Azure identitáskezelési szalagtárak nyílt forráskódú.
> 
> 

A v2.0-végpontra nem támogatja az összes Azure Active Directory forgatókönyvek és funkciók.

> [!NOTE]
> Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Töltse le a kód a Githubról
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) vagy klónozza a vázat:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

A minta csak is tölthetik le, és rögtön használatba:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy részletes kövesse a [egy alkalmazás regisztrálása a v2.0-végponttal](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolás a **alkalmazásazonosító** , amely hozzá van rendelve az alkalmazás mivel hamarosan lesz szükség.
* Adja hozzá a **Mobile** platform az alkalmazásra vonatkozóan.
* Másolás a **átirányítási URI-** a portálról. Az alapértelmezett értéket kell használnia `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Töltse le a külső NXOAuth2 könyvtárban, és egy munkaterület létrehozása
A forgatókönyv a Githubból, amely a Mac OS X és az iOS rendszerhez (Cocoa és Cocoa touch) OAuth2 könyvtár OAuth2Client fogja használni. A kódtár az OAuth2 specifikációinak 10-es tervezetén alapul. A natív alkalmazásprofil valósítja meg, és a felhasználó az engedélyezési végpont támogatja. Az alábbiakban összes integrálhatja a Microsoft identitásplatformmal együttműködve kell.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>A könyvtár hozzáadása a projekthez a CocoaPods segítségével
A CocoaPods egy Xcode-projektekhez készült függőségkezelő. Automatikusan kezeli a korábbi telepítési lépéseket.

```
$ vi Podfile
```
1. Adja hozzá a következőt a pod-fájlhoz:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. CocoaPods segítségével töltse be a podfile. Ezzel létrehoz egy új Xcode-munkaterületet.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Megismerkedhet a projekt felépítése
A projekt a vázat be van állítva az alábbi szerkezettel:

* Egy nézet egy egyszerű keresés
* A részletes nézet a kiválasztott felhasználóval kapcsolatos adatok
* Ha egy felhasználó bejelentkezhessen a az alkalmazás lekérdezni a diagram bejelentkezési nézet

A vázat a különböző fájlok azt helyezi át, a hitelesítést. A kód, például a visual code más részei identitás nem vonatkoznak, de megadta-e meg.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Állítsa be a settings.plst fájl a könyvtárban
* A gyors üzembe helyezés projektben nyissa meg a `settings.plist` fájlt. Cserélje le az értékeket az elemek a szakaszban az Azure portálon használt értékeknek megfelelően. A kód minden alkalommal az Active Directory Authentication Library hivatkozik ezeket az értékeket.
  * A `clientId` a portálról másolt az alkalmazás ügyfél-azonosító.
  * A `redirectUri` a portálon megadott átirányítási URL-cím.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Állítsa be a LoginViewController a NXOAuth2Client szalagtár
A NXOAuth2Client tartalomtárát néhány érték első beállítása. Miután elvégezte ezt a feladatot, a megszerzett jogkivonat segítségével a Graph API hívása. Mivel `LoginView` lesz nevű bármikor kell hitelesíteni, érdemes, amelyre a konfigurációs értékeket a fájlhoz.

* Adjunk néhány értéket, hogy a `LoginViewController.m` fájlt beállítani a környezetet a hitelesítéshez és engedélyezéshez. Az értékek részleteit kövesse a kódot.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Vizsgáljuk meg a kódot részleteit.

A rendszer az első karakterlánc `scopes`.  A `User.Read` érték lehetővé teszi, hogy olvassa a bejelentkezett felhasználó alapvető profiladataihoz.

Ön tudhat meg többet a rendelkezésre álló összes hatókör [Microsoft Graph-engedélyhatókörök](https://graph.microsoft.io/docs/authorization/permission_scopes).

A `authURL`, `loginURL`, `bhh`, és `tokenURL`, a korábban megadott értékeket kell használnia. Ha használja a Microsoft Azure identitáskezelési szalagtárak nyílt forráskódú, azt lekérik a ezeket az adatokat, a metaadat-végpontjához használatával. A nehezét, azaz az értékek kinyerését mi végezzük Ön helyett.

A `keychain` érték azt a tárolót adja meg, amelyet az NXOAuth2Client kódtár a jogkivonatok tárolására szolgáló kulcslánc létrehozásához fog használni. Ha szeretné beolvasni az egyszeri bejelentkezés (SSO) számos alkalmazások között, adja meg az azonos kulcslánc minden, az alkalmazások, és kérheti, hogy kulcslánc használatát az Xcode jogosultságok. Ennek a magyarázatát, az Apple-dokumentációban.

Ezeket az értékeket a többi is használhassa a kódtárat, és hozzon létre, ha a környezet értékeket hajthat helyeken.

### <a name="create-a-url-cache"></a>Egy URL-gyorsítótár létrehozása
Belül `(void)viewDidLoad()`, amely mindig neve után a nézetben be van töltve, a következő kódot primes jelek használata a gyorsítótárban.

Adja hozzá a következő kódot:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>A bejelentkezés a webes nézet létrehozása
A webes nézet a felhasználótól például SMS szöveges üzenet további tényező (Ha be van állítva), vagy hiba üzeneteket visszaküldeni, a felhasználó számára. Itt lesznek állítva a webes nézet össze, és jegyezze később a visszahívásokat, amely fordul elő a webes nézet az identitás szolgáltatással kezelni a kódot.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Írja felül a webnézet metódusait a hitelesítés kezeléséhez.
Kérje meg a webes nézet, mi történik, ha a felhasználó nem jelentkezhet be a korábban bemutatott, illessze be az alábbi kódot.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Írja meg az OAuth2-kérés eredményét kezelő kódot.
A következő kódot fogja kezelni a redirectURL, amely a webes nézet ad vissza. Ha a hitelesítés nem volt sikeres, a kódot újra próbálkozik. Eközben a könyvtárban fogja biztosítani a hiba, amelyet a konzolon látható elemek vagy aszinkron módon kezeli.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>A (néven fióktároló) OAuth-környezet létrehozása
Itt hívása `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` minden egyes szolgáltatás, amely az alkalmazás által érhetik el a megosztott fiók áruházban. A fiók típusát: egy bizonyos szolgáltatás azonosítóként használt karakterlánc. A Graph API-val érik el, mert a kód hivatkozik rá `"myGraphService"`. Majd állítsa be, amely jelzi, ha bármilyen változás a jogkivonatok megfigyelő. Miután megkapta a jogkivonatot, visszatérési a felhasználó biztonsági a `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Állítsa be a nézet kikeresheti és megjelenítheti a felhasználóknak a Graph API-n
A fő-vezérlő (MVC) alkalmazást, amely a visszaadott adatok megjelennek a rács túlmutat a jelen útmutató, és sok online oktatóprogramok bemutatják, hogyan hozhat létre egyet. Ez a kód az üres fájl van. Azonban kell néhány dolgot az MVC alkalmazás foglalkozik:

* Ha egy felhasználó valamit a keresőmezőbe INTERCEPT
* Az adatok objektum biztosítanak a MasterView vissza a, így azt jelenítheti meg az eredményeket a rács

Igazolnia kell végeznie ezeket az alábbi.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Ellenőrzi, hogy ha jelentkezett be hozzáadása
Az alkalmazás a felhasználó nem jelentkezett be, ha kevés teszi, hogy ellenőrizze, hogy már jogkivonat a gyorsítótárban való intelligens. Ha nem, akkor jelentkezhet be a felhasználó a LoginView átirányítja. Emlékezzen vissza, ha az ajánlott műveletek nézet betöltésekor módja használatára a `viewDidLoad()` módszer, amely Apple velünk.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>A tábla nézet frissítése adatainak fogadásakor.
A Graph API adatokat ad vissza, ha szeretné megjeleníteni az adatokat. Az egyszerűség Ez a tábla frissítéséhez a kódot. Csak az MVC bolierplate kódjában illessze be a megfelelő értékeket.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>A Graph API hívható, ha valaki a keresőmezőt hardvermódosításainak
Ha egy felhasználó egy keresést a lista, a Graph API shove, amely kell. A `GraphAPICaller` osztály alábbi kódjában fog létrehozni, amely elválasztja a bemutató a keresési funkciót. Most ideje lefuttatni a kódot, amely a Graph API-hírcsatornák a keresési karaktereket. A Microsoft ehhez a hívott metódus megadásával `lookupInGraph`, így tovább is azt a keresendő karakterláncot.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>A Graph API eléréséhez egy segítőosztály írása
Ez az alkalmazás alapszolgáltatásai. A többi lett beszúrni a kódot az alapértelmezett MVC mintában az Apple-től, mivel Itt írhat kódot lekérdezni a diagram a felhasználó típusokkal, és térjen vissza az adatokat. A kód itt látható, és részletesen ismerteti az azt követő.

### <a name="create-a-new-objective-c-header-file"></a>Hozzon létre egy új Objective C-fejléc fájlt
A fájl neve `GraphAPICaller.h`, és adja hozzá a következő kódot.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Itt láthatja, hogy a megadott metódus lép egy karakterláncot, és egy completionBlock adja vissza. A completionBlock, akkor előfordulhat, hogy rendelkezik kitalál, frissíteni fogja a tábla azzal, hogy biztosít egy objektum ki van töltve adatok valós idejű, a felhasználó keresi.

### <a name="create-a-new-objective-c-file"></a>Hozzon létre egy új Objective C-fájlt
A fájl neve `GraphAPICaller.m`, és adja hozzá a következő metódust.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Ezzel a módszerrel részletesen lépjen.

Ez a kód legfontosabb van a `NXOAuth2Request`, metódust a paramétereket, amelyek már meghatározta a settings.plist fájlban.

Az első lépés a megfelelő Graph API-hívás összeállításához. Mivel a hívott `/users`, megadja, hogy a verziót és a Graph API erőforrás hozzáfűzésével. Az így kell állítania egy külső beállítások fájlba, mivel ezek módosíthatja az API-t fejlődésének.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Ezután meg kell adnia a paramétereket, akkor is nyújt a Graph API-hívásnak. Az *nagyon fontos* , hogy nem helyezett a paraméterek az erőforrás-végpont mert, amely az összes nem-URI megfelelő karaktereket futásidőben van törlődik. Az összes lekérdezés kód meg kell adni a paraméterek.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Bizonyára észrevette, hogy meghívja a `convertParamsToDictionary` módszer, amely még nem írt. Tekintsük át ezzel a fájl végén:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
A következő most használja az `NXOAuth2Request` módszer segítségével adatokat vissza JSON formátumban API.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Végezetül nézzük hogyan visszatér az adatokat a MasterViewController. Az adatokat adja vissza, mert a szerializált, és kell deszerializálni és betöltött olyan objektum, amely a MainViewController felhasználhat. Erre a célra a vázat tartalmaz egy `User.m/h` fájlt, amely a felhasználó-objektumot hoz létre. Feltölti az adott felhasználói objektum, a graph származó információkkal.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>A minta futtatásához
Ha követte a forgatókönyv az alkalmazás most már működik együtt vagy használt a vázat. Indítsa el a szimulátor, és kattintson a **bejelentkezés** használni az alkalmazást.

## <a name="get-security-updates-for-our-product"></a>A termék biztonsági frissítések beszerzése
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről látogasson el a [biztonsági TechCenter](https://technet.microsoft.com/security/dd252948) és fizessen elő a biztonsági tanácsadói riasztásokra.

