---
title: Az iOS SDK használata
description: Az iOS SDK használata az Azure mobile apps hez
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249346"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Az iOS-ügyfélkönyvtár használata az Azure Mobile Apps alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogy a legújabb [Azure Mobile Apps iOS SDK][1]használatával hajtson végre gyakori forgatókönyveket. Ha most jön az Azure Mobile Apps, először teljes [Azure Mobile Apps gyorsan hozzon] létre egy háttérrendszer, hozzon létre egy táblázatot, és töltse le az előre elkészített iOS Xcode projekt. Ebben az útmutatóban az ügyféloldali iOS SDK-ra összpontosítunk. Ha többet szeretne megtudni a kiszolgálóoldali SDK a háttérrendszer, tekintse meg a kiszolgáló SDK HOWTo-k.

## <a name="reference-documentation"></a>Referenciadokumentáció

Az iOS-ügyfél SDK referenciadokumentációja itt található: [Azure Mobile Apps iOS-ügyfélreferencia.][2]

## <a name="supported-platforms"></a>Támogatott platformok

Az iOS SDK támogatja az Objective-C, Swift 2.2 projekteket és Swift 2.3 projekteket az iOS 8.0-s vagy újabb verzióihoz.

A "kiszolgáló-flow" hitelesítés webnézetet használ a bemutatott felhasználói felülethez.  Ha az eszköz nem tudja bemutatni a WebView felhasználói felületet, akkor egy másik hitelesítési módszerre van szükség, amely kívül esik a termék hatókörén.  
Ez az SDK ezért nem alkalmas watch-típusú vagy hasonlóan korlátozott eszközökhöz.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Telepítés és előfeltételek

Ez az útmutató feltételezi, hogy létrehozott egy háttérrendszer egy táblával. Ez az útmutató feltételezi, hogy a tábla ugyanazokkal a sémával rendelkezik, mint az oktatóanyagokban lévő táblák. Ez az útmutató azt is feltételezi, `MicrosoftAzureMobile.framework` hogy `MicrosoftAzureMobile/MicrosoftAzureMobile.h`a kódban hivatkozik és importál.

## <a name="how-to-create-client"></a><a name="create-client"></a>Útmutató: Ügyfél létrehozása

Ha el szeretne érni egy Azure Mobile `MSClient`Apps-háttérszolgáltatást a projektben, hozzon létre egy . Cserélje `AppUrl` le az alkalmazás URL-címét. Elmehetsz `gatewayURLString` és `applicationKey` kiüríthetsz. Ha beállít egy átjárót a hitelesítéshez, feltölti `gatewayURLString` az átjáró URL-címét.

**C célkitűzés**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Gyors**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="how-to-create-table-reference"></a><a name="table-reference"></a>Útmutató: Táblázathivatkozás létrehozása

Az adatok elérése vagy frissítése érdekében hozzon létre a háttértáblára mutató hivatkozást. A `TodoItem` helyére írja be a tábla nevét.

**C célkitűzés**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Gyors**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="how-to-query-data"></a><a name="querying"></a>Útmutató: Lekérdezési adatok

Adatbázis-lekérdezés létrehozásához kérdezze `MSTable` le az objektumot. A következő lekérdezés beszerzi az összes elemet, `TodoItem` és naplózza az egyes elemek szövegét.

**C célkitűzés**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Gyors**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Útmutató: A visszaadott adatok szűrése

Az eredmények szűréséhez számos lehetőség áll rendelkezésre.

Predikátum használatával történő `NSPredicate` szűréshez használjon és a . `readWithPredicate` A következő szűrők csak a hiányos Teendőelemek megkereséséhez adták vissza az adatokat.

**C célkitűzés**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Gyors**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-use-msquery"></a><a name="query-object"></a>Útmutató: Az MSQuery használata

Összetett lekérdezés (beleértve a rendezést és a `MSQuery` lapozást is) végrehajtásához hozzon létre egy objektumot közvetlenül vagy predikátum használatával:

