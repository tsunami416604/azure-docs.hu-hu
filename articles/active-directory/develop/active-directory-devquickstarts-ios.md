---
title: "Ismerkedés az Azure AD iOS |} Microsoft Docs"
description: "Hogyan hozhat létre, amely az Azure AD bejelentkezési és a hívások Azure AD számára az iOS-alkalmazás OAuth használatával védett API-k."
services: active-directory
documentationcenter: ios
author: brandwe
manager: mtillman
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/30/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 93df6f5e6c966fdf4909a4c21ebc36bff4283a59
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-ad-ios-getting-started"></a>Ismerkedés az Azure AD iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) biztosítja az Active Directory Authentication Library, vagy az adal-t, iOS-ügyfelek számára, amelyek védett erőforrások eléréséhez. ADAL egyszerűbben alkalmazása használja-e a hozzáférési tokenek beszerzése érdekében. Annak bemutatásához, hogyan könnyen van, a cikkben azt Objective C feladatlista alkalmazás létrehozásához, amely:

* Lekérdezi az Azure AD Graph API felület meghívásakor használatával jogkivonatainak hozzáférni a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Egy könyvtárat a felhasználók egy adott aliasnév keres.

A teljes működő alkalmazás létrehozásához kell:

1. Az alkalmazás regisztrálása az Azure ad-val.
2. Telepítse és konfigurálja az adal-t.
3. Adal-t használó tokenek lekérni az Azure AD.

A kezdéshez [töltse le az alkalmazás vázat](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Akkor is, amelyben felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő kell. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).


