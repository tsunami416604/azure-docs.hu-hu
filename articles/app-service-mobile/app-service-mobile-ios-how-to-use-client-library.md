---
title: Hogyan használja iOS SDK és Azure Mobile Apps
description: Hogyan használja iOS SDK és Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b284b599c569fe1c492b28d09fbc62a9130e939e
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409309"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Hogyan lehet Azure Mobile Apps-Klienskódtárának használata iOS-re

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató bemutatja, hogy a legújabb használatával általános forgatókönyveinek végrehajtásával [Azure Mobile Apps iOS SDK][1]. Ha most ismerkedik az Azure Mobile Apps, először végezzen [Azure Mobile Apps alkalmazások gyors üzembe helyezési] egy háttérrendszer létrehozásához hozzon létre egy táblát, és töltse le egy előre elkészített iOS Xcode-projektben. Ebben az útmutatóban koncentrálunk az ügyféloldali iOS SDK-t. A háttérrendszer a kiszolgálóoldali SDK kapcsolatos további információkért lásd: a kiszolgáló SDK HOWTOs.

## <a name="reference-documentation"></a>Segédanyagok

A hivatkozás dokumentációja az IOS-es ügyfél SDK is található itt: [Az Azure Mobile Apps IOS-es ügyfél-hivatkozása][2].

## <a name="supported-platforms"></a>A támogatott platformok

Az iOS SDK IOS 8.0-s vagy újabb verziója támogatja az Objective-C-projektek, Swift 2.2 projektek és Swift 2.3 projektek.

A "server-folyamat" hitelesítési olyan WebView-t használja az itt bemutatott felhasználói felületén.  Ha az eszköz nem tudja mutatni a WebView felhasználói felületén, majd egy másik hitelesítési módszer megadása kötelező, amely a termék hatókörén kívül esik.  
Ez az SDK így nem alkalmas Watch-típusú vagy hasonló módon korlátozott eszközöket.

## <a name="Setup"></a>A telepítő és Előfeltételek

Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérrendszernek. Ez az útmutató feltételezi, hogy a tábla ezek az oktatóanyagok a táblákként ugyanazzal a sémával rendelkezik. Ez az útmutató feltételezi, hogy a kódban hivatkozik `MicrosoftAzureMobile.framework` és importálása `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>kézikönyv: Ügyfél létrehozása

Az Azure Mobile Apps háttéralkalmazásból a projekt eléréséhez hozzon létre egy `MSClient`. Cserélje le `AppUrl` az alkalmazás URL-címmel. Előfordulhat, hogy hagyja `gatewayURLString` és `applicationKey` üres. Ha beállít egy átjárót a hitelesítéshez, feltöltéséhez `gatewayURLString` az átjáró URL-címmel.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>kézikönyv: Tábla hivatkozás létrehozása

Az adatok elérése vagy frissítése érdekében hozzon létre a háttértáblára mutató hivatkozást. A `TodoItem` helyére írja be a tábla nevét.

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>kézikönyv: Adatok lekérdezése

Egy adatbázis-lekérdezés létrehozása, lekérdezése a `MSTable` objektum. Az alábbi lekérdezés lekéri a elemeinek az `TodoItem` és naplózza az egyes elemek a szöveget.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**SWIFT**:

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

## <a name="filtering"></a>kézikönyv: Szűrő adatokat adott vissza.

Szűrheti az eredményeket, számos elérhető lehetőség van.

A predikátum szűrni, használja az `NSPredicate` és `readWithPredicate`. A következő szűrőket adott vissza az adatokat csak hiányos Todo elemeket.

**Objective-C**:

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

**SWIFT**:

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

## <a name="query-object"></a>kézikönyv: MSQuery használata

Hajtsa végre az összetett lekérdezések (beleértve a rendezést és lapozást), hozzon létre egy `MSQuery` objektumot, vagy közvetlenül egy predikátum használatával:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` lehetővé teszi számos lekérdezési viselkedés szabályozására.

* Adja meg az eredmények sorrendje
* Korlátozza a visszaadandó mezők
* A visszaadandó rekordok számának korlátozása
* Adja meg az objektumok száma válasz
* Adja meg az egyéni lekérdezési karakterlánc paraméterei kérelem
* További funkciók alkalmazása

Hajtsa végre egy `MSQuery` meghívásával lekérdezés `readWithCompletion` az objektumon.

## <a name="sorting"></a>kézikönyv: MSQuery az adatok rendezése

Eredmények rendezéséhez lássunk erre egy példát. Mező 'text' növekvő, azután a "teljes" csökkenő sorrendben rendezni, meghívása `MSQuery` lépések szerint:

