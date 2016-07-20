<properties
    pageTitle="Azure Active Directory B2C előzetes verzió: webes API meghívása iOS-alkalmazásból | Microsoft Azure"
    description="A cikkből megtanulhatja, hogyan hozzon létre olyan „tennivalólista” típusú iOS-alkalmazást, amely OAuth 2.0-s tulajdonosi jogkivonatok segítségével képes meghívni egy Node.js webes API-t. Az iOS-alkalmazás és a webes API egyaránt az Azure Active Directory B2C-t használja a felhasználói identitások kezelésére, valamint a felhasználók hitelesítésére."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="05/31/2016"
    ms.author="brandwe"/>

# Azure AD B2C előzetes verzió: webes AI meghívása iOS-alkalmazásból

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Az Azure Active Directory (Azure AD) B2C használatával mindössze néhány lépés elvégzésével hatékony önkiszolgáló identitáskezelési funkciókat adhat iOS-alkalmazásaihoz és webes API-jaihoz. A cikkből megtudhatja, hogyan hozzon létre olyan „tennivalólista” típusú iOS-alkalmazást, amely OAuth 2.0-s tulajdonosi jogkivonatok segítségével képes behívni egy Node.js webes API-t. Az iOS-alkalmazás és a webes API egyaránt az AD B2C-t használja a felhasználói identitások kezelésére, valamint a felhasználók hitelesítésére.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
    A jelen gyorsútmutató teljes körű elvégzéséhez az szükséges, hogy Ön rendelkezzen egy Azure AD B2C által védett webes API-val. Mi építettünk egyet a .NET-hez és a Node.js-hez is, amelyet használhat. Ez az útmutató azt veszi alapul, hogy a Node.js webes API-mintát használja. További információkért olvassa el az [Azure Active Directory web API for Node.js sample](active-directory-b2c-devquickstarts-api-node.md) (Azure Active Directory webes API-minta Node.js-hez) című cikket.
).

> [AZURE.NOTE]
    A cikk nem tér ki az Azure AD B2C segítségével megvalósítható bejelentkezési, regisztrációs és profilkezelési műveletekre. A cikk a webes API-knak a felhasználó hitelesítését követő meghívásával foglalkozik. Ha még nem tette meg, az Azure AD B2C alapjainak megismerése érdekében végezze el a következő oktatóanyagot: [.NET web app get started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md) (Bevezetés a .NET-es webalkalmazásokba). 

## Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az ügyfélalkalmazást és a webes API-t egyetlen **alkalmazásazonosító** képviseli, mivel a két elem közös logikai alkalmazássá áll össze. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

- Az alkalmazás tartalmazzon egy **webalkalmazást vagy webes API-t**.
- A **Reply URL** (Válasz URL-cím) legyen a következő: `http://localhost:3000/auth/openid/return`. Ez a kódminta alapértelmezett URL-címe.
- Hozzon létre egy **alkalmazástitkot** az alkalmazáshoz, majd másolja. Erre később még szüksége lesz.
- Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Később erre is szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Szabályzatok létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Az alkalmazás három különböző, identitással kapcsolatos műveletet tartalmaz: regisztráció, bejelentkezés és bejelentkezés Facebook-fiókkal. Mindháromhoz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) leírtak szerint. A három szabályzat létrehozásakor ügyeljen arra, hogy:

- A regisztrációs szabályzatban adja meg a **Megjelenített név** értékét, illetve a regisztrációs attribútumokat.
- Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet. Ezenfelül más jogcímeket is használhat.
- Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A házirendek nevére később még szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

Felhívjuk figyelmét, hogy ebben a cikkben nem foglalkozunk a létrehozott szabályzatok használatával. Ha szeretné megismerni a szabályzatoknak az Azure AD B2C alatti működését, végezze el a [.NET-es webalkalmazások használatába bevezető oktatóanyagot](active-directory-b2c-devquickstarts-web-dotnet.md).

## A kód letöltése

Az oktatóanyag kódjának [kezelése a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). A minta menet közbeni létrehozásához [töltse le a projekt vázát tartalmazó .zip-fájlt](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). A vázprojektet klónozhatja is:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Az oktatóanyag elvégzéséhez le kell töltenie a vázat.** Mivel teljes funkcionalitású iOS-alkalmazást létrehozni rendkívül összetett feladat, a **váz** tartalmazza a felhasználói élmény kódját, amely futtatható az oktatóanyag elvégzése után. Ezzel a fejlesztő időt takarít meg. A felhasználói élmény kódjának ismertetése nem tartozik a B2C iOS-alkalmazáshoz adásának témaköréhez.

