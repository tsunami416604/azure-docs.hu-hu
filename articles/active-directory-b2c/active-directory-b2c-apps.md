---
title: Az Azure Active Directory B2C-vel is használható alkalmazások típusait |} A Microsoft Docs
description: További információ a típusú alkalmazások az Azure Active Directory B2C segítségével használhatja.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7671a0a99e12463fcce5ff33fbcba7e8677dde05
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006194"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Az Active Directory B2C-ben használható alkalmazások típusok

Az Azure Active Directory (Azure AD) B2C számos a modern alkalmazásarchitektúráknak, támogatja a hitelesítést. Ezek mindegyike az iparági szabványnak számító [OAuth 2.0](active-directory-b2c-reference-protocols.md) vagy [OpenID Connect](active-directory-b2c-reference-protocols.md) protokollon alapul. Ez a dokumentum ismerteti, milyen típusú alkalmazásokat hozhat létre, a nyelvtől és platformtól független inkább. Emellett segít jobban megérteni a magas szintű forgatókönyveket, mielőtt elkezdené az alkalmazások létrehozásához.

Minden Azure AD B2C-t használó alkalmazást regisztrálni kell a [Azure AD B2C-bérlő](active-directory-b2c-get-started.md) használatával a [az Azure Portal](https://portal.azure.com/). Az alkalmazás regisztrációs folyamatának gyűjt, és hozzárendeli az értékeket, például:

* Egy **Alkalmazásazonosító** , amely egyedileg azonosítja az alkalmazást.
* A **válasz URL-cím** , amely közvetlen válaszokhoz az alkalmazáshoz használható.

Az Azure AD B2C-nek küldött kérések meghatároznak egy **szabályzatot** is. A szabályzatok vezérlik az Azure AD működését. A végpontok segítségével ezenfelül testre szabható felhasználói élmények is létrehozhatók. Gyakran használt szabályzatok például a következők: regisztráció, bejelentkezés, profilmódosítás és így tovább. Ha nincs tisztában a szabályzatokkal, a folytatás előtt olvasson utána az Azure AD B2C [bővíthető szabályzat-keretrendszernek](active-directory-b2c-reference-policies.md).

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás irányítja a felhasználót a v2.0-végpont végrehajtásához egy [házirend](active-directory-b2c-reference-policies.md).
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás egy biztonsági jogkivonatot kap a v2.0-végpontra.
4. Az alkalmazás a biztonsági jogkivonatot használ a védett adatokat vagy egy védett erőforrás eléréséhez.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

Ezeket a lépéseket készít alkalmazás típusától függően némileg eltérőek lehetnek.

## <a name="web-applications"></a>Webalkalmazások

Webes alkalmazások (beleértve a .NET, PHP, Java, Ruby, Python és Node.js), amelyek a kiszolgálón futtatott és böngészőn keresztül elért, az Azure AD B2C-t támogatja a [OpenID Connect](active-directory-b2c-reference-protocols.md) összes felhasználói élmény esetében. Ide tartozik a bejelentkezés, a regisztráció és a profilkezelés is. OpenID Connect Azure AD B2C-vel végrehajtásában a webes alkalmazások különböző felhasználói élményeket az Azure ad Szolgáltatásba küldött hitelesítési kérések alapján indítja el. A kérés eredménye egy `id_token`. Ez a biztonsági jogkivonat tartalmazza a felhasználó identitását. Ezenfelül jogcímek formájában információkat nyújt a felhasználóról is:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

További információ az elérhető jogkivonatok és jogcímek az alkalmazáshoz való típusú a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

Egy webalkalmazás, minden egyes végrehajtása a egy [házirend](active-directory-b2c-reference-policies.md) veszi ezeket a magas szintű lépéseket:

1. A felhasználó megnyitja a webalkalmazáshoz.
2. A webes alkalmazás átirányítja a felhasználóknak az Azure AD B2C-vel a szabályzat végrehajtásához.
3. A felhasználó elvégzi a házirend.
4. Az Azure AD B2C visszaadja az `id_token` a böngészőben.
5. A `id_token` be az átirányítási URI-t.
6. A `id_token` érvényesítése és a egy munkamenetcookie-t be van állítva.
7. A felhasználónak egy biztonságos lap küld vissza.

A felhasználó identitásának ellenőrzéséhez elegendő az Azure AD-tól kapott nyilvános aláírókulcs segítségével ellenőrizni a `id_token`érvényességét. Ezzel egyúttal beállít egy munkamenetcookie-t is, amely a következő lapkérések során azonosítja a felhasználót.

Ebben a forgatókönyvben működés közben látni, próbálja meg a webes alkalmazás bejelentkezést kódmintái a egyikét a [kezdeti lépéseket bemutató cikkből](active-directory-b2c-overview.md).

Amellett, hogy megkönnyítsék a bejelentkezés, egy webkiszolgáló alkalmazást is hozzá kell egy háttér-webszolgáltatást. Ebben az esetben a webalkalmazást egy némileg különböző hajthat végre [OpenID Connect-folyamat](active-directory-b2c-reference-oidc.md) és szerzi be a jogkivonatokat a hitelesítési kódok és frissítési jogkivonatok. Ezt a folyamatot a következő, [Webes API-k](#web-apis) című fejezet írja le.

## <a name="web-apis"></a>Webes API-k

Azure AD B2C segítségével webes szolgáltatásokhoz, például az alkalmazás RESTful webes API biztonságossá tétele. A webes API-k az OAuth 2.0 használatával biztosíthatják az adatok védelmét a bejövő HTTP-kérések jogkivonatokkal történő hitelesítésével. A webes API hívója hozzáfűz egy jogkivonatot a HTTP-kérés hitelesítési fejlécéhez:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Így a webes API a jogkivonat segítségével ellenőrizheti az API hívójának identitását, valamint a jogkivonatban kódolt jogcímek segítségével további információkhoz juthat a hívóról. Az [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md) további információkat tudhat meg az alkalmazásban elérhető jogkivonatok és jogcímek különböző típusairól.

Webes API-k számos különböző típusú, beleértve a webalkalmazásokat, asztali és mobil alkalmazások, egyoldalas alkalmazások, kiszolgálóoldali démonok és más webes API-kat a képes jogkivonatokat fogadni. Íme egy példa, amely meghívja a webes API-k webalkalmazás teljes folyamatára:

1. A webes alkalmazás végrehajt egy szabályzatot, és a felhasználó elvégzi a felhasználói élményt.
2. Az Azure AD B2C visszaadja az `access_token` és a egy hozzáférési kóddal a böngészőben.
3. A böngésző bejegyzések a `access_token` és az átirányítási URI-t az engedélyezési kód.
4. A webalkalmazás-kiszolgáló ellenőrzi a `access token` és a egy munkamenetcookie-t állítja be.
5. A `access_token` példáira Azure AD B2C az engedélyezési kódot, az alkalmazás ügyfél-Azonosítót és hitelesítő adatait.
6. A `access_token` és `refresh_token` visszakerülnek a webkiszolgálón.
7. A webes API-k hívása a `access_token` az engedélyeztetési fejléc.
8. A webes API érvényesíti a jogkivonatot.
9. A webkiszolgáló biztonságos adatokat küld vissza.

Ha többet szeretne tudni a hitelesítési kódokról, frissítési jogkivonatokról, valamint a jogkivonatok lekérésének lépéseiről, olvasson az [OAuth 2.0-protokollról](active-directory-b2c-reference-oauth-code.md).

Ha szeretné megtanulni, hogyan biztosíthat védelmet a webes API-k számára az Azure AD B2C segítségével, olvassa el a [kezdeti lépéseket bemutató cikk](active-directory-b2c-overview.md) webes API-kra vonatkozó oktatóanyagát.

## <a name="mobile-and-native-applications"></a>Mobil- és natív alkalmazások

Az eszközök, például az asztali és mobil alkalmazások, telepített alkalmazások gyakran igényelnek hozzáférést Háttérszolgáltatásokhoz vagy webes API-k felhasználók nevében. Egyedi identitáskezelési eszközöket adhat a natív alkalmazásokhoz, és biztonságosan meg háttér-szolgáltatásokat az Azure AD B2C-vel és a [OAuth 2.0 hitelesítési kódfolyamat](active-directory-b2c-reference-oauth-code.md).  

Ezt a folyamatot, az alkalmazás végrehajt [házirendek](active-directory-b2c-reference-policies.md) és kap egy `authorization_code` után a felhasználó teljesítette a szabályzat az Azure AD-ből. A `authorization_code` jelöli a háttér-szolgáltatásokat az aktuálisan bejelentkezett felhasználó nevében az alkalmazás számára. Az alkalmazás ezután tudjon cserélni a `authorization_code` a háttérben egy `id_token` és a egy `refresh_token`.  Az alkalmazás használhatja a `id_token` egy háttér-webes API-nak a HTTP-kérések hitelesítéséhez. Az `refresh_token` alkalmas ezenfelül új `id_token` kérésére is, ha a régi lejárna.

## <a name="current-limitations"></a>Aktuális korlátozások

### <a name="application-not-supported"></a>Az alkalmazás nem támogatott 

#### <a name="daemonsserver-side-applications"></a>Démonok/kiszolgálóoldali alkalmazások

Alkalmazások, amelyek tartalmazzák a hosszú futású folyamatok, illetve a felhasználó jelenléte nélkül is kell oly módon, védett erőforrások, például webes API-k eléréséhez. Ezek az alkalmazások hitelesítheti és a jogkivonatok lekérésére, az alkalmazás azonosítóját (helyett, hogy a felhasználó delegált identitása) használatával, és az OAuth 2.0-ügyfél használatával hitelesítő adatokat a folyamat. Ügyfél hitelesítő adat flow jelenleg nem ugyanaz, mint nevében flow és a nevében folyamat nem használható a kiszolgálók közötti hitelesítéshez.

Bár az ügyfél-hitelesítő adat flow jelenleg nem támogatott az Azure AD B2C által, beállíthatja az Azure AD ügyfél-hitelesítő adat folyamatát. Azure AD B2C-bérlő bizonyos funkciók osztja meg az Azure ad-ben nagyvállalati bérlők számára.  Az ügyfél-hitelesítő adat folyamatát az Azure AD-funkciókról, az Azure AD B2C-bérlő használata támogatott. 

Ügyfél-hitelesítő adat folyamat beállításával kapcsolatban lásd: [Azure Active Directory 2.0-s verziójú, az OAuth 2.0-ügyfél hitelesítő adatait a flow](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). A sikeres hitelesítés eredményezi, hogy a használat az Azure AD által ismertetett módon formázott jogkivonat kézhezvételét [az Azure AD-jogkivonatok referenciájából](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webes API-láncok (meghatalmazásos folyamat)

Számos architektúrában szerepelnek olyan webes API-k, amelyek más, alsóbb rétegbeli webes API-kat hívnak meg, és mindkét API biztonságát az Azure AD B2C garantálja. Ez gyakori a webes API-háttérrel rendelkező natív ügyfelek esetében. Ez aztán meghív egy Microsoft online szolgáltatást, például az Azure AD Graph API-t.

Ez a láncolatba fűzött webes API-megoldás az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával (vagy más néven a meghatalmazásos folyamat) segítségével valósítható meg.  A meghatalmazásos folyamatot azonban még nem implementáltuk az Azure AD B2C-be.

### <a name="reply-url-values"></a>Válasz URL-címértékekre

Az Azure AD B2C-vel regisztrált alkalmazások jelenleg csak meghatározott válasz URL-címértékekre korlátozódnak. A webalkalmazások és -szolgáltatások válasz URL-címének a `https`-sémával kell kezdődnie, és valamennyi válasz URL-címértéknek egyetlen DNS-tartományba kell tartoznia. Például nem regisztrálhat olyan webalkalmazást, amely az alábbi URL-címek egyikével rendelkezik:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő válasz URL-cím teljes DNS-nevét a hozzáadni kívánt válasz URL-cím DNS-nevével. A DNS-név hozzáadására vonatkozó kérést nem lehet teljesíteni, ha az alábbi körülmények valamelyike fennáll:

- Az új válasz URL-cím teljes DNS-neve nem egyezik meg a meglévő válasz URL-cím DNS-nevével.
- Az új válasz URL-cím teljes DNS-neve nem tartozik a meglévő válasz URL-cím altartományába.

Ha például az alkalmazás válasz URL-címe a következő:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha azt szeretné, hogy az alkalmazás a login-east.contoso.com vagy a login-west.contoso.com válasz URL-címmel rendelkezzen, ezeket a válasz URL-címeket az alábbi sorrendben kell hozzáadnia:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Hozzáadhatja az utóbbi kettőt, mivel az első válasz URL-cím, a contoso.com altartományába tartoznak. 

Mobil-/ natív alkalmazás létrehozásakor megadhat egy **átirányítási URI-t** helyett egy **visszajátszását URL-cím**. Két szempontot fontos átirányítási URI-t kiválasztásakor:

- **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követnie. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó kap egy **válassza ki az alkalmazás** párbeszédpanel. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
- **Teljes**: Az átirányítási URI-nak sémával és elérési útvonallal kell rendelkeznie. Az elérési utat a tartomány után legalább egy perjelet tartalmaznia kell. Ha például `//contoso/` működik és `//contoso` sikertelen lesz. Győződjön meg arról, nincsenek különleges karakterek például aláhúzásjeleket az átirányítási URI-t.

### <a name="faulted-apps"></a>Hibás alkalmazások

Az Azure AD B2C-alkalmazások nem szerkeszthetők:

- Egyéb alkalmazáskezelési portálokon, például a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/).
- Graph API-t vagy a PowerShell használatával.

Ha szerkeszti az Azure AD B2C-alkalmazás az Azure Portalon kívül, hibás alkalmazás válik, és már nem használható az Azure AD B2C-vel. Az alkalmazást ekkor törölnie kell, és újra létre kell hoznia.

Az alkalmazás törléséhez lépjen a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/) és törölheti az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

