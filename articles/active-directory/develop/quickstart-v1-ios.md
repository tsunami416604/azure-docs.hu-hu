---
title: A bejelentkezéshez az Azure AD-vel integrálható, valamint védett API-kat meghívó iOS-alkalmazás létrehozása az OAuth 2.0 segítségével | Microsoft Docs
description: Ismerje meg, hogyan jelentkeztetheti be a felhasználókat és hívhatja meg a Microsoft Graph API-t saját iOS-alkalmazásából.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53efe44f17f174073ea5ed39f94c9dc8078a9779
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206769"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Gyors útmutató: A felhasználók és a Microsoft Graph API meghívása iOS-alkalmazások

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Az Azure Active Directory (Azure AD) az Active Directory-hitelesítési tár (ADAL) segítségével teszi lehetővé az iOS-ügyfelek számára a védett erőforrásokhoz való hozzáférést. Az ADAL leegyszerűsíti azt a folyamatot, amelyet az alkalmazás a hozzáférési jogkivonatok beszerzéséhez használ. 

Ez a rövid útmutató egy olyan Objective C nyelven írt feladatlista-alkalmazás létrehozását mutatja be, amely a következőket végzi el:

* Hozzáférési jogkivonatokat kér le az Azure AD Graph API meghívásához az OAuth 2.0 hitelesítési protokoll használatával
* Megkeres egy adott aliasszal rendelkező felhasználót egy címtárban

Egy teljes körű, működő alkalmazás létrehozásához az alábbiakat kell tennie:

1. Az alkalmazás regisztrálása az Azure AD-ben.
1. Az ADAL telepítése és konfigurálása
1. Jogkivonatok lekérése az Azure AD-ből az ADAL használatával.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként végre kell hajtania az alábbi lépéseket:

