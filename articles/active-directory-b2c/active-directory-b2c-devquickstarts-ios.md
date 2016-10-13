<properties
    pageTitle="Azure Active Directory B2C: webes API meghívása iOS-alkalmazásból külső fejlesztőktől származó kódtárak használatával | Microsoft Azure"
    description="A cikkből megtanulhatja, hogyan hozzon létre olyan „tennivalólista” típusú iOS-alkalmazást, amely OAuth 2.0 tulajdonosi jogkivonatok segítségével képes meghívni egy Node.js webes API-t külső fejlesztőktől származó kódtárak használatával."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="07/26/2016"
    ms.author="brandwe"/>


# Azure AD B2C: webes API meghívása iOS-alkalmazásból külső fejlesztőktől származó kódtárak használatával

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Így a fejlesztők bármilyen típusú kódtárat integrálhatnak szolgáltatásainkkal. Hogy segítséget nyújtsunk a fejlesztőknek platformunk más kódtárakkal való használatában, több különböző útmutatót is írtunk, amelyekből megtudhatják, hogy hogyan kell beállítani úgy a külső fejlesztőktől származó kódtárakat, hogy azok kapcsolódni tudjanak a Microsoft identitásplatformjához. Az [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) használó legtöbb kódtár képes lesz kapcsolódni a Microsoft identitásplatformjához.


Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Platformunknak a szabványokban leképzett több funkciójához (például a feltételes hozzáféréshez vagy az Intune-szabályzatok felügyeletéhez) a nyílt forráskódú Microsoft Azure identitáskódtárainkat is használnia kell. 
   
A B2C platform nem támogatja az összes Azure Active Directory-forgatókönyvet és funkciót.  Ha nem biztos benne, hogy érdemes-e a B2C platformot használnia, olvassa el a [B2C korlátozásait](active-directory-b2c-limitations.md).


## Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az ügyfélalkalmazást és a webes API-t egyetlen **alkalmazásazonosító** képviseli, mivel a két elem közös logikai alkalmazássá áll össze. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

- Az alkalmazáshoz tartozzon egy **mobileszköz**.
- Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Később erre is szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Házirendek létrehozása

Az Azure AD B2C-ben minden felhasználói élményt [házirendek](active-directory-b2c-reference-policies.md) határoznak meg. Az alkalmazás egyetlen identitással kapcsolatos interakciót tartalmaz: egy kombinált regisztrációs és bejelentkezési folyamatot. Az összes típushoz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) leírtak szerint. A szabályzat létrehozásakor ügyeljen arra, hogy:

- A szabályzatban adja meg a **Megjelenített név** értékét, illetve a regisztrációs attribútumokat.
- Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet. Ezenfelül más jogcímeket is használhat.
- Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A szabályzat nevére később még szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.


## A kód letöltése

Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Hogy követni tudja a lépéseket, [töltse le .zip-fájlként az alkalmazást](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip), vagy klónozza:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Vagy egyszerűen csak töltse le az elkészült kódot, és már kezdheti is: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## A külső fejlesztőtől származó nxoauth2 kódtár letöltése és a munkaterület elindítása

Ebben az útmutatóban a GitHubon található OAuth2Client nevű, Mac OS X-hez és iOS-hez (Cocoa és Cocoa touch) készült OAuth2-kódtárat fogjuk használni. A kódtár az OAuth2 specifikációinak 10-es tervezetén alapul. Célja a natív alkalmazásprofil implementálása, valamint a végfelhasználót hitelesítő végpont támogatása. Ezekre mind szükség lesz a Microsoft identitásplatformjával való integrációhoz.

### A kódtár hozzáadása a projekthez a CocoaPods segítségével

A CocoaPods egy Xcode-projektekhez készült függőségkezelő. Automatikusan képes elvégezi a fenti telepítési lépéseket.

```
$ vi Podfile
```
Adja hozzá a következőt a pod-fájlhoz:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Ezt követően töltse be a pod-fájlt a CocoaPods segítségével. Ezzel létrehozza az új XCode-munkaterületet, amelyet később be fog tölteni.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## A projekt struktúrája

A vázban a következő struktúrát állítottuk be a projekthez:

* **Főnézet**, amelyhez munkaablak is tartozik
* **Tevékenység hozzáadása nézet**, amely a kiválasztott feladat adatait tartalmazza
* **Bejelentkezési nézet**, amelyből a felhasználók bejelentkezhetnek az alkalmazásba.

Az útmutató során hitelesítést fogunk adni a projektben szereplő különböző fájlokhoz. A kód más részei, például a vizuális kód nem fontos az identitáskezelés szempontjából, ezért ezt készen bocsátjuk rendelkezésre.

## Az alkalmazáshoz tartozó `settings.plist` fájl létrehozása

Leegyszerűsíti az alkalmazás konfigurálását, ha egyetlen központi helyen tároljuk az összes konfigurációs értékét. Ez ráadásul segít jobban átlátni, hogy mire szolgálnak az egyes beállítások az alkalmazásban. Az értékeket a *Tulajdonságlista* segítségével adjuk át az alkalmazásnak.

