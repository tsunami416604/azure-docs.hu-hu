---
title: Gyorsítótár-tokenek & beszerzése az MSAL-lal | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a tokenek beszerzését és gyorsítótárazását a Microsoft hitelesítési könyvtár (MSAL) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534584"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Tokenek beszerzése és gyorsítótárazása a Microsoft hitelesítési könyvtárával (MSAL)

[A hozzáférési jogkivonatok](access-tokens.md) lehetővé teszik az ügyfelek számára, hogy biztonságosan hívhassák meg az Azure által védett webes API-kat. A Microsoft Authentication Library (MSAL) segítségével számos módon szerezhet be jogkivonatot. Bizonyos módokfelhasználói interakciókat igényelnek a webböngészőn keresztül. Vannak, akik nem igényelnek felhasználói beavatkozást. A jogkivonat beszerzésének módja általában attól függ, hogy az alkalmazás nyilvános ügyfélalkalmazás (asztali vagy mobilalkalmazás) vagy bizalmas ügyfélalkalmazás (webalkalmazás, webes API vagy démonalkalmazás, például Windows-szolgáltatás).

Az MSAL a beolvasás után gyorsítótárazza a jogkivonatot.  Az alkalmazáskódnak meg kell próbálnia egy jogkivonatot csendben beszerezni (a gyorsítótárból), először, mielőtt más módon szerezne be egy jogkivonatot.

Törölheti a token gyorsítótár, amely úgy érhető el, hogy eltávolítja a fiókokat a gyorsítótárból. Ez azonban nem távolítja el a munkamenet-cookie-t, amely a böngészőben található.

## <a name="scopes-when-acquiring-tokens"></a>Hatókörök jogkivonatok beolvasásakor

[A hatókörök](v2-permissions-and-consent.md) azok az engedélyek, amelyeket a webes API-k elérhetővé tegyenek az ügyfélalkalmazások számára. Az ügyfélalkalmazások a felhasználó beleegyezését kérik ezekhez a hatókörökhöz, amikor hitelesítési kérelmeket hajtanak, hogy jogkivonatokat kapjanak az webes API-k eléréséhez. Az MSAL lehetővé teszi, hogy jogkivonatokat szerezzen az Azure AD-hez a fejlesztők (1.0-s verzió) és a Microsoft identity platform (2.0) API-k számára. A 2.0-s protokoll a kérelmekben erőforrás helyett hatóköröket használ. További információért olvassa el [az 1.0 és a 2.0-s és a 2.0-s összehasonlítást.](active-directory-v2-compare.md) A web API-konfiguráció alapján a jogkivonat-verzió elfogadja, a v2.0 végpont visszaadja a hozzáférési jogkivonatot az MSAL.

Számos MSAL token metódusok *hatókör-paramétert* igényel. Ez a paraméter a kért engedélyeket és erőforrásokat deklaráló karakterláncok egyszerű listája. Jól ismert hatókörök a [Microsoft Graph engedélyeket](/graph/permissions-reference).

Az MSAL-ban is lehetséges az 1.0-s és a 0-s erőforrás eléréséhez. További információért olvassa el [a Hatókörek egy 1.0-s alkalmazás hatóköreit.](msal-v1-app-scopes.md)

### <a name="request-specific-scopes-for-a-web-api"></a>Adott hatókörök kérése egy webes API-hoz

Ha az alkalmazásnak adott engedélyekkel rendelkező jogkivonatokat kell kérnie egy erőforrás API-hoz, az alábbi formátumban kell átadnia az API alkalmazásazonosító-URI-ját tartalmazó * &lt;hatóköröket: alkalmazásazonosító&gt;/&lt;URI-hatóköre&gt;*

Például a Microsoft Graph API hatókörei:`https://graph.microsoft.com/User.Read`

Vagy például egy egyéni webes API hatókörei:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

A Microsoft Graph API esetében csak `user.read` egy `https://graph.microsoft.com/User.Read` hatókörérték-leképezések formázni, és szinonimaként használható.

> [!NOTE]
> Bizonyos webes API-k, példáulhttps://management.core.windows.net/) az Azure Resource Manager API (a hozzáférési jogkivonat közönségjogcímében (aud) egy záró "/" várható. Ebben az esetben fontos, hogy adja https://management.core.windows.net//user_impersonation át a hatókört (vegye figyelembe a kettős perjel), a jogkivonat érvényes az API-ban.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dinamikus hatókörök kérése a növekményes hozzájáruláshoz