**Objective-C**:

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

**SWIFT**:

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

## <a name="selecting"></a><a name="parameters"></a>kézikönyv: Korlátozhatja a mezőket, és bontsa ki a lekérdezési karakterlánc paraméterei a MSQuery

A lekérdezés által visszaadott mezők számának korlátozásához adja meg a mezők nevei a **selectFields** tulajdonság. Ebben a példában csak a szöveg és a befejezett mezőket ad vissza:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**SWIFT**:

```swift
query.selectFields = ["text", "complete"]
```

További lekérdezési karakterlánc paraméterei közé tartozik a kiszolgálói kérelem, (például azért, mert egy egyéni kiszolgálóoldali parancsprogram használja őket), töltse fel `query.parameters` lépések szerint:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**SWIFT**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>kézikönyv: Oldalméret beállítása

Az Azure Mobile Apps az oldal méretét szabályozza, amely vannak a háttérrendszer táblákból egyszerre beolvasott rekordok száma. Hívás `pull` adatokat szeretne majd batch-adatokat, ezt oldal mérete alapján, addig, amíg hiba nem a lekérés további rekordok.

Egy oldal méretét a konfigurálása lehetséges **MSPullSettings** alább látható módon. Oldalméret alapértelmezés szerint 50, és az alábbi példa módosítja a 3.

Beállíthat, hogy egy másik oldal méretét a megfelelő teljesítmény biztosítása érdekében. Ha nagy számú kis adatfelderítési rekordok, a nagy lapméret csökkenti a kiszolgáló üzenetváltások számát.

Ez a beállítás csak az ügyféloldalon az oldal méretét szabályozza. Ha az ügyfél kéri a egy nagyobb, mint a Mobile Apps háttéralkalmazásból támogatja az oldal méretét, az oldalméret maximumon a legnagyobb a háttérrendszer támogatására van konfigurálva.

Ez a beállítás akkor is a *szám* az adatfelderítési rekordok, nem a *bájtméretnek párosnak*.