* [Töltse le az alkalmazás vázát](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) vagy [a kész mintát](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Rendelkeznie kell egy Azure AD-bérlővel, amelyben felhasználókat hozhat létre, és regisztrálhat egy alkalmazást. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

> [!TIP]
> Próbálja ki a [fejlesztői portált](https://identity.microsoft.com/Docs/iOS), amellyel pár perc alatt üzembe helyezheti az Azure AD-t. A fejlesztői portál végigvezeti az alkalmazás regisztrációjának és az Azure AD a kódba való integrálásának folyamatán. Amikor elkészült, rendelkezni fog egy egyszerű alkalmazással, amely el tudja végezni a felhasználók hitelesítését a bérlőn, valamint egy olyan háttérrendszerrel, amely képes a hozzáférési jogkivonatok fogadására és érvényesítésére.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>1. lépés: Határozza meg, mely az átirányítási URI-ja iOS-hez

Ahhoz, hogy alkalmazása bizonyos egyszeri bejelentkezéses forgatókönyvek esetében biztonságosan induljon, létre kell hoznia egy megadott formátumú *átirányítási URI-t*. Az átirányítási URI használatával biztosítható, hogy a jogkivonatokat pontosan az az alkalmazás kapja meg, amely kérte őket.

Az iOS a következő formátumot használja az átirányítási URI-khoz:

```
<app-scheme>://<bundle-id>
```

* **app-scheme** (alkalmazásséma) – Az XCode-projektben van regisztrálva, és ezzel intézhetnek hívást Ön felé más alkalmazások. Az alkalmazásséma az **Info.plist > URL-típusok > URL-azonosító** területen található meg. Ha még nem rendelkezik egy vagy több konfigurált alkalmazássémával, hozzon létre egyet.
* **bundle-id** (csomagazonosító) – Ez az XCode-projekt beállításainak **Identitás** területén található Csomagazonosító.

A rövid útmutatóban szereplő kódhoz kapcsolódó példa:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>2. lépés: A DirectorySearcher alkalmazás regisztrálása

Ahhoz, hogy az alkalmazás jogkivonatokat kérhessen le, regisztrálnia kell az Azure AD-bérlőjében, valamint engedélyt kell biztosítani neki az Azure AD Graph API eléréséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki fiókját a felső sávban. A **Címtár** lista alatt válassza ki azt az Active Directory-bérlőt, amelyben az alkalmazást regisztrálni kívánja.
3. A bal szélső navigációs panelen válassza a **Minden szolgáltatás**, majd az **Azure Active Directory** elemet.
4. Válassza az **Alkalmazásregisztrációk**, majd a **Hozzáadás** elemet.
5. Az utasításokat követve hozzon létre egy új**Natív** ügyfélalkalmazást.
    * A **név** az alkalmazás neve, amely a végfelhasználók számára ad leírást az alkalmazásról.
    * Az **Átirányítási URI** egy sémából és sztringből álló kombináció, amelyet az Azure AD jogkivonatválaszok visszaadására használ. Adjon meg egy alkalmazás-specifikus és az előző átirányítási URI-n alapuló értéket.
6. A regisztráció befejeztével az Azure AD egy egyedi alkalmazásazonosítót rendel hozzá az alkalmazáshoz. Erre az értékre szüksége lesz a következő szakaszokban, ezért másolja ki az alkalmazás lapjáról.
7. A**Beállítások** lapon válassza a **Szükséges engedélyek > Hozzáadás > Microsoft Graph** lehetőséget, majd a **Delegált engedélyek** területen adja hozzá a **Címtáradatok olvasása** engedélyt. Ez az engedély lehetővé teszi az alkalmazás számára a felhasználók lekérdezését az Azure AD Graph API-n keresztül.

## <a name="step-3-install-and-configure-adal"></a>3. lépés: Telepítse és konfigurálja az adal-t

Most, hogy már van egy alkalmazása az Azure AD-ben, telepítheti az ADAL-t, és megírhatja az identitással kapcsolatos kódját. Ahhoz, hogy az ADAL kommunikálhasson az Azure AD-vel, meg kell adnia bizonyos információkat az alkalmazásregisztrációról.

1. Először is adja hozzá az ADAL-t a DirectorySearcher projekthez a CocoaPods segítségével.

    ```
    $ vi Podfile
    ```
1. Adja hozzá a következőt a pod-fájlhoz:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

1. Töltse be a pod-fájlt a CocoaPods segítségével. Ez a lépés létrehoz egy új XCode-munkaterületet, amelyet betölthet.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. A QuickStart projektben nyissa meg a következő plist fájlt: `settings.plist`.
1. A szakaszban található elemek értékeit állítsa be úgy, hogy megegyezzenek az Azure Portalon megadott értékekkel. A kód ezekre az értékekre fog hivatkozni az ADAL használatakor.
    * A `tenant` az Azure AD-bérlő tartománya, pl. contoso.onmicrosoft.com.
    * A `clientId` az alkalmazás portálról kimásolt ügyfél-azonosítója.
    * A `redirectUri` a portálon regisztrált átirányítási URI.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>4. lépés: Adal-t használó tokenekhez Azure AD-ből való

Az ADAL mögött álló alapelv az, hogy valahányszor az alkalmazásnak szüksége van egy hozzáférési jogkivonatra, egyszerűen meghívja a `+(void) getToken : ` completionBlockot, a többit pedig az ADAL végzi el.

1. A `QuickStart` projektben a `GraphAPICaller.m` fájl megnyitását követően keresse meg a `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` megjegyzést a fájl elején.

    Itt adhatja át a CompletionBlock segítségével az ADAL-nak a koordinátákat az Azure AD-vel való kommunikációhoz és a jogkivonatok gyorsítótárazási módjának megadásához.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Erre a jogkivonatra lesz szüksége ahhoz, hogy felhasználókat kereshessen a gráfban. Keresse meg a `// TODO: implement SearchUsersList` megjegyzést. Ez a metódus egy GET kérést indít az Azure AD Graph API felé, amellyel lekérdezi azokat a felhasználókat, akiknek az egyszerű felhasználóneve az adott keresési kifejezéssel kezdődik. 

    Az Azure AD Graph API lekérdezéséhez a kérés `Authorization` fejlécében meg kell adni egy access_token (hozzáférési jogkivonat) elemet. Itt jön a képbe az ADAL.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
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

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

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
         }];

    }

    ```

3. Ha az alkalmazás a `getToken(...)` meghívásával jogkivonatot kér, az ADAL megpróbál visszaadni egyet anélkül, hogy a felhasználótól hitelesítő adatokat kérne. Ha az ADAL megállapítása szerint a felhasználónak be kell jelentkeznie ahhoz, hogy jogkivonatot kapjon, akkor megjelenít egy bejelentkezési párbeszédpanelt, begyűjti a felhasználó hitelesítő adatait, majd a sikeres azonosítás után visszaadja a jogkivonatot. Ha az ADAL bármilyen okból nem képes jogkivonatot visszaadni, egy `AdalException` kivételt jelez.

> [!NOTE]
> Az `AuthenticationResult` objektum tartalmaz egy `tokenCacheStoreItem` objektumot, amellyel összegyűjtheti az alkalmazás számára szükséges adatokat. A QuickStart alkalmazásban a `tokenCacheStoreItem` objektum határozza meg, hogy a hitelesítés már megtörtént-e.

## <a name="step-5-build-and-run-the-application"></a>5. lépés: Az alkalmazás fordítása és futtatása

Gratulálunk! Ezen a ponton rendelkezik egy működő iOS-alkalmazással, amely képes a felhasználók hitelesítésére és biztonságos webes API-k meghívására OAuth 2.0 segítségével, továbbá le tudja kérdezni a felhasználó alapvető információit is.

Ha eddig még nem tette meg, itt az ideje a bérlő néhány felhasználóval történő feltöltésének.

1. Indítsa el a QuickStart alkalmazást, majd lépjen be az egyik felhasználóval.
1. Keressen más felhasználókat az egyszerű felhasználónevük alapján.
1. Zárja be az alkalmazást, majd indítsa el újra. Megfigyelheti, hogy a felhasználói munkamenet nem változott meg.

Az ADAL segítségével ezek az alapvető identitáskezelési funkciók egyszerűen integrálhatók az alkalmazásba. Elvégzi Ön helyett az összes piszkos munkát: a gyorsítótár kezelését, az OAuth protokoll támogatását, a lejárt jogkivonatok frissítését és egy felhasználói felület biztosítását a felhasználói bejelentkezéshez. Valójában egyetlen API-hívás, a `getToken` ismeretére van csak szüksége.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként .zip-fájlban is letöltheti a [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)-ról.

## <a name="next-steps"></a>További lépések

Most már továbbléphet a további forgatókönyvekre. Javasoljuk, hogy a következőkkel folytassa:

* [Node.Js-alapú webes API biztonságossá tétele az Azure AD-vel](quickstart-v1-nodejs-webapi.md)
* Megtudhatja, [hogyan engedélyezheti az alkalmazások közötti egyszeri bejelentkezést az ADAL segítségével az iOS rendszeren](howto-v1-enable-sso-ios.md)  