Amikor az 1.0-s verziója használatával hoz létre alkalmazásokat, regisztrálnia kellett az alkalmazás által a bejelentkezéskor szükséges engedélyek (statikus hatókörök) teljes készletét. A 2.0-s vagyonban szükség szerint további engedélyeket kérhet a hatókör paraméter használatával. Ezeket dinamikus hatóköröknek nevezzük, és lehetővé teszik a felhasználó számára, hogy növekményes jóváhagyást adjon a hatókörökhöz.

Például először bejelentkezhet a felhasználóba, és megtagadhatja tőlük a hozzáférést. Később lehetővé teheti számukra, hogy olvassa el a naptár a felhasználó a beszerzési jogkivonat-metódusok a naptár hatókörét, és a felhasználó beleegyezését.

Például: `https://graph.microsoft.com/User.Read` és`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokenek csendes beolvasása (a gyorsítótárból)

Az MSAL jogkivonat-gyorsítótárat (vagy a bizalmas ügyfélalkalmazások két gyorsítótárát) tart fenn, és a beolvasást követően gyorsítótárazza a jogkivonatot.  Sok esetben egy jogkivonat csendes lekérésének megkísérlése egy másik jogkivonatot szerez be, amely több hatókört a gyorsítótárban lévő jogkivonaton alapuló további hatókörekkel. A token frissítésére is képes, amikor a lejárati idő höz közeledik (mivel a token gyorsítótára frissítési jogkivonatot is tartalmaz).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Ajánlott hívásminta nyilvános ügyfélalkalmazásokhoz

Alkalmazáskód meg kell próbálnia, hogy egy jogkivonatot csendben (a gyorsítótárból), először.  Ha a metódushívás "Felhasználói felület szükséges" hibát vagy kivételt ad vissza, próbáljon meg más módon tokeneket beszerezni.

Azonban két folyamat, amely előtt **nem kell** megpróbálni, hogy csendben megszerezni egy jogkivonatot:

- [ügyfél hitelesítő adatok flow,](msal-authentication-flows.md#client-credentials)amely nem használja a felhasználói jogkivonat-gyorsítótár, hanem egy alkalmazás token cache. Ez a módszer gondoskodik az alkalmazástoken-gyorsítótár ellenőrzéséről, mielőtt kérelmet küldene az STS-nek.
- [engedélyezési kód áramlását](msal-authentication-flows.md#authorization-code) a webalkalmazásokban, mivel beváltja a kódot, hogy az alkalmazás kapott a felhasználó bejelentkezésével, és azokat hozzájárulástovábbi hatókörök. Mivel a kód paraméterként, és nem fiókként kerül átadásra, a metódus nem tud a gyorsítótárban keresni a kód beváltása előtt, amely egyébként is hívást igényel a szolgáltatáshoz.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Ajánlott hívásminta a webalkalmazásokban az Engedélyezési kód folyamat használatával

Az [OpenID Connect engedélyezési kódfolyamatot](v2-protocols-oidc.md)használó webalkalmazások esetében a vezérlők ajánlott mintája a következő:

- Bizalmas ügyfélalkalmazás példányosítása egyedi szerializálással rendelkező jogkivonat-gyorsítótárral.
- A jogkivonat beszerzése az engedélyezési kód folyamatával

## <a name="acquiring-tokens"></a>Tokenek beolvasása

Általában a jogkivonat megszerzésének módja attól függ, hogy nyilvános vagy bizalmas ügyfélalkalmazásról van-e szó.

### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

Nyilvános ügyfélalkalmazások (asztali vagy mobilalkalmazás) esetén:
- Gyakran szerez tokeneket interaktívan, miután a felhasználó egy felhasználói felületen vagy előugró ablakon keresztül jelentkezik be.
- Az integrált Windows-hitelesítés (IWA/Kerberos) használatával [a bejelentkezett felhasználó számára csendes en konkretitust kaphat,](msal-authentication-flows.md#integrated-windows-authentication) ha az asztali alkalmazás egy tartományhoz vagy az Azure-hoz csatlakozott Windows-számítógépen fut.
- A .NET framework asztali ügyfélalkalmazásokban [felhasználónévvel és jelszóval rendelkező jogkivonatot kaphat,](msal-authentication-flows.md#usernamepassword) de ez nem ajánlott. Ne használjon felhasználónevet/jelszót bizalmas ügyfélalkalmazásokban.
- Szerezhet egy jogkivonatot az [eszközkód-folyamaton](msal-authentication-flows.md#device-code) keresztül olyan eszközökön futó alkalmazásokban, amelyek nem rendelkeznek webböngészővel. A felhasználó kap egy URL-t és egy kódot, aki ezután megy egy webböngésző egy másik eszközön, és beírja a kódot, és bejelentkezik.  Az Azure AD ezután egy jogkivonatot küld vissza a böngésző nélküli eszközre.

### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

Bizalmas ügyfélalkalmazások (webalkalmazás, webes API vagy démonalkalmazás, például Windows-szolgáltatás) esetén a következőket teszi:
- Az [ügyfél hitelesítő adatainak folyamatával](msal-authentication-flows.md#client-credentials)nem az **alkalmazás jogkivonatait** szerezze be, és ne egy felhasználószámára. Ez olyan eszközök vagy eszközök szinkronizálására használható, amelyek általában a felhasználókat dolgozzák fel, és nem egy adott felhasználót.
- A webes [API-k nevében egy api-t](msal-authentication-flows.md#on-behalf-of) a felhasználó nevében hívhat meg. Az alkalmazás ügyfélhitelesítő adatokkal azonosítja annak érdekében, hogy egy felhasználói állításon (például SAML vagy jwt-jogkivonaton) alapuló jogkivonatot szerezzen be. Ezt a folyamatot olyan alkalmazások használják, amelyeknek egy adott felhasználó erőforrásaihoz kell hozzáférnie a szolgáltatás-szolgáltatás hívásokban.
- Tokenek beszerzése az [engedélyezési kód folyamat](msal-authentication-flows.md#authorization-code) a webalkalmazásokban, miután a felhasználó bejelentkezik az engedélyezési kérelem URL-címen keresztül. OpenID Connect alkalmazás általában ezt a mechanizmust használja, amely lehetővé teszi a felhasználó bejelentkezést open id connect használatával, majd a felhasználó nevében webes API-k eléréséhez.

## <a name="authentication-results"></a>Hitelesítési eredmények

Amikor az ügyfél hozzáférési jogkivonatot kér, az Azure AD egy hitelesítési eredményt is ad vissza, amely tartalmaz néhány metaadatot a hozzáférési jogkivonatról. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét és azokat a hatóköröket, amelyekre érvényes. Ezek az adatok lehetővé teszik, hogy az alkalmazás a hozzáférési jogkivonatok intelligens gyorsítótárazását anélkül végezze el, hogy magát a hozzáférési jogkivonatot kellene elemeznie.  A hitelesítéseredménye a következőket teszi elérhetővé:

- A webes API-hoz az erőforrások eléréséhez a [hozzáférési jogkivonat.](access-tokens.md) Ez egy karakterlánc, általában egy base64 kódolt JWT, de az ügyfél soha ne nézzen a hozzáférési jogkivonatba. A formátum nem garantált, hogy stabil marad, és az erőforrás titkosítható. Az ügyfélen lévő hozzáférési jogkivonat-tartalomtól függően kódot író személyek az egyik legnagyobb hibaforrás és ügyféllogikai törés.
- A felhasználó [azonosító tokenje](id-tokens.md) (ez egy JWT).
- A jogkivonat lejárata, amely megmondja a dátum/idő, amikor a jogkivonat lejár.
- A bérlőazonosító tartalmazza azt a bérlőt, amelyben a felhasználó található. Vendég felhasználók (Azure AD B2B forgatókönyvek) a bérlői azonosító a vendég bérlő, nem az egyedi bérlő. Amikor a jogkivonat egy felhasználó nevében kerül kézbesítésre, a hitelesítési eredmény a felhasználóra vonatkozó információkat is tartalmaz. Olyan bizalmas ügyfélfolyamatok esetén, ahol a jogkivonatokat felhasználó nélkül kérik (az alkalmazáshoz), ez a felhasználói információ null értékű.
- Azok a hatókörök, amelyekhez a jogkivonatot kiadták.
- A felhasználó egyedi azonosítója.

## <a name="next-steps"></a>További lépések

Ha Az MSAL java-hoz, ismerje meg az [egyéni tokengyorsítótár-szerializálást az MSAL for Java alkalmazásban.](msal-java-token-cache-serialization.md)

További információ a [hibák és kivételek kezeléséről.](msal-handling-exceptions.md)
