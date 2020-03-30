---
title: AppAuth használata iOS-alkalmazásban
titleSuffix: Azure AD B2C
description: Hogyan hozhat létre egy iOS-alkalmazást, amely az AppAuth és az Azure Active Directory B2C használatával kezeli a felhasználói identitásokat és hitelesíti a felhasználókat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186828"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Bejelentkezés iOS-alkalmazáshasználatával

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. A nyílt szabványú protokoll használata több fejlesztői lehetőséget kínál, amikor kiválaszt egy könyvtárat, amelyintegrálódik a szolgáltatásainkkal. Ezt a forgatókönyvet és a hozzá hasonló kontőket a Microsoft Identity platformhoz csatlakozó alkalmazások írásában segíthetjük a fejlesztőknek. Az [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) megvalósító legtöbb könyvtár képes csatlakozni a Microsoft Identity platformhoz.

> [!WARNING]
> A Microsoft nem biztosít javításokat harmadik féltől származó tárakhoz, és nem vizsgálta felül ezeket a könyvtárakat. Ez a minta egy külső gyártótól származó, AppAuth nevű függvénytárat használ, amelyet az Azure AD B2C-vel az alapvető forgatókönyvekben való kompatibilitás szempontjából teszteltek. A problémákat és a szolgáltatáskérelmeket a tár nyílt forráskódú projektjéhez kell irányítani. További információt [ebben a cikkben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)talál.
>
>

Ha most jön az OAuth2 vagy az OpenID Connect, a mintakonfiguráció nagy része nem sok értelme van. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtár egy tároló az összes felhasználó, alkalmazások, csoportok és így tovább. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ezután regisztráljon egy alkalmazást az Azure AD B2C-bérlőben. Ez biztosítja az Azure AD az alkalmazással való biztonságos kommunikációhoz szükséges információkat.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Is rögzítse az egyéni átirányítási URI-hoz használ egy későbbi lépésben. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Felhasználói folyamatok létrehozása
Az Azure AD B2C-ben minden felhasználói élményt egy [felhasználói folyamat](user-flow-overview.md)határoz meg. Ez az alkalmazás egy identitásélményt tartalmaz: a kombinált bejelentkezés és a regisztráció. A felhasználói folyamat létrehozásakor ügyeljen arra, hogy:

* A **Regisztrációs attribútumok csoportban**válassza ki a **Megjelenítendő név attribútumot.**  Más attribútumokat is kiválaszthat.
* Az **Alkalmazásjogcímek**csoportban válassza ki a **jogcímeket A megjelenített név** és a felhasználó **objektumazonosítója csoportban.** Más jogcímeket is választhat.
* A létrehozás után másolja az egyes felhasználói folyamatok **nevét.** A felhasználói folyamat neve `b2c_1_` előtaggal van ellátva a felhasználói folyamat mentésekor.  Később szüksége lesz a felhasználói folyamat nevére.

Miután létrehozta a felhasználói folyamatokat, készen áll az alkalmazás létrehozásához.

## <a name="download-the-sample-code"></a>A mintakód letöltése
A [GitHubon](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)az Azure AD B2C használatával működő mintát biztosítottunk. Letöltheti a kódot, és futtathatja. Saját Azure AD B2C-bérlő használatához kövesse a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md)című utasításokat.

Ezt a mintát az iOS AppAuth projekt README utasításainak követésével hozták létre [a GitHubon.](https://github.com/openid/AppAuth-iOS) A minta és a könyvtár működésével kapcsolatos további részletekért keresse fel az AppAuth README-t a GitHubon.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás módosítása az Azure AD B2C és az AppAuth használatához

> [!NOTE]
> Az AppAuth támogatja az iOS 7-es és újabb rendszert.  A Google közösségi bejelentkezéseinek támogatásához azonban az SFSafariViewController szükséges, amelyhez iOS 9 vagy újabb rendszer szükséges.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2C-vel való kommunikáció konfigurálásához adja meg az engedélyezési végpont és a token végpont URI-k.  Az URI-k létrehozásához a következő információkra van szükség:
* Bérlőazonosítója (például contoso.onmicrosoft.com)
* Felhasználóforgalom neve (például B2C\_1\_SignUpIn)

A jogkivonat-végpont URI-ja a\_bérlői azonosító\_és a házirend neve lecserélésével jön létre a következő URL-címen:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Az engedélyezési végpont URI-ja a bérlői\_azonosító és\_a házirend neve lecserélésével jön létre a következő URL-címen:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Futtassa a következő kódot az AuthorizationServiceConfiguration objektum létrehozásához:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Engedélyezése

Az engedélyezési szolgáltatás konfigurációjának konfigurálása vagy beolvasása után engedélyezési kérelem állítható létre. A kérelem létrehozásához a következő adatokra van szükség:

* A korábban rögzített ügyfélazonosító (APPLICATION ID). Például: `00000000-0000-0000-0000-000000000000`.
* Egyéni átirányítási URI, amely korábban rögzített. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Az [alkalmazás regisztrálásakor](#create-an-application)mindkét elemet menteni kellett volna.

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Ha be szeretné állítani az alkalmazást úgy, hogy az egyéni sémával kezelje az URI-ba való átirányítást, frissítenie kell az "URL-sémák" listáját az Info.pList listában:
* Nyissa meg az Info.pList parancsot.
* Mutasson egy sorra, például a \+ "Bundle OS Type Code" (Csomagoperációsrendszer típuskód) elemre, és kattintson a szimbólumra.
* Nevezze át az új sor "URL-típusok".
* A fa megnyitásához kattintson az "URL-típusok" bal oldalán található nyílra.
* A fa megnyitásához kattintson a "0. elem" bal oldalán lévő nyílra.
* Nevezze át az első elemet a 0.
* A fa megnyitásához kattintson az "URL-sémák" bal oldalán található nyílra.
* Az "Érték" oszlopban egy üres mező található a "0.  Állítsa be az értéket az alkalmazás egyedi sémára.  Az értéknek meg kell egyeznie az REDirectURL-ben használt sémával az OIDAuthorizationRequest objektum létrehozásakor.  A mintában a rendszer "com.onmicrosoft.fabrikamb2c.exampleapp" rendszer használatos.

Tekintse meg az [AppAuth útmutatót](https://openid.github.io/AppAuth-iOS/) a folyamat további befejezéséhez. Ha gyorsan el kell kezdenie egy működő alkalmazást, tekintse meg [a mintát.](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) saját Azure AD B2C-konfigurációjának megadásához kövesse a lépéseket.

Mindig nyitottak vagyunk a visszajelzésekre és a javaslatokra! Ha bármilyen nehézséget okoz ez a cikk, vagy javaslatokat tesz a tartalom javítására, nagyra értékeljük a visszajelzést az oldal alján. Szolgáltatáskérések esetén adja hozzá őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