**C célkitűzés**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Gyors**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`lehetővé teszi több lekérdezési viselkedés vezérlését.

* Az eredmények sorrendjének megadása
* A visszaadandó mezők korlátozása
* A visszaadandó rekordok listájának korlátozása
* Válaszként imitált teljes szám megadása
* Egyéni lekérdezési karakterlánc-paraméterek megadása a kérelemben
* További függvények alkalmazása

`MSQuery` Lekérdezés végrehajtása az `readWithCompletion` objektum meghívásával.

## <a name="how-to-sort-data-with-msquery"></a><a name="sorting"></a>Útmutató: Adatok rendezése az MSQuery segítségével

Az eredmények rendezéséhez nézzünk meg egy példát. A "szöveg" mező növekvő, majd a "teljes" `MSQuery` csökkenő sorrendben történő rendezéshez így hivatkozzon:

**C célkitűzés**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Gyors**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-limit-fields-and-expand-query-string-parameters-with-msquery"></a><a name="selecting"></a><a name="parameters"></a>Útmutató: Mezők korlátozása és lekérdezési karakterlánc-paraméterek kibontása az MSQuery segítségével

A lekérdezésben visszaadandó mezők korlátozásához adja meg a **selectFields** tulajdonság mezőinek nevét. Ez a példa csak a szöveget és a befejezett mezőket adja vissza:

**C célkitűzés**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Gyors**:

```swift
query.selectFields = ["text", "complete"]
```

Ha további lekérdezési karakterlánc-paramétereket szeretne felvenni a kiszolgálói kérelembe (például `query.parameters` azért, mert egy egyéni kiszolgálóoldali parancsfájl használja őket), a következőket használja:

**C célkitűzés**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Gyors**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="how-to-configure-page-size"></a><a name="paging"></a>Útmutató: Oldalméret konfigurálása

Az Azure Mobile Apps, az oldalméret szabályozza a rekordok számát, amelyek lekérik egy időben a háttértáblák. Az adatok `pull` hívása ezután az oldalméret alapján kötegeli az adatokat, amíg nincs több lehívható rekord.

Lehetőség van az oldalméret konfigurálására **az MSPullSettings** használatával, az alábbiak szerint. Az alapértelmezett oldalméret 50, és az alábbi példa 3-ra változtatja.

Teljesítménybeli okokból más oldalméretet is konfigurálhat. Ha nagy számú kis adatrekordal rendelkezik, a magas oldalméret csökkenti a kiszolgálói átutazások számát.

Ez a beállítás csak az ügyféloldalon lévő oldalméretet szabályozza. Ha az ügyfél nagyobb oldalméretet kér, mint amit a mobilalkalmazások háttérrendszere támogat, az oldalméret a háttérrendszer által támogatott maximális korláton lesz korlátozva.

Ez a beállítás az adatrekordok *számát* is jelenti, nem a *bájtméretet.*

Ha növeli az ügyféloldal méretét, akkor a kiszolgálón is növelnie kell az oldalméretet. Az ehhez szükséges lépésekről a ["Hogyan: A táblázat lapozási méretének beállítása"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) című témakörben található.

**C célkitűzés**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Gyors**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="how-to-insert-data"></a><a name="inserting"></a>Útmutató: Adatok beszúrása

Új táblázatsor beszúrásához hozzon létre egy `NSDictionary` és invoke . `table insert` Ha [a dinamikus séma] engedélyezve van, az Azure App Service `NSDictionary`mobilháttér-rendszer automatikusan új oszlopokat hoz létre a alapján.

Ha `id` nincs megadva, a háttérrendszer automatikusan létrehoz egy új egyedi azonosítót. Adja meg `id` saját e-mail címét, felhasználónevét vagy saját egyéni értékeit azonosítóként. Saját azonosító megadása megkönnyítheti az illesztéseket és az üzleti alapú adatbázislogikát.

A `result` tartalmazza a beszúrt új elemet. A kiszolgáló logikájától függően előfordulhat, hogy további vagy módosított adatokkal rendelkezik a kiszolgálónak átadott adatokhoz képest.

**C célkitűzés**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Gyors**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="how-to-modify-data"></a><a name="modifying"></a>Útmutató: Adatok módosítása

Meglévő sor frissítéséhez módosítsa az `update`elemet, és hívja meg a következőket:

**C célkitűzés**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Gyors**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Másik lehetőségként adja meg a sorazonosítót és a frissített mezőt:

**C célkitűzés**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Gyors**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

A frissítések `id` készítésekor legalább az attribútumot be kell állítani.

## <a name="how-to-delete-data"></a><a name="deleting"></a>Útmutató: Adatok törlése

Elem törléséhez hívja `delete` meg a következő elemet:

**C célkitűzés**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Gyors**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Másik lehetőségként a törlés egy sorazonosító megadásával:

**C célkitűzés**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Gyors**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

A törléskor `id` legalább be kell állítani az attribútumot.

## <a name="how-to-call-custom-api"></a><a name="customapi"></a>Útmutató: Egyéni API hívása

Az egyéni API-val bármilyen háttérfunkció elérhetővé teheti. Nem kell asztalművelethez megfelelnie. Nem csak akkor kapnak több ellenőrzést üzenetküldés, akkor is olvasni / beállítani fejlécek és változtassa meg a válasz törzs formátumban.

Egyéni API hívásához `MSClient.invokeAPI`hívja meg a hívást. A kérelem és a válasz tartalmát json ként kezeli a hívás. Más adathordozó-típusok használatához [használja a `invokeAPI`többi túlterhelést. ][5]  Ha kérés `GET` helyett `POST` kérést szeretne `HTTPMethod` küldeni, állítsa a paramétert `"GET"` és a paramétert `body` `nil` (mivel a GET-kérelmeknek nincsenek üzenettörzsei.) Ha az egyéni API támogatja a `HTTPMethod` többi HTTP-műveletek, módosítsa megfelelően.

**C célkitűzés**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Gyors**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="templates"></a>Útmutató: Push sablonok regisztrálása platformfüggetlen értesítések küldéséhez

Sablonok regisztrálásához adja át a sablonokat a **client.push registerDeviceToken** metódussal az ügyfélalkalmazásban.

**C célkitűzés**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Gyors**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

A sablonok NSDictionary típusúak, és több sablont is tartalmazhatnak a következő formátumban:

**C célkitűzés**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Gyors**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Minden címke levan vonva a biztonsági kérelemtől.  Ha címkéket szeretne hozzáadni a telepítésekhez vagy sablonokhoz a [.NET háttérkiszolgáló SDK használata az Azure Mobile Apps szolgáltatáshoz][4]című témakörben olvashat.  Ha ezeket a regisztrált sablonokat használó értesítéseket szeretne küldeni, működjön együtt [az Értesítési központok API-jával.][3]

## <a name="how-to-handle-errors"></a><a name="errors"></a>Útmutató: Hibák kezelése

Amikor egy Azure App Service-mobil háttérrendszer hívása, a befejezési blokk tartalmaz egy paramétert. `NSError` Hiba esetén ez a paraméter nem nulla. A kódban ellenőrizze ezt a paramétert, és szükség szerint kezelje a hibát, amint azt az előző kódrészletek is mutatják.

A [`<WindowsAzureMobileServices/MSError.h>`][6] fájl határozza meg `MSErrorResponseKey` `MSErrorRequestKey`az `MSErrorServerItemKey`állandókat , és a . A hibával kapcsolatos további adatok:

**C célkitűzés**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Gyors**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ezenkívül a fájl minden hibakódhoz állandókat határoz meg:

**C célkitűzés**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Gyors**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="how-to-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Útmutató: Felhasználók hitelesítése az Active Directory hitelesítési tárral

Az Active Directory hitelesítési könyvtár (ADAL) segítségével az Azure Active Directory használatával felhasználókat jelentkezhet be az alkalmazásba. Az SDK-t használó ügyfélfolyamat-hitelesítés előnyösebb, mint a `loginWithProvider:completion:` metódus használata.  Az ügyfélfolyamat-hitelesítés natívabb felhasználói környezetet biztosít, és további testreszabást tesz lehetővé.

1. Konfigurálja a mobilalkalmazás-háttérrendszert az AAD-bejelentkezéshez az [App Service konfigurálása][7] az Active Directory bejelentkezési oktatóanyagához című oktatóanyag követésével. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának választható lépését végezze el. IOS esetén azt javasoljuk, hogy az átirányítási URI az űrlapból áll. `<app-scheme>://<bundle-id>` További információt az [ADAL iOS rövid útmutatója tartalmaz.][8]
2. Telepítse az ADAL-t a Cocoapods segítségével. A Podfile fájl szerkesztése a következő definícióval, a **YOUR-PROJECT** helyett az Xcode projekt nevével:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   és a Pod:

        pod 'ADALiOS'

