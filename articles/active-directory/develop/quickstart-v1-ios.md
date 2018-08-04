---
title: Ismerkedés az Azure AD iOS |} A Microsoft Docs
description: Hogyan hozhat létre egy iOS-alkalmazás, amely integrálható az Azure AD-be- és a hívások Azure AD OAuth használatával védett API-k.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: jmprieur
ms.openlocfilehash: cc85dbe4bc1d207f18381e2a6d85461db229d6ea
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506597"
---
# <a name="azure-ad-ios-getting-started"></a>Ismerkedés az Azure AD iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Az Azure Active Directory (Azure AD) az Active Directory Authentication Library vagy adal-KÓDTÁRRAL biztosít a védett erőforrásokhoz kell hozzáférniük iOS-ügyfelek esetében. Adal-t leegyszerűsíti a folyamatot, az alkalmazás által használt hozzáférési tokenek beszerzése érdekében. Bemutatása, mennyire egyszerű is, ebben a cikkben azt egy Objective-C feladatlista-alkalmazás létrehozása, amely:

* Lekérdezi hozzáférési jogkivonatokat használatával az Azure AD Graph API hívásakor a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* A felhasználók számára egy adott aliasú könyvtár keres.

A teljes, működő alkalmazást készíthet, kell tennie:

1. Regisztrálja az alkalmazást az Azure ad-ben.
2. Telepítse és konfigurálja az adal-t.
3. Adal-t használó tokenekhez Azure AD-ből való.