* Nyissa meg/hozza létre a `settings.plist` fájlt az alkalmazás munkaterületének `Supporting Files` részén.

* Adja meg az alábbi értékeket (ezeket hamarosan részletesen is elmagyarázzuk).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Nézzük az értékek részletezését.


Az `authURL`, `loginURL`, `bhh` és `tokenURL` értékeknél láthatta, hogy a bérlő nevét kellett megadnia. Ez annak a B2C-bérlőnek a bérlőneve, amelyet Önhöz rendeltek. Például `kidventusb2c.onmicrosoft.com`. Ha a Microsoft Azure identitáskódtárait használja, ezt az információt metaadatvégpontunk segítségével kérjük le Önnek. A nehezét, azaz az értékek kinyerését mi végezzük Ön helyett.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A `keychain` érték azt a tárolót adja meg, amelyet az NXOAuth2Client kódtár a jogkivonatok tárolására szolgáló kulcslánc létrehozásához fog használni. Ha több alkalmazásra érvényes egyszeri bejelentkezést (SSO-t) szeretne használni, adja meg az összes alkalmazásban ugyanezt a kulcsláncot, valamint az XCode-jogosultságokban kérelmezze a kulcslánc használatát. Ennek módját az Apple dokumentációja írja le.

Az egyes URL-címek végén szereplő `<policy name>` azokat a helyeket jelöli, ahová a fentiekben létrehozott szabályzat kerül. Az alkalmazás a folyamattól függően meghívja ezeket a szabályzatokat.

A `taskAPI` az a REST-végpont, amelyet a B2C-jogkivonat segítségével feladatok hozzáadása vagy meglévő feladatok lekérdezése céljából meghívunk. Ezt itt kifejezetten ehhez a mintához állítottuk be, ezért nem szükséges módosítani.

A többi érték a kódtár használatát segíti elő, és olyan helyeket biztosít, amelyek segítségével átadhatja az értékeket a kontextusnak.

Most, hogy létrehoztuk a `settings.plist` fájlt, már csak a kódra van szükség, hogy be tudjuk olvasni.

## AppData osztály létrehozása a beállítások beolvasása érdekében

Hozzunk létre egy egyszerű fájlt, amely elemzi a fentiekben elkészített `settngs.plist` fájlt, és a jövőben bármilyen osztály számára elérhetővé teszi a beállításokat. Mivel nem szeretnénk újabb és újabb másolatot létrehozni az adatokból minden alkalommal, amikor egy osztály lekéri azokat, egy Singleton-mintát fogunk használni, amely mindig ugyanazt a példányt adja vissza a beállításokra vonatkozó kéréseknek.

* Hozzon létre egy `AppData.h` fájlt:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Hozzon létre egy `AppData.m` fájlt:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Ettől kezdve (ahogy azt lentebb látni fogjuk), minden osztályból elég egy `  AppData *data = [AppData getInstance];` hívás az adatok beszerzéséhez.



## Az NXOAuth2Client kódtár beállítása az AppDelegate-ben

Az NXOAuthClient kódtár beállításához különböző értékek szükségesek. Ha ezeket megadta, a létrejövő jogkivonat segítségével meghívhatja a REST API-t. Mivel tudjuk, hogy az alkalmazás betöltésekor a rendszer mindig meghívja az `AppDelegate`-et, logikus lépés ebben a fájlban elhelyezni a konfigurációs értékeket.
* Nyissa meg az `AppDelegate.m` fájlt.

* Importálja azokat a fejlécfájlokat, amelyeket később fogunk használni.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Adja hozzá a `setupOAuth2AccountStore` metódust az AppDelegate-hez.

Létre kell hoznunk egy AccountStore-t, és átadni ennek a `settings.plist` fájlból beolvasott adatokat.

Itt meg kell említenünk néhány dolgot a B2C szolgáltatással kapcsolatban, amely segít érthetőbbé tenni az alábbi kódot:


1. Az Azure AD B2C a lekérdezési paraméterek által biztosított módon használja a *szabályzatot* a kérések teljesítésére. Így az Azure Active Directory független, kizárólag az Ön alkalmazásához kapcsolódó szolgáltatásként tud működni. Ahhoz, hogy megadhassuk ezeket a további lekérdezési paramétereket, el kell látnunk a `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` metódust szabályzatunk egyedi paramétereivel. 

2. Az Azure AD B2C a többi OAuth2-kiszolgálókhoz rendkívül hasonló módon kezeli a hatóköröket. Mivel azonban a B2C használatának célja nemcsak a felhasználók hitelesítése, hanem legalább ilyen mértékben az erőforrások elérhetővé tétele is, ahhoz, hogy a folyamat megfelelően működjön, bizonyos hatókörökre feltétlenül szükség van. Ilyen az `openid` hatókör. A Microsoft identitáskezelő SDK-i automatikusan elérhetővé teszik az `openid` hatókört, így ez itt nem szerepel az SDK konfigurációjában. Mivel azonban külső fejlesztőtől származó kódtárat használunk, ezúttal meg kell adnunk ezt a hatókört.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Ezt követően állítsa be, hogy az AppDelegate is meghívja a hatókört a `didFinishLaunchingWithOptions:` metódus részeként. 