3. A terminál használatával `pod install` futtassa a projektet tartalmazó könyvtárból, majd nyissa meg a létrehozott Xcode munkaterületet (ne a projektet).
4. Adja hozzá a következő kódot az alkalmazáshoz, a használt nyelvnek megfelelően. Mindegyikben tegye meg ezeket a cseréket:

   * Cserélje **le az INSERT-AUTHORITY-HERE-t** annak a bérlőnek a nevére, amelyben kiépítette az alkalmazást. A formátumnak https://login.microsoftonline.com/contoso.onmicrosoft.coma legyen . Ez az érték az Azure Active Directory tartománylapjáról másolható az [Azure Portalon.]
   * Cserélje **le az INSERT-RESOURCE-ID-HERE-t** a mobilalkalmazás-háttérrendszer ügyfélazonosítójára. Az ügyfélazonosítót az Azure Active **Directory beállításai** a **portálon** a Speciális lapon szerezheti be.
   * Cserélje **le az INSERT-CLIENT-ID-HERE-t** a natív ügyfélalkalmazásból másolt ügyfélazonosítóra.
   * Cserélje **le az INSERT-REDIRECT-URI-HERE-t** a webhely */.auth/login/done* végpontjára a HTTPS-séma használatával. Ennek az értéknek *https://contoso.azurewebsites.net/.auth/login/done*a hoz hasonlónak kell lennie.

