---
title: "IOS alkalmazás - Azure AD B2C segítségével tokenbeolvasás |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan Azure Active Directory B2C AppAuth használ a felhasználói identitások kezelésére, és hitelesíti a felhasználókat egy iOS-alkalmazás létrehozásához."
services: active-directory-b2c
documentationcenter: ios
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: ebec5d910b8987dcc8155cd4ead00f87d219941c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Az Azure AD B2C: Bejelentkezés használata iOS-alkalmazás

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. További fejlesztői választott egy megnyitott szabványos protokollt használó kínál, a szalagtár szolgáltatások integrálása kiválasztásakor. Ez a forgatókönyv, míg mások például úgy, hogy segítséget nyújtanak a fejlesztők az alkalmazások a Microsoft Identity platform-hez mellékelt. A legtöbb tárak, amelyek megvalósítják az [a RFC6749 OAuth2 spec](https://tools.ietf.org/html/rfc6749) kapcsolódik a Microsoft Identity platformra.

> [!WARNING]
> A Microsoft nem biztosít a külső szalagtárak javítások, és nem végrehajtva a tárak áttekintése. Ez a minta, amely tesztelték AppAuth nevű alapszintű forgatókönyvekben kompatibilisek-e az Azure AD B2C külső szalagtárat használ. A könyvtár nyílt forráskódú projekt problémákat és funkciókra vonatkozó kérések legyenek irányítva. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Ha most ismerkedik az OAuth2 vagy az OpenID Connect, ez a minta konfigurálási nem célszerű sok Önnek. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt. A könyvtár egy olyan tároló, a felhasználók, alkalmazások, csoportok és több. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása
A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Az alkalmazás regisztrálása biztosít az alkalmazás biztonságos kommunikációhoz szükséges információkat az Azure AD. A mobilalkalmazások létrehozásához hajtsa végre az [ezeket az utasításokat](active-directory-b2c-app-registration.md). Ügyeljen arra, hogy:

* Tartalmaznak egy **natív ügyfél** az alkalmazásban.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. A GUID később szüksége.
* Állítson be egy **átirányítási URI-** egy egyéni séma (például com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Ezt az URI később szüksége.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Szabályzatok létrehozása
Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Az alkalmazás tartalmaz egy identitás-élmény: egy kombinált bejelentkezési és regisztrációs. A házirend létrehozásához lásd: a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). A szabályzat létrehozásakor ügyeljen arra, hogy:

* A **regisztrációs attribútumokat**, válassza ki az attribútum **megjelenített név**.  Kiválaszthatja, valamint egyéb attribútumai.
* A **alkalmazási jogcímet**, válassza ki a jogcímeket **megjelenített név** és **felhasználó Objektumazonosító**. Kiválaszthatja, hogy más jogcímeket is.
* Az egyes házirendek létrehozása után másolja a házirend **nevét**. A házirend neve a következő előtaggal van `b2c_1_` amikor menteni a házirendet.  A házirend nevére később szüksége.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése
Egy Azure AD B2C AppAuth használó minta adtunk [a Githubon](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Töltse le a kódot, és futtassa. A saját Azure AD B2C bérlő használatához kövesse az utasításokat a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Ez a minta információs útmutatásai szerint hozta létre a [iOS AppAuth-projektre a Githubon](https://github.com/openid/AppAuth-iOS). További részletek a minta és a szalagtár működése lásd a AppAuth Readme fájlt a Githubon.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás az Azure AD B2C használata AppAuth módosítása

> [!NOTE]
> AppAuth támogatja az iOS 7-es vagy újabb verzió.  Azonban Google közösségi bejelentkezések támogatásához SFSafariViewController szükséges ehhez a iOS 9-es vagy újabb verzióját.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2C kommunikációs konfigurálható az engedélyezési végpont és a token-végpont URI-azonosítók megadásával.  Az URI-k létrehozásához szüksége van a következő információkat:
* Bérlő azonosítója (például contoso.onmicrosoft.com)
* Házirend neve (például B2C\_1\_SignUpIn)

A token-végpont URI generálhatók azáltal, hogy a bérlő\_azonosítója és a házirend\_neve a következő URL-cím:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Az engedélyezési végpont URI generálhatók azáltal, hogy a bérlő\_azonosítója és a házirend\_neve a következő URL-cím:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Futtassa a következő kódot a AuthorizationServiceConfiguration objektum létrehozásához:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Engedélyezése

Konfigurálja, vagy egy engedélyezési szolgáltatás konfigurációjának beolvasása után egy engedélyezési-kérelem lehet létrehozni. A kérelem létrehozásához szüksége van a következő információkat:  
* Ügyfél-azonosító (például 00000000-0000-0000-0000-000000000000)
* Átirányítási URI egy egyéni séma (például com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Mindkét elemeket kell mentett Ha Ön volt [regisztrálja az alkalmazást](#create-an-application).

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

Az alkalmazás kezelni az egyéni séma URI-ra való beállításához módosítania a Info.pList URL-sémákat listája:
* Nyissa meg az Info.pList-ben.
* Például a "Csomag az operációs rendszer típusa Code" sor mutat, majd kattintson a \+ szimbólum.
* Nevezze át az új sor "URL-cím types".
* Kattintson a "URL-cím types" balra mutató nyílra a fa megnyitásához.
* Kattintson a balra mutató nyílra "elem 0' a fa megnyitásához.
* Nevezze át az első elem "URL-sémákat" 0 elem alatt.
* Kattintson a "URL-sémákat" nyissa meg a fa balra mutató nyílra.
* Az "Érték" oszlop nincs egy üres mező balra "elem 0'"URL-sémákat"alá.  Adja meg az értéket az alkalmazás egyedi séma.  Az értéket meg kell egyeznie a rendszer a OIDAuthorizationRequest objektum létrehozásakor használt redirectURL.  A mintában a rendszer "com.onmicrosoft.fabrikamb2c.exampleapp" használtuk.

Tekintse meg a [AppAuth útmutató](https://openid.github.io/AppAuth-iOS/) való fejezze be a folyamatot. Ha egy működő alkalmazást gyorsan megismerkedhet van szüksége, tekintse meg [a minta](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) a saját Azure AD B2C konfigurációs megadását.

Azt mindig nyitva a visszajelzések és tanácsok! Ha bármilyen nehézségbe ütközik az ebben a témakörben, vagy az ehhez a tartalomhoz javítására állnak, azt fogadjuk visszajelzéseit az oldal alján. A szolgáltatás kéréseket, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