```
[self setupOAuth2AccountStore];
```


## A hitelesítési kérések kezelésére szolgáló `LoginViewController` osztály létrehozása

A fiókkal történő bejelentkezéshez webnézetet használunk. Így felszólíthatjuk a felhasználót különféle további tényezők (például, ha beállítja, SMS-üzenetek) használatára, illetve megjeleníthetjük számára az esetleges hibaüzeneteket. Most beállítjuk a webnézetet, majd később megírjuk a kódot, amely kezelni fogja a webnézetben a Microsoft identitásszolgáltatásától érkező visszahívásokat.

* Hozzon létre egy `LoginViewController.h` osztályt.

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Az egyes metódusokat alább fogjuk létrehozni.

> [AZURE.NOTE] 
    Ne felejtse el összekötni a `loginView`-t a forgatókönyvben szereplő tényleges webnézettel. Ellenkező esetben a webnézet nem fog megjelenni, amikor hitelesítést kell kérni.

* Hozzon létre egy `LoginViewController.m` osztályt.

* Adjon hozzá változókat, amelyek átadják az állapotokat a hitelesítés során.

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Írja felül a webnézet metódusait a hitelesítés kezeléséhez.

Be kell állítanunk, hogy a webnézet mit tegyen, amikor a felhasználók be szeretnének jelentkezni a fentiekben leírtak szerint. Egyszerűen másolja a fájlba az alábbi kódot.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Írja meg az OAuth2-kérés eredményét kezelő kódot.

Szükségünk lesz a kódra, amely képes kezelni a webnézettől visszakapott átirányítási URL-címet. Ha nem jártunk sikerrel, újrapróbálkozunk. Ilyenkor a kódtár megadja a hibát, amelyet aztán megtekinthet a konzolban, vagy aszinkron módon kezelhet. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Állítson be értesítési példányosító metódusokat.

Ugyanazt a metódust állítjuk be, mint fent, az `AppDelegate` esetében, de ezúttal néhány `NSNotification` hozzáadásával elérjük, hogy a metódus tájékoztasson minket arról, hogy mi történik a szolgáltatásban. Beállítunk egy figyelőt, amely közli, ha bármi módosul a jogkivonatban. Ha megvan a jogkivonat, visszaléptetjük a felhasználót a `masterView`-be.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Adja hozzá a kódot, amely kezeli a felhasználót, amikor sign-native kérést kezdeményeznek.

Hozzuk létre a metódust, amelyet a rendszer meghív, ha hitelesítési kérést kap. Ez lesz az a metódus, amely ténylegesen létrehozza a webnézetet.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Végül állítsuk be, hogy a rendszer a `LoginViewController` betöltésekor mindig meghívja a fentiekben megírt összes metódust. Ezt úgy érjük el, hogy hozzáadjuk a metódusokat az Apple-től kapott `viewDidLoad` metódushoz.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Ezzel létrehoztuk a fő módszert, amellyel bejelentkezés céljából interakcióba lehet lépni az alkalmazással. A bejelentkezést követően használni kell a kapott jogkivonatokat. Ehhez meg kell írnunk némi segédkódot, amely a kódtár használatával meghívja a REST API-kat.


## Hozzon létre egy `GraphAPICaller` osztályt, amely kezeli a REST API felé irányuló kéréseket.

Elértük, hogy a rendszer az alkalmazás megnyitásakor betöltse a konfigurációt. Most ezzel kell tennünk valamit, ha megkaptuk a jogkivonatot. 

* Hozzon létre egy `GraphAPICaller.h` fájlt.

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Ebből a kódból látható, hogy két metódust fogunk létrehozni: egyet a feladatok lekérésre az API-tól, egy másikat pedig feladatok API-hoz adására.

Most, hogy beállítottuk az interfészt, adjuk hozzá a tényleges implementációt:

* Hozzon létre egy `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## A mintaalkalmazás futtatása

Végül fordítsa le és futtassa az alkalmazást az Xcode-ban. Regisztráljon vagy jelentkezzen be az alkalmazásba, majd hozzon létre feladatokat a bejelentkezett felhasználónak. Jelentkezzen ki, majd jelentkezzen be egy másik felhasználóval, és hozzon létre feladatokat ennek a felhasználónak is.

Figyelje meg, hogy a rendszer felhasználónként tárolja a feladatokat az API-ban, mivel az API kinyeri a felhasználó identitását a beérkező hozzáférési jogkivonatból.


## Következő lépések

Most már továbbléphet az összetettebb B2C-témákra. Próbálkozzon meg a következőkkel:

[Call a Node.js web API from a Node.js web app (Node.js webes API meghívása Node.js webalkalmazásokból)]()

[Customize the UX for a B2C app (A felhasználói élmény testre szabása B2C-alkalmazásokhoz)]()



<!--HONumber=Oct16_HO1-->