Ha növeli az ügyfél oldal méretét, az oldal méretét a kiszolgálón is növelni kell. Lásd: ["How to: A tábla lapozófájl méretének módosítása"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ennek lépéseit.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**SWIFT**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>kézikönyv: Adatok beszúrása

Hozzon létre egy új tábla sor beszúrásához a `NSDictionary` és meghívása `table insert`. Ha [a dinamikus sémák] van engedélyezve, az Azure App Service-mobilháttérmodul automatikusan előállítja az új oszlopok alapján a `NSDictionary`.

Ha `id` nincs megadva, a háttérben automatikusan létrehoz egy új egyedi. Adja meg a saját `id` e-mail-címek, felhasználóneveket, vagy saját egyéni értékeket-azonosító néven Megkönnyítése érdekében az biztosítása a saját azonosító előfordulhat, hogy illesztések és üzleti célú adatbázis logikát.

A `result` a beszúrt új elemet tartalmaz. Attól függően, a kiszolgáló logikai mi lett átadva a kiszolgálóhoz képest további vagy módosított adatok lehetnek.

**Objective-C**:

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

**SWIFT**:

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

## <a name="modifying"></a>kézikönyv: Adatok módosítása

Meglévő sor frissítése, módosítása egy elemet, és a hívás `update`:

**Objective-C**:

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

**SWIFT**:

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

Másik lehetőségként adja meg a Sorazonosító, és a frissített mező:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Legalább a `id` attribútumot kell beállítani, amikor frissítések.

## <a name="deleting"></a>kézikönyv: Adatok törlése

Egy elem törléséhez hívja `delete` a cikket:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Azt is megteheti törölje a Sorazonosító megadásával:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Legalább a `id` attribútumot kell beállítani, amikor az így törli.

## <a name="customapi"></a>kézikönyv: Egyéni API hívása

Egyéni API-val tehetők közzé olyan háttérrendszer-funkciót. Nem kell olyan művelet leképezése. Nem csak, így az jobban szabályozhatja az üzenetkezelési, is olvasási/készletet a fejlécek, és módosítsa a válasz törzse formátuma. További információt az egyéni API-k létrehozása a háttérkiszolgálón, [egyéni API-k](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Egyéni API hívása hívja `MSClient.invokeAPI`. A kérés- és tartalom JSON-fájlként kell kezelni. Egyéb adathordozó-típusok használandó [használja más `invokeAPI` ] [ 5].  Győződjön meg arról, hogy egy `GET` kérelem helyett egy `POST` kérelem, az adatkészlet-paraméternek `HTTPMethod` való `"GET"` és paraméter `body` való `nil` (mivel a GET-kérések nem rendelkezik az üzenet törzse.) Ha az egyéni API támogatja a más HTTP-műveletek, módosítsa `HTTPMethod` megfelelően.

**Objective-C**:

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

**SWIFT**:

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

## <a name="templates"></a>kézikönyv: Regisztráció leküldéses sablonok platformfüggetlen értesítések küldése

Sablonok regisztrálásához adja át a sablonok a **client.push registerDeviceToken** metódus az az ügyfélalkalmazás.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**SWIFT**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

A sablonok NSDictionary típusú, és tartalmazhat több sablon a következő formátumban:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**SWIFT**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Az összes címke a program eltávolítja a biztonság a kérelemből.  Címkék hozzáadása telepítések vagy sablonok telepítések belül, lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a][4].  A regisztrált sablonok használatával értesítéseket küldhet együttműködve [Notification Hubs API-k][3].

## <a name="errors"></a>kézikönyv: Hibáinak kezelése

Amikor egy Azure App Service-mobilháttérmodul hívja, a befejezési blokk tartalmaz egy `NSError` paraméter. Ha hiba történik, a paraméter nem üres. A kódban ellenőrizze ezt a paramétert és kezelni a hibát, ha szükséges, ahogyan az az előző kódrészleteket is.

A fájl [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] határozza meg az állandókat `MSErrorResponseKey`, `MSErrorRequestKey`, és `MSErrorServerItemKey`. A hibára vonatkozó további adatok lekérése:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**SWIFT**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Emellett a fájl minden egyes hibakód állandókat határozza meg:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**SWIFT**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>kézikönyv: Felhasználók hitelesítése az Active Directory Authentication Library az

Az Active Directory Authentication Library (ADAL) segítségével bejelentkezhetnek a felhasználók az alkalmazásokba az Azure Active Directoryval. Ügyfél folyamat hitelesítést egy identitásszolgáltató SDK használatával használata helyett a `loginWithProvider:completion:` metódust.  Client flow hitelesítést biztosít egy több natív UX működésével, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás az AAD-bejelentkezés konfigurálása a következő a [konfigurálása App Service-ben az Active Directory-bejelentkezés] [ 7] oktatóanyag. Ellenőrizze, hogy a natív ügyfélalkalmazás regisztrációja nem kötelező lépése. IOS-, azt javasoljuk, hogy az átirányítási URI-ja a következő formában `<app-scheme>://<bundle-id>`. További információkért lásd: a [ADAL iOS rövid][8].
2. Telepítse a Cocoapods segítségével adal-t. Szerkessze a következő definícióját tartalmazza a Podfile cseréje **YOUR-projekt** az Xcode-projektben nevére:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   és a Pod:

        pod 'ADALiOS'

3. A terminálban futtassa `pod install` a címtárból, amely tartalmazza a projekthez, és nyissa meg a generált Xcode-munkaterületet (ne a projektre).
4. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően. Az egyes győződjön meg arról, ezek cserét:

   * Cserélje le **INSERT-SZOLGÁLTATÓ – Itt** , amelyben az alkalmazás kiosztása a bérlő nevével. A formátum https://login.microsoftonline.com/contoso.onmicrosoft.com. Ez az érték lehet másolni az Azure Active Directory tartományi lapról a [Azure Portal].
   * Cserélje le **INSERT-erőforrás-azonosító – Itt** az ügyfél-Azonosítóját a mobile Apps-háttéralkalmazást. Az ügyfél-Azonosítót a szerezheti be a **speciális** lapjára **Azure Active Directory-beállítások** a portálon.
   * Cserélje le **INSERT-ügyfél-azonosító – Itt** és az ügyfél-Azonosítót a natív ügyfélalkalmazás fájlból kimásolt.
   * Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel */.auth/login/done* végpontról, a HTTPS-sémát. Ez az érték legyen hasonló *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

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

**SWIFT**:

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

## <a name="facebook-sdk"></a>kézikönyv: Az iOS-hez készült Facebook-SDK-val a felhasználók hitelesítése

A Facebook SDK IOS rendszerhez készült segítségével bejelentkezhetnek a felhasználók Facebook használatával, az alkalmazásba.  Az ügyfél-hitelesítési folyamat a következő használata helyett a `loginWithProvider:completion:` metódust.  Az ügyfél-hitelesítési folyamat több natív UX betekintést nyújt, és lehetővé teszi, hogy további testreszabási.

1. A mobile Apps-háttéralkalmazást, a Facebook-bejelentkezés konfigurálása a következő a [Facebook-bejelentkezés konfigurálása az App Service] [ 9] oktatóanyag.
2. Telepítse a Facebook SDK iOS rendszerhez az alábbi a [Facebook SDK (iOS) – első lépések a] [ 10] dokumentációját. Helyett az alkalmazások létrehozásának folyamatába, az iOS platform adhat hozzá a meglévő regisztrációt.
3. App Delegát néhány Objective-C kódjának a Facebook-dokumentáció tartalmazza. Ha használ **Swift**, AppDelegate.swift is használhatja a következő fordítása:

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
4. Hozzáadásán `FBSDKCoreKit.framework` a projekthez is fel kell vennie egy hivatkozást `FBSDKLoginKit.framework` megegyező módon.
5. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően.

    **Objective-C**:

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

    **SWIFT**:

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

## <a name="twitter-fabric"></a>kézikönyv: IOS-hez a Twitter-Fabric felhasználók hitelesítése

IOS-es Fabric segítségével bejelentkezhetnek a felhasználók az alkalmazásba Twitter. Client Flow hitelesítésre használata helyett a `loginWithProvider:completion:` módját, hogy több natív UX betekintést nyújt, és lehetővé teszi, hogy további testreszabási.

1. A mobile Apps-háttéralkalmazást, a Twitter-bejelentkezés konfigurálása a következő a [Twitter-bejelentkezés konfigurálása App Service-ben](../app-service/configure-authentication-provider-twitter.md) oktatóanyag.
2. A következő Fabric hozzáadása a projekthez a [IOS – első lépések a háló] dokumentáció és TwitterKit beállítása.

   > [!NOTE]
   > Alapértelmezés szerint a háló létrehoz egy Twitter-alkalmazás az Ön számára. Elkerülheti, hogy az alkalmazás létrehozása a fogyasztói kulcs és a fogyasztói titkos kulcs, korábban létrehozott az alábbi kódrészleteket regisztrálásával.    Másik megoldásként lecserélheti a fogyasztói kulcs és a fogyasztói titkos kulcs értékeit, Ön által megadott App Service-ben látható értékekkel a [Hálóállapot irányítópult]. Ha ezt a lehetőséget választja, ügyeljen arra, hogy például egy helyőrző értékre állítsa be a visszahívási URL-Címének `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Ha a korábban létrehozott titkos kulcsok használatát választja, adja meg a következő kódot, az alkalmazás delegált:

    **Objective-C**:

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

    **SWIFT**:

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

3. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően.

    **Objective-C**:

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

    **SWIFT**:

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

## <a name="google-sdk"></a>kézikönyv: A Google bejelentkezési SDK iOS-hez a felhasználók hitelesítése

A Google bejelentkezési SDK IOS rendszerhez készült segítségével bejelentkezhetnek a felhasználók az alkalmazásba a Google-fiók használatával.  Google megtudhat az OAuth-biztonsági házirendek módosításai.  A házirend-módosítások a jövőben a Google SDK használatára lesz szükség.

1. A mobil-háttéralkalmazás számára a Google-bejelentkezés konfigurálása a következő a [Google-bejelentkezés konfigurálása App Service-ben](../app-service/configure-authentication-provider-google.md) oktatóanyag.
2. Telepítse a Google SDK iOS rendszerhez az alábbi a [Google jelentkezzen be az iOS - integrálásának megkezdéséhez](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentációját. A "Hitelesítés a egy háttérkiszolgáló" szakaszt kihagyhatja.
3. Adja hozzá a következő a delegált `signIn:didSignInForUser:withError:` módszert használ nyelvének megfelelően.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **SWIFT**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Győződjön meg arról is adja hozzá a következőt `application:didFinishLaunchingWithOptions:` a az alkalmazás delegált, és cserélje le a "SERVER_CLIENT_ID" ugyanazzal az azonosítóval, amelyet az App Service konfigurálása az 1. lépésben használt.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **SWIFT**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Adja hozzá a következő kódot egy UIViewController, amely megvalósítja az alkalmazását a `GIDSignInUIDelegate` protokollt használ nyelvének megfelelően.  Mielőtt újra éppen bejelentkezett kijelentkeztetése, és nem kell újra adja meg hitelesítő adatait, de látni a beleegyezés párbeszédpanelen.  Ha a munkamenet-jogkivonat lejárt csak ez a metódus meghívható.

   **Objective-C**:

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

   **SWIFT**:

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
[Azure Mobile Apps alkalmazások gyors üzembe helyezési]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[A dinamikus sémák]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Hálóállapot irányítópult]: https://www.fabric.io/home
[IOS – első lépések a háló]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