A kész alkalmazás [.zip-fájlként](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) vagy az adott tárház `complete` ágában is elérhető.

Következő lépésként a CocoaPods segítségével töltse be a következőt: `podfile`. Ezzel létrehoz egy új Xcode-munkaterületet. Ha nem rendelkezik a CocoaPods programmal, [keresse fel a webhelyét, és töltse le a szoftvert](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Az iOS-feladatalkalmazás konfigurálása

Ahhoz, hogy az iOS-feladatalkalmazás kommunikálni tudjon az Azure AD B2C-vel, meg kell adnia néhány általános paramétert. A `Microsoft Tasks` mappában, a projekt gyökérkönyvtárában nyissa meg a `settings.plist` fájlt, és cserélje le a `<dict>` szakaszban szereplő értékeket. Ezeket az értékeket az alkalmazás számos részében használni fogjuk.

```
<dict>
    <key>authority</key>
    <string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
    <key>clientId</key>
    <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    <key>scopes</key>
    <array>
        <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    </array>
    <key>additionalScopes</key>
    <array>
    </array>
    <key>redirectUri</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>taskWebAPI</key>
    <string>http://localhost/tasks:3000</string>
    <key>emailSignUpPolicyId</key>
    <string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
    <key>faceBookSignInPolicyId</key>
    <string><your sign in policy for FB></string>
    <key>emailSignInPolicyId</key>
    <string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
    <key>fullScreen</key>
    <false/>
    <key>showClaims</key>
    <true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Hozzáférési jogkivonat lekérése és a feladat-API meghívása

Ebben a részben leírjuk, hogyan végezhet OAuth 2.0 jogkivonatcserét egy webalkalmazásban a Microsoft kódtárai és keretrendszerei segítségével. Ha nincs tisztában a hitelesítési kódok és a hozzáférési jogkivonatok működésével, az [OAuth 2.0 protokoll referenciájában](active-directory-b2c-reference-protocols.md) további információkhoz juthat.

### Fejlécfájlok létrehozása metódusok segítségével

A kiválasztott szabályzatot tartalmazó jogkivonat lekéréséhez, valamint a feladatkiszolgáló meghívásához metódusok használata szükséges. Most ezeket állítjuk be.

Hozzon létre egy `samplesWebAPIConnector.h` nevű fájlt az Xcode projekt alatt, a `/Microsoft Tasks` mappában.

Adja hozzá az alábbi kódot, amely meghatározza a teendőket:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Ezek egyszerű, létrehozás, olvasás, frissítés és törlés (CRUD) típusú műveletek az API-n, valamint a következő metódus: `doPolicy`. A metódus segítségével kérheti le a használni kívánt szabályzathoz tartozó jogkivonatot.

Ezenfelül két másik fejlécfájlt is létre kell hozni. Ezek tartalmazzák a feladatelemeket és a szabályzat adatait. Hozza létre most ezeket:

Hozza létre a `samplesTaskItem.h` fájlt a következő kóddal:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Hozza létre a `samplesPolicyData.h` fájlt is, amely a szabályzati adatokat fogja tárolni:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### A feladatokra és a szabályzatelemekre vonatkozó implementáció hozzáadása

Most, hogy elkészültek a fejlécfájlok, megírhatja a mintában használt adatok tárolására szolgáló kódot.

Hozza létre a `samplesPolicyData.m` fájlt a következő kóddal:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Beállítási kód megírása az iOS-es ADAL meghívásához

A felhasználói felületi objektumok tárolására szolgáló gyorskód elkészült. A következőkben meg kell írnia a kódot, amely a `settings.plist` fájlban elhelyezett paraméterek használatával meghívja az iOS-es Active Directory Authentication Libraryt (ADAL). Így megkapja a hozzáférési jogkivonatot, amelyet aztán átadhat a feladatkiszolgálónak.

Minden műveletet a következő helyen kell elvégezni: `samplesWebAPIConnector.m`.

Először hozza létre a `doPolicy()`-implementációt, amelyet korábban a `samplesWebAPIConnector.h` fejlécfájlban már megírt:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

A metódus felettébb egyszerű. Bemenetei a következők: a létrehozott `samplesPolicyData` objektum, a szülő `ViewController`, illetve egy visszahívás. Minket most a visszahívás érdekel, ezért ezt részletesebben is megnézzük.

- Láthatjuk, hogy a `completionBlock` típusa a következő: `ADProfileInfo`. Ezt a rendszer egy `userInfo` objektum segítségével adja vissza. `ADProfileInfo` típus tárolja a kiszolgáló összes válaszát, köztük a jogcímeket is.
- Nézze meg a következőt is: `readApplicationSettings`. Ez olvassa be az Ön által a `settings.plist` résznél megadott adatokat.
- Végül ott a nagyobb `getClaimsWithPolicyClearingCache` metódus. Ez maga az iOS-es ADAL-nak küldendő hívás, amelyet meg kell írnia. Később még visszatérünk rá.

Következő lépésként írja meg a nagyobb metódust: `getClaimsWithPolicyClearingCache`. Ez elég nagy ahhoz, hogy külön fejezetben foglalkozzunk vele.

### Az iOS-es ADAL-nak küldendő hívás létrehozása

A váznak a GitHubról való letöltését követően láthatja, hogy az számos ilyen hívást tartalmaz a mintaalkalmazás létrehozásának megkönnyítése érdekében. Ezek mindegyike a következő mintát követi: `get(Claims|Token)With<verb>ClearningCache`. Mivel az Objective C nyelv konvencióit használják, szinte olyanok, mintha angolul írták volna őket. Például a „Kérd le a jogkivonatot az általam megadott kiegészítő paraméterekkel, és ürítsd ki a gyorsítótárat” parancs a következőképp néz ki: `getTokenWithExtraParamsClearingCache()`.

A „Kérd le a jogcímeket, valamint a jogkivonatot az által megadott szabályzattal, de ne ürítsd a gyorsítótárat” parancs a következőképp néz ki: `getClaimsWithPolicyClearingCache`. Az ADAL mindig elküldi a jogkivonatot, ezért a metódusban nem szükséges megadni, hogy a jogcímek és jogkivonat lekérése is szükséges. Előfordulhat azonban, hogy csak a jogkivonatra van szüksége, és nem szeretne a jogcímek elemzésével foglalkozni. Erre az esetre a vázban található `getTokenWithPolicyClearingCache` nevű metódus használható.

Írja meg az alábbi kódot:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

A kód eleje bizonyára ismerős lesz.

- Töltse be a `settings.plist` által megadott beállításokat, majd rendelje a következőhöz: `data`.
- Állítsa be a következőt: `ADAuthenticationError`, amely fogadja az iOS-es ADAL által küldött hibákat.
- Hozza létre a következőt: `authContext`, amely az ADAL felé irányuló hívásért felel. A folyamat elindításához meg kell adni ennek a felhatalmazást.
- Hozzon létre a felsőbb szintű vezérlőre mutató hivatkozást a következőben: `authContext`, hogy később vissza tudjon térni hozzá.
- Alakítsa át a `redirectURI`-ben karaktersorként szereplő `settings.plist`-et az ADAL által igényelt NSURL-típussá.
- Állítsa be a `correlationId` azonosítót. Ez az UUID képes követni a meghívást az ügyfélen át a kiszolgálóhoz, és vissza. Ez a hibakeresés során lehet hasznos.

A következő az ADAL felé irányuló tényleges hívás. A hívás itt alakul át az iOS-es ADAL korábbi használata során megszokottakhoz képest:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Láthatja, hogy a hívás viszonylag egyszerű.

`scopes`: azok a hatókörök, amelyeket átad a kiszolgálónak, és amelyeket a bejelentkező felhasználó számára kérelmezni kell a kiszolgálótól. A B2C előzetes verziója esetében a következőnek az átadására van szükség: `client_id`. Ez azonban a jövőben várhatóan olvasási hatókörökre fog módosulni. Ebben az esetben módosítani fogjuk ezt a dokumentumot.
`additionalScopes`: érdemes megfontolni ezeknek a további hatóköröknek a használatát is az alkalmazásban. Terveink szerint a jövőben ezeket is használni fogjuk.
`clientId`: a portáltól kapott alkalmazásazonosító.
`redirectURI`: az átirányítási hely, amelyre a rendszernek vissza kell küldenie a jogkivonatokat.
`identifier`: a felhasználó azonosításának módja, amelynek segítségével eldöntheti, hogy van-e használható jogkivonat a gyorsítótárban. Így nem szükséges mindig újabb jogkivonatot kérni a kiszolgálótól. Ezt egy `ADUserIdentifier` nevű típus tárolja. Az azonosítóként használt elemet Ön adhatja meg. Javasoljuk a `username` használatát.
`promptBehavior`: Elavult. A következőnek kellene itt szerepelnie: `AD_PROMPT_ALWAYS`.
`extraQueryParameters`: bármi más, amit szeretne URL-ként kódolt formában átadni a kiszolgálónak.
`policy`: a meghívott szabályzat. Ez az útmutató legfontosabb eleme.

A `completionBlock` jelzi, hogy átadta a következőt: `ADAuthenticationResult`. Ez tartalmazza a jogkivonatot, valamint a profiladatokat (ha a meghívás sikeres volt).

A fenti kódot használva lekérheti a megadott szabályzathoz tartozó jogkivonatot. A jogkivonat segítségével aztán meghívhatja az API-t.

### A kiszolgálónak küldendő feladathívások létrehozása

Most, hogy megkapta a jogkivonatot, át kell adnia az API-nak a hitelesítés céljából.

Három metódust kell használnia:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` : a feladatokat képviselő tömböt hoz létre a kiszolgálón. `addTask` és `deleteTask`: elvégzik a szükséges műveleteket, és ha azok sikeresek, a `true` vagy a `false` értéket adják vissza.

Írja meg először a `getTaskList` metódust:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Ennek az útmutatónak nem célja a feladatkód részletes bemutatása. Elképzelhető, hogy észrevett valami érdekeset: a `craftRequest` metódust, amely a feladat URL-címét használja bemenetként. Ezzel a metódussal hozza létre a kiszolgálónak küldendő kérelmet a kapott hozzáférési jogkivonat segítségével. Írja meg most ezt.

Adja a következő kódot a `samplesWebAPIConnector.m` fájlhoz:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Ez vesz egy webes URI-t, kiegészíti a jogkivonattal a HTTP `Bearer` fejléc segítségével, majd visszaküldi Önnek az eredményt. A `getTokenClearingCache` API-t kell meghívni. Furcsának tűnhet, de ez a hívás mindössze arra szolgál, hogy lekérje a jogkivonatot a gyorsítótárból, és ellenőrizze az érvényességét. (A `getToken` hívások is ezt a műveletet végzik el az ADAL lekérdezésével.) Ezt a kódrészletet minden egyes hívásban használni kell. A következő lépés a kiegészítő feladatmetódusok létrehozása.

Írja meg a `addTask` metódust:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Ez a korábban megismert mintákat követi, de egyben bevezeti az utoljára létrehozandó metódust, amely a `convertTaskToDictionary`. Ez szótárobjektumot hoz létre a tömbből. Ez az objektum egyszerűbben idomítható a kiszolgálónak küldendő lekérdezési paraméterekhez. A kód egyszerű:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Következő lépésként írja meg a `deleteTask` metódust:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Kijelentkezés hozzáadása az alkalmazáshoz

Az utolsó lépés a kijelentkezés beállítása. Ez felettébb egyszerű: A `sampleWebApiConnector.m` fájlba írja be:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## A mintaalkalmazás futtatása

Végül fordítsa le és futtassa az alkalmazást az Xcode-ban. Regisztráljon vagy jelentkezzen be az alkalmazásba, majd hozzon létre feladatokat a bejelentkezett felhasználónak. Jelentkezzen ki, majd jelentkezzen be egy másik felhasználóval, és hozzon létre feladatokat ennek a felhasználónak is.

Figyelje meg, hogy a rendszer felhasználónként tárolja a feladatokat az API-ban, mivel az API kinyeri a felhasználó identitását a beérkező hozzáférési jogkivonatból.

Referenciaként a teljes minta [elérhető .zip-fájlként is](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Ezenfelül a GitHubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Következő lépések

Most már továbbléphet az összetettebb B2C-témákra. Próbálkozzon meg a következőkkel:

[Call a Node.js web API from a Node.js web app (Node.js webes API meghívása Node.js webalkalmazásokból)]()

[Customize the UX for a B2C app (A felhasználói élmény testre szabása B2C-alkalmazásokhoz)]()



<!--HONumber=Jun16_HO2-->