> [!TIP]
> Az új az előzetes kiadás kipróbálásához [fejlesztői portálján](https://identity.microsoft.com/Docs/iOS) , amely segít, amelyekből megismerheti az Azure AD csak néhány perc múlva. A fejlesztői portálján végigvezeti a regisztrálja az alkalmazást, és az Azure AD integrálása a kódot. Ha végzett, konfigurálnia kell egy egyszerű alkalmazást, amely képes hitelesíteni a felhasználók az Ön bérlőjében, és egy háttérhálózatként, hogy fogadni, és -ellenőrzéshez. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Határozza meg, milyen az átirányítási URI megadása iOS-hez
Biztonságosan indítsa el az alkalmazások bizonyos SSO forgatókönyvekben, létre kell hoznia egy *átirányítási URI* adott formátumban. Egy átirányítási URI-t annak biztosítására szolgál, hogy a jogkivonatok térjen vissza a megfelelő alkalmazás feltett számukra.


Az iOS egy átirányítási URI formátuma:

```
<app-scheme>://<bundle-id>
```

* **alkalmazás-séma** -ez regisztrálva van az XCode-projektjéhez. Fontos, hogy milyen más alkalmazások hívhatják meg. Ez az Info.plist -> található URL-cím típusú -> URL-cím azonosítója. Ha még nem rendelkezik legalább egy konfigurálni kell létrehoznia egyet.
* **a csomagazonosító** -Ez az a csomagazonosítót az XCode projekt beállításaiban "azonosító" alatt található.

A kód gyors üzembe helyezési példa: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. A DirectorySearcher alkalmazás regisztrálása
Az alkalmazás beállítása a jogkivonatok lekérésére, először kell regisztrálni az Azure AD-bérlő, és adja meg azt az engedélyt az Azure AD Graph API eléréséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki, hol szeretné az alkalmazás regisztrálása az Active Directory-bérlő.
3. Kattintson a **minden szolgáltatás** a bal oldali navigációs ablaktáblán, és válassza ki azt a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat, hozzon létre egy új **natív ügyfélalkalmazás**.
  * A **neve** , az alkalmazás írja le az alkalmazást a végfelhasználók számára.
  * A **átirányítási Uri-** protokollt és a karakterlánc kombinációját, amely az Azure AD token válaszok adja vissza.  Adjon meg egy értéket, amely az alkalmazásra vonatkoznak, és a korábbi átirányítási URI információkon alapul.
6. A regisztráció befejezését követően az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót.  Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás lapján.
7. A a **beállítások** lapon jelölje be **szükséges engedélyek** majd **hozzáadása**. Válassza ki **Microsoft Graph** , az API-t, majd adja hozzá a **címtáradatok olvasása** engedélyt a **delegált engedélyek**.  Az alkalmazás a felhasználók számára az Azure AD Graph API lekérdezni állít be.

## <a name="3-install-and-configure-adal"></a>3. Telepítse és konfigurálja az adal-t
Most, hogy az Azure AD-alkalmazás, telepítse az adal-t, és az identitás-kapcsolódó kód írása.  Az adal-t az Azure AD kommunikálni meg kell adnia azt bizonyos információkat az alkalmazás regisztrációját.

1. Kezdje az adal-t hozzáadása a DirectorySearcher projekt CocoaPods segítségével.

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

3. Most töltse be a podfile CocoaPods segítségével. Ebben a lépésben létrehoz egy új XCode-munkaterületet, hogy betöltve.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. A gyors üzembe helyezés projektben nyissa meg a plist-fájlt `settings.plist`.  Cserélje le az értékeket az elemek a szakaszban az Azure-portálon megadott értékeknek megfelelően. A kód hivatkozik ezeket az értékeket, minden alkalommal adal-t.
  * A `tenant` a tartományt az Azure AD-bérlőn, például a contoso.onmicrosoft.com.
  * A `clientId` a portálról másolt az alkalmazás ügyfél-azonosító.
  * A `redirectUri` az átirányítási URL-cím regisztrált a portálon.

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.    Adal-t használó tokenek lekérni az Azure AD
Az alapelv ADAL mögött, hogy a hozzáférési tokent kell, ha egyszerűen meghívja a completionBlock `+(void) getToken : `, és a többi adal-t.  

1. Az a `QuickStart` projektben nyissa meg `GraphAPICaller.m` , és keresse meg a `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` Megjegyzés felső részén.  Ez az adott át adal-t a koordináták a CompletionBlock, az Azure ad-val, és módon gyorsítótárazza a jogkivonatok keresztül.

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

2. Most létre kell a token használata a grafikonon felhasználók kereséséhez. Keresés a `// TODO: implement SearchUsersList` megjegyzés. Ez a módszer az Azure AD Graph API lekérdezéshez GET kérés teszi a felhasználók számára, amelyek egyszerű felhasználónév a megadott keresési kifejezés kezdődik.  Az Azure AD Graph API lekérdezni, meg kell adnia egy access_token a a `Authorization` a kérelem fejlécében. Ez az adal-t honnan.

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


3. Ha az alkalmazás kísérel meg jogkivonat meghívásával `getToken(...)`, adal-t próbálja elküldeni a jogkivonatot a felhasználói hitelesítő adatok kérése nélkül.  ADAL határozza meg, hogy a felhasználó bejelentkezhet a szolgáltatáshitelesítést egy token kell-e, ha azt fogja bejelentkezésnél párbeszédpanel megjelenítése, a felhasználói hitelesítő adatok összegyűjtése és sikeres hitelesítés után térjen vissza a jogkivonat.  Ha az adal-t nem tud visszatérni jogkivonat bármilyen okból, jelez egy `AdalException`.

> [!Note] 
> A `AuthenticationResult` objektum tartalmaz egy `tokenCacheStoreItem` objektum, amely a céghez, amely csak az alkalmazás akkor is használható. A gyorsindító `tokenCacheStoreItem` határozza meg, ha a hitelesítés már megtörtént.
>
>

## <a name="5-build-and-run-the-application"></a>5. Az alkalmazás fordítása és futtatása
Gratulálunk! Most már rendelkezik egy működő iOS-alkalmazást, amely hitelesíti a felhasználókat, biztonságos webes API-k hívására OAuth 2.0-s, és a felhasználó alapszintű adatainak beolvasása.  Ha még nem tette meg, most már az egyes felhasználóival a bérlő feltölti idő.  Indítsa el a gyorsindító alkalmazást, és jelentkezzen be valamelyik azoknak a felhasználóknak.  Az egyszerű Felhasználónevük alapján más felhasználók kereséséhez.  Zárja be az alkalmazást, majd indítsa el újból.  Figyelje meg, hogy a felhasználói munkamenet érintetlen marad.

Az adal TÁRAT megkönnyíti, hogy átfogó mindezeket a közös identitás funkciókat az alkalmazásba.  Azt gondoskodik a dirty munkát meg, mint a gyorsítótár-felügyelet OAuth protokoll támogatása, a felhasználó a felhasználói felületen való bejelentkezéshez, bemutató, és a jogkivonatok frissítése lejárt.  Biztosan tudni, hogy szüksége egy egyetlen API-hívással `getToken`.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként a megadott [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="next-steps"></a>További lépések
Most már továbbléphet további helyzeteket is.  Előfordulhat, hogy ki szeretné próbálni:

* [Egy Node.JS webes API-t az Azure ad-vel biztonságos](active-directory-devquickstarts-webapi-nodejs.md)
* Ismerje meg, [adal-t használó IOS alkalmazások közötti SSO engedélyezése](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

