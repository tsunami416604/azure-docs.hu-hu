---
title: Hogyan használata IOS-hez készült Azure Mobile Apps SDK
description: Hogyan használata IOS-hez készült Azure Mobile Apps SDK
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 63dd283605553297a7dc8feab90c8bcbd716d5de
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
ms.locfileid: "26660141"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Hogyan használja iOS Azure Mobile Apps készült ügyféloldali kódtára
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ez az útmutató útmutatást ad teszi a végrehajtását szolgáltatást a legújabb használó általános forgatókönyvhöz [Azure Mobile Apps iOS SDK][1]. Ha most ismerkedik az Azure Mobile Apps, először végezzen [Azure Mobile Apps gyors üzembe helyezés] -háttéralkalmazás létrehozása, hozzon létre egy táblát, és töltse le egy előre elkészített iOS Xcode projekt. Az útmutató azt összpontosítanak az ügyféloldali iOS SDK is. A kiszolgálóoldali SDK a háttérkiszolgáló kapcsolatos további tudnivalókért tekintse meg a kiszolgáló SDK HOWTOs.

## <a name="reference-documentation"></a>Segédanyagok
Az iOS-ügyfél SDK dokumentációját a következő helyen található: [Azure Mobile Apps iOS ügyfél hivatkozási][2].

## <a name="supported-platforms"></a>A támogatott platformok
Az iOS SDK támogatja Objective-C projektek, a Swift 2.2 projektek és a Swift 2.3-projektek az iOS 8.0-s vagy újabb verziójú.

A "server-folyamat" hitelesítési bemutatott felhasználói felülete a webes nézet használja.  Ha az eszköz nem képesek egy webes nézet felhasználói felület, akkor egy másik hitelesítési módszer szükséges, amely a termék hatókörén kívül esik.  
Ez az SDK így nem alkalmas figyelési típusú vagy hasonló módon korlátozott eszközök.

## <a name="Setup"></a>A telepítő és Előfeltételek
Ez az útmutató feltételezi, hogy létrehozott egy táblát a háttérkiszolgálón. Ez az útmutató feltételezi, hogy rendelkezik-e a tábla a táblák ugyanazon séma ezen oktatóprogram a. Ez az útmutató feltételezi, hogy a kódban hivatkozik `MicrosoftAzureMobile.framework` , majd importálja `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Hogyan: ügyfél létrehozása
Az Azure Mobile Apps-háttéralkalmazás a projekt eléréséhez hozzon létre egy `MSClient`. Cserélje le `AppUrl` az alkalmazás URL-címmel. Hagyhatja `gatewayURLString` és `applicationKey` üres. Ha állít be egy átjárót a hitelesítéshez, feltöltése `gatewayURLString` a átjáró URL-címet.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Hogyan: táblahivatkozás létrehozása
Az adatok elérése vagy frissítése érdekében hozzon létre a háttértáblára mutató hivatkozást. A `TodoItem` helyére írja be a tábla nevét.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Útmutató: adatok lekérdezése
Adatbázis-lekérdezés létrehozásához lekérdezni a `MSTable` objektum. Az alábbi lekérdezés lekérdezi a elemek `TodoItem` és a szöveg, az egyes elemek naplózza.

**Objective-C**:

```
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

```
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

## <a name="filtering"></a>Hogyan: szűrő adatokat adott vissza.
Az eredmények szűréséhez többféleképpen érhető el.

Szűrés predikátum, használja az `NSPredicate` és `readWithPredicate`. A szűrők csak hiányos Todo elemeket található adatokat adott vissza.

**Objective-C**:

```
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

```
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

## <a name="query-object"></a>Hogyan: MSQuery használata
Egy összetett lekérdezés (beleértve a rendezés és lapozáshoz), hozzon létre egy `MSQuery` objektum, közvetlenül vagy predikátum használatával:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`lehetővé teszi, hogy több lekérdezés viselkedések szabályozhatja.

* Adja meg az eredmények sorrendje
* Térjen vissza mezőket korlátozása
* Vissza rekordok számának korlátozása
* Adja meg a számuk adott válasz
* Adja meg az egyéni lekérdezési karakterlánc paramétert kérelem
* További funkciók alkalmazása

Hajtsa végre egy `MSQuery` meghívásával lekérdezés `readWithCompletion` az objektumon.

