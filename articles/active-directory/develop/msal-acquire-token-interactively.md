---
title: Tokenek kezelése (Microsoft Authentication Library)
titleSuffix: Microsoft identity platform
description: További információ a tokenek beszerzéséről és gyorsítótárazásáról a Microsoft Authentication Library (MSAL) használatával.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa6a939fce3eae8b1367c2d01e947e813fa5437
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803224"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Tokenek beszerzése és gyorsítótárazása a MSAL használatával
A [hozzáférési tokenek](access-tokens.md) lehetővé teszik az ügyfelek számára az Azure által védett webes API-k biztonságos meghívását. A Microsoft Authentication Library (MSAL) segítségével többféleképpen is beszerezheti a jogkivonatokat. A felhasználók egy webböngészőn keresztüli interakciót igényelnek. Némelyikhez nincs szükség felhasználói beavatkozásra. Általánosságban elmondható, hogy a token beszerzésének módja attól függ, hogy az alkalmazás egy nyilvános ügyfélalkalmazás (asztali vagy mobil alkalmazás) vagy egy bizalmas ügyfélalkalmazás (webalkalmazás, webes API vagy démon alkalmazás, például Windows-szolgáltatás).

A MSAL a beszerzését követően gyorsítótárazza a tokent.  Az alkalmazás kódjának csendesen (a gyorsítótárból) meg kell próbálnia a token lekérését, mielőtt más módon beszerezze a jogkivonatot.

Törölheti a jogkivonat-gyorsítótárat is, amely a fiókoknak a gyorsítótárból való eltávolításával érhető el. Ez nem távolítja el a böngészőben található munkamenet-cookie-t, de.

## <a name="scopes-when-acquiring-tokens"></a>Hatókörök a jogkivonatok beszerzése során
A [hatókörök](v2-permissions-and-consent.md) azok az engedélyek, amelyeket a webes API tesz elérhetővé az ügyfélalkalmazások számára, hogy hozzáférést kérjenek a alkalmazáshoz. Az ügyfélalkalmazások kérik a felhasználó belefoglalását ezekhez a hatókörökhöz, amikor hitelesítési kéréseket kap a tokenek eléréséhez a webes API-khoz. A MSAL lehetővé teszi, hogy jogkivonatokat kapjon az Azure AD for Developers (v 1.0) és a Microsoft Identity platform (v 2.0) API-k eléréséhez. a v 2.0 protokoll hatóköröket használ a kérelmekben lévő erőforrások helyett. További információért olvassa el a következőt: [v 1.0 és v 2.0 összehasonlítás](active-directory-v2-compare.md). Az által elfogadott jogkivonat-verzió webes API-konfigurációja alapján a v 2.0-végpont visszaadja a hozzáférési jogkivonatot a MSAL.

A MSAL beszerzésére szolgáló metódusok számának *hatókör* -paramétert kell megadni. Ez a paraméter a szükséges engedélyeket és erőforrásokat deklaráló karakterláncok egyszerű listája. A jól ismert hatókörök a [Microsoft Graph engedélyek](/graph/permissions-reference).