Első lépésként [töltse le az alkalmazás skeleton](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Emellett ahol felhasználók létrehozása és regisztrálni egy alkalmazást az Azure AD-bérlővel. Ha még nem rendelkezik egy bérlő [megtudhatja, hogyan tehet szert egy](quickstart-create-new-tenant.md).


> [!TIP]
> Próbálja ki az előzetes verziója az új [fejlesztői portál](https://identity.microsoft.com/Docs/iOS) , amely segít helyezheti üzembe csupán pár perc alatt az Azure AD-val. A fejlesztői portál végigvezeti Önt regisztrálja az alkalmazást, és Azure AD integrálása a kódot. Amikor elkészült, rendelkezni fog egy egyszerű alkalmazás, amely hitelesítheti a felhasználókat a bérlőben, és a egy háttérrendszer, amely fogadni, és -ellenőrzéshez. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Határozza meg, mely az átirányítási URI-ja iOS-hez
Az alkalmazások biztonságos kezdőkönyvtára bizonyos egyszeri bejelentkezési forgatókönyvek, létre kell hoznia egy *átirányítási URI* adott formátumban. Átirányítási URI-t segítségével győződjön meg arról, hogy a jogkivonatok térjen vissza a megfelelő alkalmazáshoz, amely kéri őket.


Az IOS-es átirányítási URI formátuma:

```
<app-scheme>://<bundle-id>
```

* **alkalmazás-séma** – ez regisztrálva van az XCode-projektben. Fontos, hogy más alkalmazások hívhatják meg. Ez alatt az info.plist fájl -> található URL-cím-típusok-azonosító URL-címe >. Ha még nem rendelkezik egy vagy több konfigurálva kell létrehoznia egyet.
* **Csomagazonosító** – Ez az a csomagazonosítót az XCode projekt beállításaiban "azonosító" alatt található.

Ez a rövid útmutató a kód példa: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. A DirectorySearcher alkalmazás regisztrálása
Az alkalmazás beállítása a jogkivonatok lekérésére, akkor először kell az Azure AD-bérlő regisztrálásához, és engedélyezi azt az Azure AD Graph API eléréséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiók. Alatt a **Directory** menüben válassza ki az Active Directory-bérlő kívánja az alkalmazás regisztrálásához.
3. Kattintson a **minden szolgáltatás** a bal oldali navigációs panelen, és válassza ki a **Azure Active Directory**.
4. Kattintson a **alkalmazásregisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat követve hozzon létre egy új **natív ügyfélalkalmazás**.
  * A **neve** az alkalmazás ismerteti a az alkalmazást a végfelhasználók számára.
  * A **átirányítási URI-t** sémát és karakterlánc kombinációja, amely az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg egy értéket, amely az alkalmazás és a korábbi átirányítási URI-t adatai alapján.
6. A regisztráció elvégzése után az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Ez az érték kell a következő szakaszokban, ezért másolja ki az alkalmazások lapon.
7. Az a **beállítások** lapon jelölje be **szükséges engedélyek** , majd **Hozzáadás**. Válassza ki **Microsoft Graph** , az API-t, majd adja hozzá a **címtáradatok olvasása** engedélyt a **delegált engedélyek**. Az alkalmazás a felhasználók számára az Azure AD Graph API lekérdezéséhez állít be.

## <a name="3-install-and-configure-adal"></a>3. Telepítse és konfigurálja az adal-t
Most, hogy egy alkalmazás az Azure ad-ben, telepítheti az adal-t és az identitással kapcsolatos kód írása. Az ADAL-kommunikálni az Azure ad-ben meg kell adnia azt az alkalmazás regisztrációs bizonyos információkat.

1. Első lépésként adja hozzá adal-t a DirectorySearcher projekthez a CocoaPods segítségével.

    ```
    $ vi Podfile
    ```
2. Adja hozzá a következőt a pod-fájlhoz:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Most már betöltheti a podfile CocoaPods segítségével. Ez a lépés létrehoz egy új XCode-munkaterületet, amely betölti.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. A gyorsindítási projekt, nyissa meg a plist-fájlt `settings.plist`. Cserélje le az értékeket az elemek a szakaszban az Azure Portalon megadott értékeknek megfelelően. A kód hivatkozik az ezeket az értékeket, amikor az adal-t használ.
  * A `tenant` a tartomány az Azure AD-bérlő, például contoso.onmicrosoft.com.
  * A `clientId` a portálról kimásolt az alkalmazás ügyfél-azonosítója.
  * A `redirectUri` a portál regisztrált átirányítási URL-címe.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. Adal-t használó tokenekhez Azure AD-ből való
Az alapelv ADAL mögött, hogy minden alkalommal, amikor az alkalmazás kell egy hozzáférési jogkivonatot, egyszerűen meghívja a completionBlock `+(void) getToken : `, ADAL végzi. 

1. Az a `QuickStart` projektben nyissa meg `GraphAPICaller.m` , és keresse meg a `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` tetejénél található megjegyzést. Ez az, ahol adja át az adal-t a koordináták a CompletionBlock kommunikálni az Azure ad-ben, és mondja el, hogyan jogkivonatok gyorsítótárazása révén.

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

2. Most meg kell használni ezt a jogkivonatot a gráf felhasználók kereséséhez. Keresse meg a `// TODO: implement SearchUsersList` megjegyzést. Ez a módszer egy GET kéréssel teszi az Azure AD Graph API lekérdezéséhez, a felhasználók számára, akiknek UPN kezdődik-e a megadott keresési kifejezés. Az Azure AD Graph API lekérdezéséhez, meg kell adni a-access_token a `Authorization` a kérelem fejlécében. Ez az, ahol ADAL érhető el.

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


3. Ha az alkalmazás tokent kér meghívásával `getToken(...)`, ADAL próbál egy token vissza a felhasználói hitelesítő adatok kérése nélkül. Ha adal-t, hogy a felhasználónak kell-e jogkivonat beszerzéséhez jelentkezzen be, azt fog megjelenik egy párbeszédpanel a bejelentkezéshez, a felhasználó hitelesítő adatainak gyűjtése, illetve visszalép jogkivonat sikeres hitelesítés után. Ha adal-t nem adott vissza egy token bármilyen okból, jelez egy `AdalException`.

> [!Note] 
> A `AuthenticationResult` az objektum tartalmaz egy `tokenCacheStoreItem` objektum, amely a-alkalmazásoknak szükségük lehet, hogy az adatok összegyűjtése használható. A rövid útmutatóban `tokenCacheStoreItem` határozza meg, ha a hitelesítés már megtörtént.
>
>

## <a name="5-build-and-run-the-application"></a>5. Az alkalmazás fordítása és futtatása
Gratulálunk! Most már rendelkezik egy működő iOS-alkalmazást, hitelesítheti a felhasználókat, biztonságos webes API-k hívása az OAuth 2.0 használatával, és alapvető információkat szeretne a felhasználó beolvasása. Ha még nem tette, most már az az idő, az egyes felhasználók a bérlő feltölti. Indítsa el a rövid útmutató alkalmazást, és jelentkezzen be egy ezen felhasználók. Keresse meg a más felhasználók, az egyszerű felhasználónév alapján. Zárja be az alkalmazást, majd indítsa el újra. Figyelje meg, hogy a felhasználói munkamenet továbbra is változatlan marad.

Adal-t megkönnyíti az alkalmazásba beépíteni az összes alábbi általános identitás-szolgáltatások. Ez gondoskodik dirty munka, például a gyorsítótár kezeléséhez, OAuth protokoll támogatása, a felhasználói felületen keresztül bejelentkezni, a felhasználó jelentő és frissítése lejárt jogkivonatok. Feltétlenül szükséges tudnia, csak egyetlen API hívással `getToken`.

Referenciaként az elkészült mintát (a konfigurációs értékek) nélkül a megadott [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). 

## <a name="next-steps"></a>További lépések
Most már továbbléphet további forgatókönyvek. Előfordulhat, hogy szeretné próbálni:

* [Biztonságos Node.JS webes API-k az Azure ad-vel](active-directory-devquickstarts-webapi-nodejs.md)
* Ismerje meg, [IOS-es adal-t használó alkalmazások közötti SSO engedélyezése](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