## <a name="sorting"></a>Hogyan: MSQuery az adatok rendezése
Az eredmények rendezéséhez nézzük például. Mező "text" növekvő, azután a "teljes" csökkenő rendezéséhez meghívása `MSQuery` , például így:

**Objective-C**:

```
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

```
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


## <a name="selecting"></a><a name="parameters"></a>Hogyan: mezők korlátjának növelését, és bontsa ki a lekérdezési karakterlánc paramétereket MSQuery
A lekérdezés által visszaadott mezők korlátozásához adja meg a mezők nevét a **selectFields** tulajdonság. Ebben a példában csak a szöveg és a befejezett mezők adja vissza:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**SWIFT**:

```
query.selectFields = ["text", "complete"]
```

A kiszolgálói kérelem közé tartoznak további lekérdezési karakterlánc paraméter (például azért, mert egy egyéni kiszolgálóoldali parancsfájl használja őket), feltöltéséhez `query.parameters` , például így:

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**SWIFT**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Hogyan: oldalméret konfigurálása
Az Azure Mobile Apps a lap mérete határozza meg, hogy a háttér tábla egyszerre vannak lekért rekordok száma. Hívása `pull` adatokat szeretné majd a batch-adatokat, a lap mérete alapján, amíg nincsenek további rekordok való lekérésére.

Konfigurálhatja a lap mérete segítségével lehet **MSPullSettings** alább látható módon. Az alapértelmezett oldal mérete 50, és az alábbi példa 3 módosítja azt.

Konfigurálhatja a különböző méretet a jobb teljesítmény érdekében. Ha kis rekordok nagy száma, a magas oldalméret csökkenti a kiszolgáló üzenetváltások számát.

Ez a beállítás csak az ügyféloldali oldalméret szabályozza. Ha az ügyfél egy nagyobb oldalméret, mint a Mobile Apps-háttéralkalmazás támogatja, a lapméretnél tárfiókonként a maximumot, a háttér támogatására van konfigurálva.

Ez a beállítás akkor is a *szám* az rekordok, nem a *bájtméretnek*.