A MSAL a v 1.0 erőforrásokhoz is hozzáfér. További információért olvassa el [a v 1.0 alkalmazás hatóköröket](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>A webes API-k adott hatókörének kérése
Ha az alkalmazásnak adott engedélyekkel rendelkező jogkivonatokat kell igényelnie egy erőforrás-API-hoz, át kell adnia az API alkalmazás-azonosító URI-JÁT tartalmazó hatóköröket az alábbi formátumban: *&lt;alkalmazás azonosítójának URI-ja&gt;/&lt;hatóköre&gt;*

Például Microsoft Graph API hatókörei: `https://graph.microsoft.com/User.Read`

Vagy például egy egyéni webes API hatókörei: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

A Microsoft Graph API esetében csak a hatókör érték `user.read` `https://graph.microsoft.com/User.Read` formátumra mutat, és a szolgáltatás szinonimaként használható.

> [!NOTE]
> Bizonyos webes API-k (például a Azure Resource Manager API) (https://management.core.windows.net/) a hozzáférési jogkivonat célközönségi jogcíme (AUD) "/" karakterét várnak. Ebben az esetben fontos átadni a hatókört https://management.core.windows.net//user_impersonation ként (jegyezze fel a dupla perjelet), hogy a token érvényes legyen az API-ban.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dinamikus hatókörök kérése növekményes engedélyhez
Ha a 1.0-s verziójú alkalmazásokat készít, regisztrálnia kell az alkalmazás által a bejelentkezéskor a felhasználó számára szükséges engedélyek (statikus hatókörök) teljes készletét. A 2.0-s verzióban a hatókör-paraméterrel további engedélyekre lehet szükség. Ezeket dinamikus hatóköröknek nevezzük, és lehetővé teszik, hogy a felhasználó növekményes beleegyezett a hatókörökbe.

Például először bejelentkezhet a felhasználóba, és megtagadhatja a hozzáférést. Később lehetőség van arra, hogy beolvassa a felhasználó naptárát úgy, hogy kéri a naptár hatókörét a beszerzési jogkivonat metódusokban, és beolvassa a felhasználó belefoglalását.

Például: `https://graph.microsoft.com/User.Read` és `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokenek csendes beszerzése (a gyorsítótárból)
A MSAL megőrzi a jogkivonat-gyorsítótárat (vagy két gyorsítótárat a bizalmas ügyfélalkalmazások számára), és a beszerzése után gyorsítótárazza a tokent.  Sok esetben, ha egy jogkivonat csendes beolvasását kísérli meg, a gyorsítótárban lévő tokenen alapuló további hatókörök is megszerezhetnek egy másik tokent. Emellett képes a jogkivonatok frissítésére is, ha a lejárathoz közeledik (mivel a jogkivonat-gyorsítótár frissítési jogkivonatot is tartalmaz).

### <a name="recommended-call-pattern-for-public-client-applications"></a>A nyilvános ügyfélalkalmazások ajánlott hívási mintája
Az alkalmazás kódjának meg kell próbálkoznia a token csendes lekérésével (a gyorsítótárból), először.  Ha a metódus hívása "felhasználói felület szükséges" hibát vagy kivételt ad vissza, próbálkozzon más módon a jogkivonat beszerzésével. 

Van azonban két folyamat, amely előtt **ne** próbálkozzon a tokenek csendes beszerzésével:

- az [ügyfél-hitelesítő adatok folyamata](msal-authentication-flows.md#client-credentials), amely nem használja a felhasználói jogkivonat-gyorsítótárat, de az alkalmazás-jogkivonat gyorsítótára. Ez a módszer gondoskodik az alkalmazás-jogkivonat gyorsítótárának ellenőrzéséről, mielőtt elküld egy kérést az STS-nek.
- az [engedélyezési kód](msal-authentication-flows.md#authorization-code) Web Apps, mivel bevált egy kódot, amelyet az alkalmazás a felhasználó bejelentkezésekor kapott, és amelyek további hatókörökhöz csatlakoznak. Mivel a kód paraméterként van megadva, és nem egy fiók, a metódus nem tud megtekinteni a gyorsítótárban a kód beváltása előtt, amelyhez egyébként a szolgáltatás hívása szükséges.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Ajánlott hívási minta a Web Apps az engedélyezési kód folyamatábrájának használatával 
Az [OpenID Connect engedélyezési kódját](v2-protocols-oidc.md)használó webalkalmazások esetében a vezérlők ajánlott mintája a következő:

- Egy bizalmas ügyfélalkalmazás létrehozása a jogkivonat-gyorsítótárral testreszabott szerializálással. 
- A jogkivonat beszerzése az engedélyezési kód folyamatábrájának használatával

## <a name="acquiring-tokens"></a>Jogkivonatok beszerzése
Általában a token beszerzésének módja attól függ, hogy nyilvános ügyfél vagy bizalmas ügyfélalkalmazás-e.

### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások
A nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazások) esetében:
- A tokenek gyakran interaktív módon szerezhetők be, és a felhasználó felhasználói felület vagy előugró ablak használatával jelentkeznek be.
- [Tokent kaphat a bejelentkezett felhasználó számára](msal-authentication-flows.md#integrated-windows-authentication) az integrált Windows-hitelesítés (IWA/Kerberos) használatával, ha az asztali alkalmazás egy tartományhoz vagy az Azure-hoz csatlakoztatott Windows-számítógépen fut.
- A .NET-keretrendszer asztali ügyfélalkalmazások [számára a felhasználónévvel és jelszóval rendelkező tokent kaphat](msal-authentication-flows.md#usernamepassword) , de ez nem ajánlott. Ne használja a username/Password nevet a bizalmas ügyfélalkalmazások számára.
- A nem webböngészővel rendelkező eszközökön futó alkalmazások [programkód-folyamatán](msal-authentication-flows.md#device-code) keresztül kaphat jogkivonatot. A felhasználónak egy URL-címmel és egy kóddal kell megadnia egy webböngészőt, és be kell írnia a kódot, és be kell jelentkeznie.  Az Azure AD ezután visszaküldi a jogkivonatot a böngésző nélküli eszközre.

### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások 
A bizalmas ügyfélalkalmazások (webalkalmazások, webes API-k vagy démoni alkalmazások, például Windows-szolgáltatások) esetében:
- Az [ügyfél hitelesítő adatainak](msal-authentication-flows.md#client-credentials)beszerzésével **saját maga is megvásárolhatja az alkalmazáshoz** tartozó jogkivonatokat, nem pedig felhasználó számára. Ez használható eszközök szinkronizálására, illetve olyan eszközökre, amelyek általános és nem egy adott felhasználó számára dolgozzák fel a felhasználókat. 
- A webes API-k által használt, a felhasználó nevében egy API-t meghívó [helyszíni folyamat](msal-authentication-flows.md#on-behalf-of) . Az alkalmazás ügyfél-hitelesítő adatokkal van azonosítva, hogy a jogkivonat beszerzése felhasználói állítás (SAML például vagy JWT token) alapján történjen. Ezt a folyamatot olyan alkalmazások használják, amelyeknek hozzá kell férniük egy adott felhasználó erőforrásaihoz a szolgáltatások közötti hívásokban.
- Jogkivonatok beszerzése a webalkalmazások [engedélyezési kódjának](msal-authentication-flows.md#authorization-code) használatával, miután a felhasználó bejelentkezik az engedélyezési kérelem URL-címén. Az OpenID Connect alkalmazás általában ezt a mechanizmust használja, amely lehetővé teszi a felhasználó számára a bejelentkezést az Open ID Connect használatával, majd a felhasználó nevében hozzáférni a webes API-khoz.


## <a name="authentication-results"></a>Hitelesítési eredmények 
Ha az ügyfél hozzáférési jogkivonatot kér, az Azure AD olyan hitelesítési eredményt is ad vissza, amely a hozzáférési jogkivonattal kapcsolatos metaadatokat tartalmaz. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét, valamint azokat a hatóköröket, amelyekhez érvényes. Ezek az adatmennyiség lehetővé teszi, hogy az alkalmazás a hozzáférési tokenek elemzése nélkül végezzen intelligens gyorsítótárazást a hozzáférési jogkivonatok használatával.  A hitelesítési eredmények a következőket teszik elérhetővé:

- A webes API [hozzáférési jogkivonata](access-tokens.md) az erőforrások eléréséhez. Ez egy karakterlánc, amely általában Base64 kódolású JWT, de az ügyfélnek soha nem kell megkeresnie a hozzáférési jogkivonatban. A formátum nem garantált, hogy stabil maradjon, és titkosítva lehet az erőforrás számára. Az ügyfél hozzáférési jogkivonatának tartalmától függően a kód írása az egyik legnagyobb forrása a hibáknak és az ügyfél-logikai szüneteknek.
- A felhasználó [azonosító jogkivonata](id-tokens.md) (ez egy JWT).
- A jogkivonat lejárata, amely a jogkivonat lejárati dátumát és időpontját jelzi.
- A bérlő azonosítója tartalmazza azt a bérlőt, amelyben a felhasználó található. A vendég felhasználók (Azure AD B2B forgatókönyvek) esetében a bérlői azonosító a vendég bérlő, nem az egyedi bérlő. Ha a tokent egy felhasználó nevében küldi el a rendszer, a hitelesítési eredmény a felhasználóval kapcsolatos információkat is tartalmaz. A bizalmas ügyfél-folyamatok esetében, amelyekben a rendszer a jogkivonatokat nem felhasználó (az alkalmazás esetében) kéri, ez a felhasználói információ null értékű.
- Azok a hatókörök, amelyekhez a jogkivonat ki lett állítva.
- A felhasználó egyedi azonosítója.

## <a name="next-steps"></a>Következő lépések
További információ a [hibák és a kivételek kezelésére](msal-handling-exceptions.md)vonatkozóan. 