**C célkitűzés**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Gyors**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="how-to-authenticate-users-with-the-facebook-sdk-for-ios"></a><a name="facebook-sdk"></a>Hogyan: A felhasználók hitelesítése az iOS-es Facebook SDK-val

Az iOS-es Facebook SDK segítségével a Facebook segítségével jelentkezhetsz be a felhasználókba az alkalmazásodba a Facebook használatával.  Az ügyfélfolyamat-hitelesítés használata előnyösebb, mint a `loginWithProvider:completion:` módszer használata.  Az ügyfélfolyamat-hitelesítés natívabb felhasználói környezetet biztosít, és további testreszabást tesz lehetővé.

1. Konfigurálja a mobilalkalmazás háttérrendszerét a Facebook bejelentkezéséhez az App Service beállítása a [Facebook bejelentkezési oktatóanyagához.][9]
2. Telepítsd az iOS-es Facebook SDK-t az [iOS-hez tervezett Facebook SDK – Első lépések][10] dokumentációjának követésével. Ahelyett, hogy létrehozna egy alkalmazást, hozzáadhatja az iOS-platformot a meglévő regisztrációjához.
3. A Facebook dokumentációja tartalmaz néhány C objektív kódot az alkalmazásmeghatalmazottban. Ha **Swiftet**használ, az AppDelegate.swift következő fordításait használhatja:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. A projekthez `FBSDKCoreKit.framework` való hozzáadás mellett ugyanilyen módon is hozzáadhat egy hivatkozást. `FBSDKLoginKit.framework`
5. Adja hozzá a következő kódot az alkalmazáshoz, a használt nyelvnek megfelelően.

    **C célkitűzés**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Gyors**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-twitter-fabric-for-ios"></a><a name="twitter-fabric"></a>Hogyan: Hitelesítse a felhasználókat a Twitter Fabric for iOS-val