Ha a ügyfél méretének növeléséhez is növelje az ajánlott méretet a kiszolgálón. Lásd: ["hogyan: a tábla a lapozófájl méretének módosítása"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ennek lépéseit.

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**SWIFT**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Útmutató: adatok beszúrása
Új tábla sor beszúrására, hozzon létre egy `NSDictionary` és invoke `table insert`. Ha [dinamikus séma] van engedélyezve, az Azure App Service mobil-háttéralkalmazást automatikusan hoz létre új oszlopok alapján a `NSDictionary`.

Ha `id` van nincs megadva, a háttér automatikusan hoz létre egy új egyedi azonosítót. Adja meg a saját `id` használni e-mail címek, felhasználónevek, vagy a saját egyéni értékeinek azonosítóját. Saját azonosító megadása is megkönnyítik illesztések és üzleti célú adatbázis programot.

A `result` tartalmazza az új elemet beszúrt. Attól függően, hogy a kiszolgáló logika mellette további vagy módosított adatok milyen lett átadva a kiszolgáló képest.

**Objective-C**:

```
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

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Útmutató: adatok módosítása
Egy meglévő sor frissítése, módosítsa egy elemet, és a hívás `update`:

**Objective-C**:

```
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

```
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

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Legalább a `id` attribútumot úgy kell beállítani, amikor frissíteni.

## <a name="deleting"></a>Útmutató: adatok törlése
Egy elem törléséhez meghívása `delete` a cikket:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
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

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Legalább a `id` attribútumot úgy kell beállítani, ha törli az elvégzése.

## <a name="customapi"></a>Útmutató: egyéni API hívása
Egy egyéni API-val olyan háttér funkciót is elérhetővé teheti. Egy tábla művelet hozzárendelése nem tartalmaz. Nem csak akkor kapnak az üzenetkezelési teljesebb körű vezérlése, még akkor is is fejlécek olvasási vagy állították be, és módosítsa a válasz törzsében formátuma. Egy egyéni API létrehozása a háttérkiszolgálón, olvassa el [egyéni API-k](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Egy egyéni API hívása, hívja meg a `MSClient.invokeAPI`. A kérelem és válasz tartalom JSON tekintendők. Más media tárolótípus [használja más `invokeAPI` ] [ 5].  Annak a `GET` ahelyett, hogy kérjen egy `POST` kérelmezéséhez set paraméter `HTTPMethod` való `"GET"` és paraméter `body` való `nil` (mivel a GET-kérésekhez nincs az üzenet törzse.) Ha az egyéni API támogatja az egyéb HTTP-műveletek, módosítsa `HTTPMethod` megfelelően.

**Objective-C**:

```
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

```
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

## <a name="templates"></a>Útmutató: regisztráció leküldéses sablonok platformfüggetlen értesítések küldéséhez
Sablonok regisztrálásához adja át a sablon is van a **client.push registerDeviceToken** ügyfélalkalmazás metódust.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

A sablonok NSDictionary típusú, és tartalmazhat több sablon a következő formátumban:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**SWIFT**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Az összes címke a kérelemből a biztonság üres karaktert törölni.  Címkék hozzáadása a telepítésekkel és sablonok telepítések belül, lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a][4].  A regisztrált sablonokkal értesítések küldéséhez, együttműködve [Notification Hubs API-k][3].

## <a name="errors"></a>Hogyan: hibák kezelésének
Az Azure App Service mobil-háttéralkalmazást hívásakor a befejezési blokk tartalmaz egy `NSError` paraméter. Ha hiba lép fel, a paraméter nem üres. A kódban ellenőrizze a paraméter és a hiba, ha szükséges, kezelni, ahogyan az a megelőző kódrészletek.

A fájl [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] határozza meg a állandók `MSErrorResponseKey`, `MSErrorRequestKey`, és `MSErrorServerItemKey`. A hibával kapcsolatos további adatok eléréséhez:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**SWIFT**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Emellett a fájl meghatározza, hogy minden hibakód állandókat:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**SWIFT**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Útmutató: az Active Directory Authentication Library a felhasználók hitelesítéséhez
Az Active Directory Authentication Library (ADAL) segítségével a felhasználók jelentkezzen be az alkalmazás Azure Active Directory használatával. Ügyfél adatfolyam-hitelesítéshez az identitásszolgáltató SDK használata helyett a `loginWithProvider:completion:` metódust.  Ügyfél folyamata hitelesítést biztosít több natív UX abba, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás számára az AAD-bejelentkezés konfigurálása a következő a [App Service konfigurálása az Active Directory bejelentkezési] [ 7] oktatóanyag. Ügyeljen arra, hogy a natív ügyfélalkalmazás regisztrációján választható lépés elvégzése után. Az iOS, azt javasoljuk, hogy az átirányítási URI megadása az űrlap `<app-scheme>://<bundle-id>`. További információkért lásd: a [ADAL iOS gyors üzembe helyezés][8].
2. Telepítse a Cocoapods segítségével adal-t. A következő definícióját tartalmazza a Podfile szerkesztése cseréje **YOUR-projekt** az Xcode-projektjéhez nevét:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   és fogyasztanak:

        pod 'ADALiOS'
3. Futtassa a terminál használatával `pod install` a könyvtárból, amely tartalmazza a projekthez, és nyissa meg a létrehozott Xcode-munkaterületet (ne a projektre).
4. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően. Minden ellenőrizze a cserét:

   * Cserélje le **INSERT-SZOLGÁLTATÓ-Itt** nevű, a bérlő, amelyben az alkalmazás üzembe. A következő formátumban kell megadni https://login.microsoftonline.com/contoso.onmicrosoft.com. Ez az érték lehet másolni az Azure Active Directory tartományi lapján a [Azure-portálon].
   * Cserélje le **INSERT-erőforrás-azonosító-Itt** az ügyfél-azonosító a mobil-háttéralkalmazás számára. Ezt úgy szerezheti be az ügyfél-azonosító a **speciális** lap **Azure Active Directory beállításai** a portálon.
   * Cserélje le **INSERT-ügyfél-azonosító-Itt** , az ügyfél-Azonosítót a natív ügyfélalkalmazás másolta.
   * Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel való */.auth/login/done* végpont, a HTTPS protokollt használ. Ez az érték a következőképpen kell kinéznie *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

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


**SWIFT**:

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

## <a name="facebook-sdk"></a>Útmutató: a Facebook SDK iOS-hez a felhasználók hitelesítéséhez
A Facebook SDK iOS segítségével a felhasználók jelentkezzen be az alkalmazás Facebook használatával.  A folyamat ügyfél-hitelesítés használata használata helyett a `loginWithProvider:completion:` metódust.  Az ügyfél-hitelesítési folyamat több natív UX abba biztosít, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás a Facebook-bejelentkezés konfigurálása a következő a [App Service konfigurálása Facebook bejelentkezési] [ 9] oktatóanyag.
2. A Facebook SDK iOS-követve telepítse a [Facebook SDK iOS - első lépések] [ 10] dokumentációját. Alkalmazás létrehozása, helyett a meglévő regisztrációs az iOS platform adhat hozzá.
3. Az alkalmazás delegált néhány Objective-C kódjának Facebook tartozó dokumentáció tartalmazza. Ha használ **Swift**, a következő fordítások AppDelegate.swift használható:

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
4. Hozzáadásán `FBSDKCoreKit.framework` a projektbe egy hivatkozást is meg kell adni `FBSDKLoginKit.framework` azonos módon.
5. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően.

**Objective-C**:

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

**SWIFT**:

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

## <a name="twitter-fabric"></a>Útmutató: az IOS-es Twitter-hálóval felhasználók hitelesítéséhez
IOS-háló segítségével felhasználók jelentkezzen be az alkalmazás Twitter használatával. Ügyfél folyamata a hitelesítés az használata helyett a `loginWithProvider:completion:` metódus, mert több natív UX abba biztosít, és lehetővé teszi, hogy további testreszabási.

1. A mobil-háttéralkalmazás számára Twitter-bejelentkezés konfigurálása a következő a [App Service konfigurálása Twitter bejelentkezési](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) oktatóanyag.
2. Háló hozzáadása a projekthez követve a [(iOS) – első lépések a háló] dokumentáció és TwitterKit beállítása.

   > [!NOTE]
   > Alapértelmezés szerint háló létrehoz egy Twitter-alkalmazást. Elkerülheti, ha regisztrálja a fogyasztói kulcs és a fogyasztói titkos kulcsot, korábban létrehozott az alábbi kódrészleteket alkalmazások létrehozására.    Másik lehetőségként a fogyasztói kulcs és a fogyasztói titkos értékek, amelyek látható értékekkel biztosítanak az App Service lecserélheti a [háló irányítópult]. Ha ezt a lehetőséget választja, ügyeljen arra, hogy a visszahívási URL-Címének beállítása a helyőrző értékre, mint például `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Ha a korábban létrehozott titkos kulcsok használatát választja, adja hozzá a következő kódot az alkalmazás delegált:

    **Objective-C**:

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

    **SWIFT**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Adja hozzá a következő kódot az alkalmazás használ nyelvének megfelelően.

**Objective-C**:

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

**SWIFT**:

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

## <a name="google-sdk"></a>Útmutató: a Google-bejelentkezés SDK iOS-felhasználók hitelesítéséhez
A Google-bejelentkezés SDK iOS segítségével a felhasználók jelentkezzen be az alkalmazás a Google-fiók használatával.  Google nemrég jelentette be az OAuth-biztonsági házirendek módosításai.  A módosítások a házirend megköveteli a Google SDK a továbbiakban.

1. A mobil-háttéralkalmazás a Google-bejelentkezés konfigurálása a következő a [App Service konfigurálása Google bejelentkezési](../app-service/app-service-mobile-how-to-configure-google-authentication.md) oktatóanyag.
2. Követve telepítse a Google SDK iOS-hez a [Google bejelentkezhet az iOS - Start integrálása](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentációját. A "Hitelesítéséhez és a háttérkiszolgáló" szakaszt kihagyhatja.
3. Adja hozzá a következőket a delegált `signIn:didSignInForUser:withError:` módszert használ nyelvének megfelelően.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**SWIFT**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Győződjön meg arról is adja hozzá a következőt `application:didFinishLaunchingWithOptions:` a az alkalmazás delegált cseréje a "SERVER_CLIENT_ID" App Service konfigurálása az 1. lépésben használt ugyanazzal az azonosítóval.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **SWIFT**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Adja hozzá a következő kódot az alkalmazást egy UIViewController, amely megvalósítja a `GIDSignInUIDelegate` protokollt használ nyelvének megfelelően.  Be van jelentkezve mielőtt újra éppen bejelentkezett, és nem kell újra a hitelesítő adatainak megadását, bár látja-e a hozzájárulási párbeszédablak.  Ez a metódus csak hívható, amikor a munkamenet-token érvényessége lejárt.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **SWIFT**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

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
[Azure Mobile Apps gyors üzembe helyezés]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure-portálon]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dinamikus séma]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[háló irányítópult]: https://www.fabric.io/home
[(iOS) – első lépések a háló]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
