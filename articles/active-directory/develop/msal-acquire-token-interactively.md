---
title: Jogkivonatok (Microsoft-hitelesítési tár) kezelése |} Az Azure
description: További információ beszerzéséhez és a gyorsítótárazás jogkivonatok segítségével a Microsoft-hitelesítési tár (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546070"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Beszerzéséhez, és az MSAL használatával jogkivonatok gyorsítótárazása
[Hozzáférési jogkivonatokat](access-tokens.md) biztonságos hívása a webes API-k az Azure által védett ügyfelek. Számos módon használja a Microsoft-hitelesítési tár (MSAL) egy token beszerzéséhez. Néhány módszer felhasználói interakció érdekében egy webböngészőn keresztül van szükség. Néhány nem szükséges minden felhasználói interakció érdekében. Általában a jogkivonat-beszerzési módja attól függ, ha az alkalmazás nyilvános ügyfélalkalmazás (asztali vagy mobil alkalmazás) vagy a bizalmas ügyfélalkalmazások (például egy olyan Windows-szolgáltatáshoz webalkalmazás, webes API-t vagy démon alkalmazás)-e.

Az MSAL gyorsítótárazza a jogkivonat után azt beszerezte.  Alkalmazáskód egy token beszerzéséhez csendes módban (a gyorsítótárból), először egy token beszerzése a más módon előtt próbálja meg.

A jogkivonatok gyorsítótárát, amely úgy történik, hogy a fiókok eltávolítása a gyorsítótárból is törölheti. A munkamenetcookie-t, amely a böngészőben, azonban ez nem távolítja el.

## <a name="scopes-when-acquiring-tokens"></a>Amikor a jogkivonatok beszerzésének hatókörök
[Hatókörök](v2-permissions-and-consent.md) az engedélyek, amelyek a webes API elérhetővé teszi az ügyfélalkalmazások számára a hozzáférés kérése. Ügyfélalkalmazások ezeken a hatókörökön a felhasználói jóváhagyás kérése tokenekhez a webes API-k elérésére a hitelesítési kérést küldő. Az MSAL segítségével tokenekhez elérni az Azure AD fejlesztőknek (1.0-s verzió) és a Microsoft identity platform (2.0-s verzió) API-k. v2.0 protokoll helyett erőforrás felé küldött kérésekben a hatóköröket. További információkért olvassa el [1.0-s és 2.0-s verzió összehasonlítása](active-directory-v2-compare.md). A token verzió-jeit fogadja el a webes API-k konfigurációján alapul, a v2.0-végpont adja vissza a hozzáférési jogkivonatot az MSAL.

Az MSAL számos beszerezni a hitelesítési módok közül szükséges egy *hatókörök* paraméter. Ez a paraméter az egyszerű listája, amelyek a kívánt engedélyekkel és a kért erőforrások deklarálja. Jól ismert hatóköröknek a [Microsoft Graph-engedélyek](/graph/permissions-reference).

Akkor is az MSAL 1.0-s verziójú erőforrások eléréséhez. További információkért olvassa el [egy 1.0-s verziójú alkalmazás hatóköreinek](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Kérelem meghatározott hatókörök webes API-hoz
Az alkalmazásnak kell egy erőforrás API különleges engedélyekkel rendelkező jogkivonatokat kérhet, ha az alkalmazást azonosító URI-ját az API-t tartalmazó hatókört át kell az az alábbi formátumban:  *&lt;app ID URI&gt; / &lt;hatókör&gt;*

Ha például a Microsoft Graph API-hoz hatókörök: `https://graph.microsoft.com/User.Read`

Vagy például egy egyéni webes API hatóköreinek: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

A Microsoft Graph API-hoz, csak, egy hatókör értéke `user.read` képez le `https://graph.microsoft.com/User.Read` formázhatja és felcserélhetők.

> [!NOTE]
> Bizonyos webes API-k, például az Azure Resource Manager API-val (https://management.core.windows.net/) várhatóan egy záró "/" karaktert a célközönség jogcímek a hozzáférési jogkivonat (aud). Ebben az esetben fontos, hogy adja át a hatókör https://management.core.windows.net//user_impersonation (vegye figyelembe a dupla perjellel), a jogkivonat érvényes, az API-ban.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dinamikus hatóköreinek növekményes jóváhagyás kérése
1.0-s verziójú használó alkalmazások létrehozását, kellett regisztrálja az alkalmazást a felhasználó jóváhagyást bejelentkezés alkalmával a szükséges engedélyek (statikus hatókörök) teljes készlete. A 2.0-s verziójú kérheti a hatókör-paraméter használatával szükség szerint további engedélyeket. Ezek a hatókörök dinamikus nevezik, és engedélyezi a felhasználó történő növekményes hatókörhöz.

Például először jelentkezzen be a felhasználó szabadon bármilyen hozzáférés megtagadása őket. Később átadásával olvassa el a naptár, a felhasználó a naptár hatókör beolvasási jogkivonat módszerek igénylésével és a felhasználó beleegyezését kérő.

Például: `https://graph.microsoft.com/User.Read` és `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Csendes módban (a gyorsítótárból)-jogkivonatok beszerzésének
Az MSAL tartja karban a jogkivonatok gyorsítótárának (vagy a bizalmas ügyfélalkalmazások két gyorsítótárak), és gyorsítótárazza a jogkivonat után azt beszerezte.  Sok esetben egy token csendes beolvasására tett kísérlet fogják beszerezni egy másik jogkivonatot a jogkivonat a gyorsítótárban alapján további hatókörökkel. Emellett akkor is képes egy jogkivonat frissítését, amikor azt hamarosan lejár az első (mivel a jogkivonatok gyorsítótárának is tartalmaz egy frissítési jogkivonat).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Nyilvános ügyfélalkalmazások ajánlott hívás mintája
Alkalmazáskód egy token beszerzéséhez csendes módban (a gyorsítótárból), először próbálja meg.  Ha a metódus meghívása egy "Felhasználói felület szükséges" hiba vagy kivételt ad vissza, próbálja meg egy token beszerzése a más módon. 

Vannak azonban két folyamatok, ameddig meg **nem** csendes a jogkivonat-beszerzési kísérlet:

- [ügyfél-hitelesítő adatok folyamat](msal-authentication-flows.md#client-credentials), amely nem használja a felhasználói jogkivonatok gyorsítótárát, de az alkalmazás tokengyorsítótárral. Ez a módszer az alkalmazás tokengyorsítótárral ellenőrzése előtt az STS-re történő kérelemküldés gondoskodik.
- [az engedélyezési kód folyamata](msal-authentication-flows.md#authorization-code) Web Apps, a, mert visszaváltja a kódot, amely az alkalmazás a bejelentkezés a felhasználói és azok van több hatókört jóváhagyás. A kódot egy paraméterként, és a fiók nem fogad el, mivel a metódus nem jelenik meg a gyorsítótár előtt váltja be a kódot, amelyhez szükség van, mégis, a szolgáltatás hívást.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Ajánlott hívás minta a Web Apps használata esetén az engedélyezési kód folyamata 
A webes alkalmazásokhoz, amelyek használják a [OpenID Connect hitelesítési kódfolyamat](v2-protocols-oidc.md), a javasolt minta, a vezérlők:

- Hozza létre az egyéni szerializálás a jogkivonatok gyorsítótárának bizalmas ügyfélalkalmazás. 
- Az engedélyezési kód folyamata segítségével a jogkivonat beszerzése

## <a name="acquiring-tokens"></a>-Jogkivonatok beszerzésének
Általában a módszer egy token beszerzése a attól függ, hogy egy nyilvános ügyfél vagy a bizalmas ügyfélalkalmazás.

### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások
A nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazás) akkor:
- Gyakran szerzi be a jogkivonatokat, a felhasználók, jelentkezzen be a felhasználói felület vagy a felugró ablakban.
- Is [csendes egy token beszerzése a bejelentkezett felhasználó](msal-authentication-flows.md#integrated-windows-authentication) integrált Windows-hitelesítés (IWA/Kerberos) használatával, ha az asztali alkalmazást egy Windows-számítógépen fut csatlakozott egy tartományhoz vagy az Azure-bA.
- Is [felhasználónévvel és jelszóval egy token beszerzéséhez](msal-authentication-flows.md#usernamepassword) .NET-keretrendszer asztali ügyfél-alkalmazásokhoz, de ez nem ajánlott. Ne használjon bizalmas ügyfélalkalmazások számára a felhasználónév/jelszó.
- Egy jogkivonat segítségével lekérheti a [eszköz kódfolyamat](msal-authentication-flows.md#device-code) nem rendelkezik egy webes böngésző eszközökön futó alkalmazások. A felhasználó az egy URL-CÍMÉT és a egy kódot, akik ezután egy webes böngésző egy másik eszközön kerül, és írja be a kódot, és bejelentkezik van megadva.  Majd az Azure AD elküldi egy jogkivonatot a böngésző nélküli eszközt.

### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások 
A bizalmas ügyfélalkalmazások (webalkalmazás, webes API-t vagy démon az alkalmazás például egy Windows-szolgáltatás) akkor:
- Szerzi be a jogkivonatokat **magának az alkalmazásnak** és a egy felhasználó esetében nem használja a [ügyfél hitelesítő adatai a flow](msal-authentication-flows.md#client-credentials). Ez szinkronizálja eszközök vagy eszközöket, amelyek általában feldolgozni a felhasználókat és a egy adott felhasználó nem használható. 
- Használja a [a alapú meghatalmazásos folyamat](msal-authentication-flows.md#on-behalf-of) egy webes API-hívás egy API-t a felhasználó nevében. Az alkalmazás, amelynél az ügyfél-hitelesítő adatok annak érdekében, hogy egy felhasználó helyességi feltétel (például vagy a JWT jogkivonat SAML) alapján egy token beszerzéséhez. Ez a folyamat használják az alkalmazásokat, amelyek egy adott felhasználó a szolgáltatások közötti hívások erőforrásokhoz kell hozzáférniük.
- Jogkivonatok segítségével szerez a [hitelesítési kódfolyamat](msal-authentication-flows.md#authorization-code) a web appsben, miután a felhasználó bejelentkezik az engedélyezési keresztül kérelem URL-címe. OpenID Connect alkalmazás általában akkor használják, ez mechanizmust, amely lehetővé teszi, hogy a felhasználó be Open ID connect, és a hozzáférési webes API-kat a felhasználó nevében.


## <a name="authentication-results"></a>Hitelesítési eredmények 
Ha az ügyfél hozzáférési jogkivonatot kér, az Azure AD, az is egy hitelesítés eredménye a hozzáférési jogkivonat néhány metaadatokat tartalmaz, amelyek adja vissza. Ezen információk közé tartozik a hozzáférési jogkivonatot, és a hatóköröket, amelyek esetében érvényes lejárati idejét. Ezek az adatok lehetővé teszi az alkalmazás intelligens gyorsítótárazással hozzáférési jogkivonatokat elemezni a hozzáférési jogkivonat maga nélkül tegye.  A hitelesítés eredménye tünteti fel:

- A [hozzáférési jogkivonat](access-tokens.md) a webes API erőforrások eléréséhez. Ez egy karakterláncérték, általában egy base64 kódolású JWT, de az ügyfél soha nem kell kinéznie a hozzáférési jogkivonat belül. A formátum nem garantált, hogy állandó marad, és titkosíthatók az erőforráshoz. Személyek kód írása függően az ügyfél jogkivonat tartalmának eléréséhez a legnagyobb források hibák és az ügyfél logikai oldaltörések egyike.
- A [azonosító jogkivonat](id-tokens.md) (Ez a jwt-t) a felhasználó számára.
- A jogkivonat lejárati, amely arra kéri a dátum/idő, amikor a jogkivonat érvényessége lejár.
- A bérlő Azonosítóját, amelyben a felhasználó található a bérlő tartalmazza. A vendégfelhasználók számára (Azure AD B2B-forgatókönyvekhez) a Bérlőazonosító a Vendég bérlő, nem a egyedi bérlő. Ha a jogkivonat érkezik be a felhasználó nevét, a a hitelesítés eredménye a felhasználó adatait is tartalmazza. Bizalmas ügyfél folyamatokat, ahol nincs (a az alkalmazás) felhasználói jogkivonatok igényelt a felhasználói adatok má hodnotu null.
- A hatókörök, amelynek a jogkivonat lett kiállítva.
- A felhasználó egyedi azonosítója.

## <a name="next-steps"></a>További lépések
Ismerje meg [hibák és kivételek kezelése](msal-handling-exceptions.md). 