Az iOS Fabric segítségével a Twitter használatával bejelentkezhet a felhasználókaz alkalmazásba. Az ügyfélfolyam-hitelesítés előnyösebb `loginWithProvider:completion:` a módszer használata esetén, mivel natívabb felhasználói környezetet biztosít, és további testreszabást tesz lehetővé.

1. Konfigurálja a mobilalkalmazás háttér-Twitter bejelentkezési következő [az App Service for Twitter bejelentkezési](../app-service/configure-authentication-provider-twitter.md) tutorial.
2. Fabric hozzáadása a projekthez az [iOS-alapú fabric] – első lépések dokumentációjának követésével és a TwitterKit beállításával.

   > [!NOTE]
   > Alapértelmezés szerint a Fabric létrehoz egy Twitter alkalmazást az Ön számára. Elkerülheti egy alkalmazás létrehozását, ha regisztrálja a korábban létrehozott fogyasztói kulcsot és fogyasztói titkos kulcsot a következő kódrészletek használatával.    Azt is megteheti, hogy lecseréli az App Service-nek megadott fogyasztói kulcs- és fogyasztói titkos értékeket a Fabric irányítópultján látható [értékekre.] Ha ezt a lehetőséget választja, ügyeljen arra, hogy a visszahívás `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`iURL-címét egy helyőrző értékre állítsa be, például .

    Ha úgy dönt, hogy a korábban létrehozott titkos kulcsokat használja, adja hozzá a következő kódot az alkalmazásmeghatalmazotthoz:

    **C célkitűzés**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Gyors**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Adja hozzá a következő kódot az alkalmazáshoz, a használt nyelvnek megfelelően.

    **C célkitűzés**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Gyors**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-the-google-sign-in-sdk-for-ios"></a><a name="google-sdk"></a>Útmutató: A felhasználók hitelesítése az iOS-alapú Google Bejelentkezési SDK-val

Az iOS-es Google Bejelentkezési SDK segítségével Google-fiókkal jelentkezhet be a felhasználókaz alkalmazásba.  A Google nemrég bejelentette, hogy módosítja az OAuth biztonsági irányelveit.  Ezek az irányelvmódosítások a jövőben a Google SDK használatát igénylik.

1. Konfigurálja a mobilalkalmazás-háttérrendszert a Google bejelentkezéséhez az [App Service konfigurálása a Google bejelentkezési](../app-service/configure-authentication-provider-google.md) útmutatójában.
2. Telepítse az iOS-es Google SDK-t az [iOS-alapú Google-bejelentkezés](https://developers.google.com/identity/sign-in/ios/start-integrating) – A dokumentáció integrálásának megkezdése. Kihagyhatja a "Hitelesítés háttérkiszolgálóval" című szakaszt.
3. Adja hozzá a következőket `signIn:didSignInForUser:withError:` a meghatalmazott metódusához a használt nyelvnek megfelelően.

    **C célkitűzés**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Gyors**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Győződjön meg arról `application:didFinishLaunchingWithOptions:` is, hogy az alkalmazás meghatalmazottja a következőket is hozzáadja, és a "SERVER_CLIENT_ID" helyett ugyanazzal az azonosítóval kell leváltania, mint amelyet az 1.

    **C célkitűzés**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Gyors**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adja hozzá a következő kódot az alkalmazáshoz egy `GIDSignInUIDelegate` UIViewController alkalmazásban, amely a protokollt a használt nyelvnek megfelelően valósítja meg.  Ki jelentkezett, mielőtt újra bejelentkezne, és bár nem kell újra megadnia a hitelesítő adatait, megjelenik egy jóváhagyási párbeszédpanel.  Ezt a metódust csak akkor hívja meg, ha a munkamenet-jogkivonat lejárt.

   **C célkitűzés**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Gyors**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Az Azure mobilalkalmazásokkal való gyors indítás]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure-portál]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dinamikus séma]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Szövet műszerfal]: https://www.fabric.io/home
[Fabric iOS-hez – első lépések]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
