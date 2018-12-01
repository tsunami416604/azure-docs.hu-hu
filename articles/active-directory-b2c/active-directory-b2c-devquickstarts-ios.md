---
title: Az AppAuth használatával az iOS-alkalmazásokba az Azure Active Directory B2C |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre iOS-alkalmazás, amely az Azure Active Directory B2C az AppAuth használatával felhasználói identitásokat kezelhet és hitelesítheti a felhasználókat.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9f475c7bc373afd8a109873908bb583bc07708f1
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722547"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Az Azure AD B2C: Bejelentkezés egy iOS-alkalmazás használatával

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. További fejlesztői választott kínál egy nyílt szabványú protokoll használatával, egy kódtárat integrálhatnak szolgáltatásainkkal kiválasztásakor. A fejlesztők vizsgálatot, amely csatlakozni a Microsoft Identity platform-alkalmazások írására, mint ez a forgatókönyv és egyéb mellékelt. Használó legtöbb kódtár [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) tudnak csatlakozni a Microsoft Identity platform.

> [!WARNING]
> A Microsoft nem biztosít javítását, külső gyártótól származó kódtárakat, és nem végzett át ezeket a kódtárakat. Ez a minta egy külső kódtár, hogy tesztelték az AppAuth nevű alapszintű forgatókönyvekben kompatibilitás érdekében az Azure AD B2C-vel használ. Problémák és a funkciókérések legyen átirányítva a tár nyílt forráskódú projekt. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Ha most ismerkedik az OAuth2 vagy az OpenID Connect, a minta konfigurálási előfordulhat, hogy nem értelmezhető Önnek. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt. Egy könyvtár az összes felhasználó, alkalmazások, csoportok és további tárolója. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása
A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Az alkalmazás regisztrációját biztosítja az alkalmazás biztonságos kommunikációhoz szükséges információkat az Azure ad-ben. Mobilalkalmazás létrehozása, hajtsa végre a [ezek az utasítások](active-directory-b2c-app-registration.md). Ügyeljen arra, hogy:

* Például egy **natív ügyfél** az alkalmazásban.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Később szüksége lesz a GUID.
* Állítsa be a **átirányítási URI-t** egy egyéni sémával (például com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Később szüksége lesz az URI.

## <a name="create-your-user-flows"></a>A felhasználói folyamatok létrehozása
Az Azure AD B2C felhasználói élményeket által meghatározott egy [felhasználói folyamat](active-directory-b2c-reference-policies.md). Ez az alkalmazás egyetlen identitással kapcsolatos tartalmaz: egy kombinált bejelentkezési és a regisztrációhoz. Ez a felhasználói folyamat létrehozása a leírtak szerint a [felhasználói folyamat áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ha a felhasználói folyamatot hoz létre, ügyeljen arra, hogy:

* A **regisztrálási attribútumok**, válassza ki az attribútumot **megjelenítendő név**.  Egyéb attribútumokat is választhat.
* A **alkalmazásjogcímek**, válassza a jogcímek **megjelenítendő név** és **felhasználó Objektumazonosítója**. Kiválaszthat egyéb jogcímeket is.
* Másolás a **neve** az egyes felhasználói folyamatok létrehozása után. A felhasználói interakciósorozat neve a következő előtaggal kezdődik `b2c_1_` a felhasználói folyamat mentésekor.  Később szüksége lesz a felhasználói interakciósorozat neve.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A felhasználói folyamatok létrehozását követően készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése
Biztosítunk egy mintát, amely az AppAuth használja az Azure AD B2C-vel [a Githubon](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Töltse le a kódot, és futtathatja. A saját Azure AD B2C-bérlő használatához hajtsa végre a következő témakör utasításait a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Ez a minta hozta létre a információs fájlban OLVASHATÓ utasítások szerint a [iOS az AppAuth a GitHub project](https://github.com/openid/AppAuth-iOS). Hogyan működik a minta és a tár további részleteiért hivatkozhat a Githubon az AppAuth információs fájlban OLVASHATÓ.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás Azure AD B2C használata az AppAuth módosítása

> [!NOTE]
> Az AppAuth támogatja az iOS 7 és újabb verziók.  Azonban a Google közösségi bejelentkezések támogatásához SFSafariViewController szükséges ami megköveteli, hogy az iOS 9-es vagy újabb verziója.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2C-vel kommunikációs konfigurálhatja az engedélyezési végpont és a jogkivonat-végpont URI-k megadásával.  Az URI-k létrehozásához szüksége van a következő információkat:
* Bérlő azonosítója (például contoso.onmicrosoft.com)
* Felhasználói interakciósorozat neve (például a B2C\_1\_SignUpIn)

A jogkivonat-végpont URI-t hozhat létre és cserélje le a bérlő\_Azonosítóját és a szabályzat\_az alábbi URL-cím neve:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Az engedélyezési végpont URI-t hozhat létre és cserélje le a bérlő\_Azonosítóját és a szabályzat\_az alábbi URL-cím neve:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Futtassa a következő kódot a AuthorizationServiceConfiguration objektum létrehozásához:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Engedélyező

Miután konfigurálása, vagy egy engedélyezési szolgáltatás konfigurációjának lekérése, egy engedélyezési kérést lehet létrehozni. A kérelem létrehozásához szüksége van a következő információkat:  
* Ügyfél-azonosító (például 00000000-0000-0000-0000-000000000000)
* Átirányítási URI-t egy egyéni sémával (például com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Mindkettőt kell a rendszer mentette, amikor [regisztrálja az alkalmazást](#create-an-application).

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

Állítsa be az alkalmazás kezelje az átirányítás, az egyéni sémával rendelkező URI-ra, az info.plist fájl URL-sémákat listáját kell:
* Nyissa meg az info.plist fájlban.
* "Csomag az operációs rendszer típusa code" sor fölé, majd kattintson a \+ szimbólum.
* Nevezze át az új sorban "URL-cím-típusok".
* Kattintson a nyílra a bal oldali URL-cím-típusok a fa megnyitásához.
* Kattintson a balra mutató nyílra "elemet 0" a fa megnyitásához.
* Nevezze át az első elem, "URL-sémát a(z)" 0 elem alá.
* Kattintson a "URL-sémákat" megnyitása a fa balra mutató nyílra.
* A "Value" oszlop nincs egy üres mező bal oldalán található "0 elem:"URL-sémákat"alatt.  Állítsa az értékét az alkalmazás egyedi séma.  Az értéknek egyeznie kell a séma Webnézettől a OIDAuthorizationRequest objektum létrehozásakor használt.  A mintában a rendszer "com.onmicrosoft.fabrikamb2c.exampleapp" szolgál.

Tekintse meg a [az AppAuth útmutató](https://openid.github.io/AppAuth-iOS/) való fejezze be a folyamatot. Ha a használatának gyors megkezdése egy működő alkalmazást van szüksége, tekintse meg az [a minta](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) , adja meg a saját Azure AD B2C konfiguráció.

Mindig tudjuk nyissa meg a vélemények és tanácsok! Ha ez a cikk a kérdése van, vagy javaslatai ezt a tartalmat, örömmel visszajelzését a lap alján. A szolgáltatással kapcsolatos kéréseit, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
