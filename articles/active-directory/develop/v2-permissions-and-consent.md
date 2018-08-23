---
title: Az Azure Active Directory 2.0-hatókörök, engedélyek és jóváhagyás |} A Microsoft Docs
description: Engedélyezés az Azure AD v2.0-végpont, beleértve a hatókörök, engedélyek és jóváhagyás leírása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 6d3847f547646ae7c62f98b4cee716af5c6ba5e9
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054943"
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Hatókörök, engedélyek és jóváhagyás az Azure Active Directory v2.0-végpont
Alkalmazások, amelyek integrálhatók az Azure Active Directory (Azure AD) egy engedélyezési modellt biztosít a felhasználók hogyan az alkalmazás hozzáférhessen-e adataik felett, kövesse. A rendszer frissítette a 2.0-s verziójú megvalósítása a használt engedélyezési modellt, és hogyan kell működjön az alkalmazás Azure AD-vel változik. Ez a cikk ismerteti az alapvető fogalmait, az engedélyezési modellt, beleértve a hatókörök, engedélyek és jóváhagyás.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek
Az Azure AD valósítja meg a [OAuth 2.0](active-directory-v2-protocols.md) engedélyezési protokollt. OAuth 2.0 a egy módszer, amelyen keresztül egy harmadik féltől származó alkalmazások hozzáférhetnek a web-ban üzemeltetett erőforrásokhoz egy felhasználó nevében. Bármely, amely integrálható az Azure AD-ban üzemeltetett web erőforrás rendelkezik egy erőforrás-azonosító, vagy *Alkalmazásazonosító URI-ja*. Ha például a Microsoft web-ban üzemeltetett erőforrások többek között:

* Az Office 365 Posta API egyesített: `https://outlook.office.com`
* Az Azure AD Graph API: `https://graph.windows.net`
* A Microsoft Graph: `https://graph.microsoft.com`

Ugyanez érvényes, amely integrálva van az Azure AD külső erőforrásokat. A következő erőforrások is adhatja meg, hogy a funkcionalitás az erőforrás szeletekre használható engedélyek egy készletét. Tegyük fel [Microsoft Graph](https://graph.microsoft.io) definiált engedélyek, többek között a következő feladatokat végezheti el:

* Olvassa el a felhasználó-naptár
* A felhasználó naptárba írása
* E-mailek küldése más felhasználó nevében

Az ilyen típusú engedélyek megadásával az erőforrás rendelkezik részletesen szabályozhatja az adatok, valamint azt, hogyan érhető el az adatok. Egy harmadik féltől származó alkalmazások is ezeket az engedélyeket kérhet egy alkalmazás felhasználói. Az alkalmazás felhasználói jóvá kell hagynia az engedélyeket, mielőtt az alkalmazás a felhasználó nevében működni képes. Darabolás kisebb engedélycsoportok az erőforrás funkciókat, a harmadik féltől származó alkalmazások csak a függvény végrehajtásához szükséges konkrét engedélyeket kérhet építhetők fel. Alkalmazás felhasználóinak tudhatja, pontosan hogyan alkalmazás fogja használni az adatokat, és biztos lehet, hogy az alkalmazás viselkedik rosszindulatú is lehet.

Az Azure ad-ben és az OAuth, az engedélyeket az ilyen típusú úgynevezett *hatókörök*. Akkor is vannak néven *oAuth2Permissions*. A hatókör az Azure ad-ben jelenik meg egy karakterláncértéket. A Microsoft Graph példa folytatása, a hatókör minden egyes engedély értéke:

* Olvassa el a felhasználó naptár használatával `Calendars.Read`
* A felhasználó naptár írni használatával `Calendars.ReadWrite`
* E-mailek küldésére, egy felhasználó használatával `Mail.Send`

Egy alkalmazás ezeket az engedélyeket kérhetnek a hatóköröket a v2.0-végpontra irányuló megadásával.

## <a name="openid-connect-scopes"></a>OpenID Connect hatókörök
OpenID Connect v2.0 végrehajtására van néhány jól meghatározott hatókörök nem alkalmazható egy adott erőforrás: `openid`, `email`, `profile`, és `offline_access`.

### <a name="openid"></a>openid
Ha egy alkalmazás segítségével hajtja végre a bejelentkezési [OpenID Connect](active-directory-v2-protocols.md), azt kell igényelnie a `openid` hatókör. A `openid` hatókör jeleníti meg a munkahelyi fiók hozzájárulást kérő lap, a "Bejelentkezés" engedéllyel, és a személyes Microsoft fiók hozzájárulást kérő lap, a "Saját profil megtekintése és a Microsoft-fiókját használó alkalmazásokhoz és szolgáltatásokhoz való csatlakozás" engedéllyel. Ezzel az engedéllyel, egy alkalmazás akkor fogadhat egy egyedi azonosítót a felhasználó formájában a `sub` jogcím. Azt is hozzáférést biztosít az alkalmazás a UserInfo végpontra. A `openid` hatókör azonosító-jogkivonatokat, ami használható alkalmazás különböző összetevői közötti HTTP-hívások biztonságossá tételéhez beszerzésére használható jogkivonat 2.0-s verziójú végpontján.

### <a name="email"></a>e-mailben
A `email` hatókör használható a `openid` hatókörrel és a többi. Az alkalmazás-hozzáférés a felhasználó elsődleges e-mail címének formájában nyújt a `email` jogcím. A `email` jogcím szerepel egy token csak akkor, ha e-mail-címmel társítva, a felhasználói fiókkal, amely nem mindig a helyzet. Ha az a `email` hatókör, az alkalmazás fel kell készülnöm kezelésére egy esetet, amelyben a `email` jogcím nem szerepel a jogkivonatban.

### <a name="profile"></a>profil
A `profile` hatókör használható a `openid` hatókörrel és a többi. Jelentős mennyiségű felhasználóval kapcsolatos információkat nyújt az alkalmazás eléréséhez. Az hozzá tudjon férni információkat tartalmaz, de nem korlátozódik, a felhasználó Utónév, Vezetéknév, elsődleges felhasználónév és objektum azonosítója. Egy adott felhasználó a id_tokens paraméter elérhető profil jogcímek teljes listáját lásd: a [2.0-s verziójú jogkivonatok referencia](v2-id-and-access-tokens.md).

### <a name="offlineaccess"></a>offline_access
A [ `offline_access` hatókör](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) lehetővé teszi az alkalmazás hozzáférjen az erőforrásokhoz a felhasználó nevében hosszabb ideig. A munkahelyi fiók hozzájárulást kérő lap a hatókör megjelenik az "Az adatok elérése bármikor" engedélyt. A személyes Microsoft fiók hozzájárulást kérő lap jelenik meg a "Saját adatok elérése bármikor" engedéllyel. Amikor egy felhasználó jóváhagyja a `offline_access` hatókör, az alkalmazás frissítési biztonsági jogkivonat kap a v2.0 jogkivonat-végpont. Frissítési jogkivonatok olyan hosszú élettartamú. Az alkalmazás is szerezhet új hozzáférési jogkivonatok jár le a régieket.

Ha az alkalmazás nem kér a `offline_access` hatókör, hogy nem kapja meg frissítési biztonsági jogkivonat. Ez azt jelenti, hogy ha a hozzáférési kód beváltása az [OAuth 2.0 hitelesítési kódfolyamat](active-directory-v2-protocols.md), csak a hozzáférési jogkivonatot kap a `/token` végpont. A hozzáférési jogkivonat érvénytelen, rövid ideig. A hozzáférési jogkivonatot általában egy óra múlva lejár. Biztonsági másolatot at, hogy pont, az alkalmazás kell átirányítja a felhasználót a `/authorize` végpontot, hogy egy új hozzáférési kód lekérése. Az átirányítás, az alkalmazás típusától függően során a felhasználó előfordulhat, hogy szükség írja be újra a hitelesítő adatait, vagy újra járul hozzá az engedélyeket.

Letöltheti a frissítési biztonsági jogkivonat kapcsolatos további információkért lásd: a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

## <a name="accessing-v10-resources"></a>1.0-s verziójú erőforrások elérése
2.0-s verziójú alkalmazások jogkivonatok igényelheti és hozzájárulás 1.0-s verziójú alkalmazások (például a Power bi API `https://analysis.windows.net/powerbi/api` vagy a Sharepoint API `https://{tenant}.sharepoint.com`).  Ehhez az alkalmazás URI-t és a hatókör karakterláncát az is lehet hivatkozni a `scope` paraméter.  Ha például `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All` lenne kérése a Power bi `View all Datasets` engedélyt az alkalmazás. 

Több engedélyek kéréséhez, fűzze hozzá a teljes URI-t egy-egy szóközzel vagy `+`, pl. `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://analysis.windows.net/powerbi/api/Report.Read.All`.  Ez egyaránt kér a `View all Datasets` és `View all Reports` engedélyeket.  Vegye figyelembe, hogy az összes Azure AD-hatókörök és engedélyek alkalmazások csak kezdeményezhetik kérést egy erőforrás - egyszerre ezért a kérelem `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://api.skypeforbusiness.com/Conversations.Initiate`, amely kér, mind a Power bi `View all Datasets` engedélyt, és a Skype vállalati verzió `Initiate conversations` engedélyt, a rendszer elutasítja a két különböző erőforrások kérő engedélyek miatt.  

### <a name="v10-resources-and-tenancy"></a>1.0-s verziójú erőforrásokat és bérlős üzemmód
Az 1.0-s verziója és a 2.0-s verziójú Azure AD-protokollokat használ egy `{tenant}` paraméter beágyazva az URI-t (`https://login.microsoftonline.com/{tenant}/oauth2/`).  1.0-s verzió szervezeti erőforrások eléréséhez a v2.0-végpont használata esetén a `common` és `consumers` bérlők nem használható, mivel ezeket az erőforrásokat csak elérhetők a szervezeti (Azure AD) fiókok.  Így ezeket az erőforrásokat, csak a bérlő GUID elérésekor vagy `organizations` lehet használni a `{tenant}` paraméter.  

Ha egy alkalmazás megpróbál hozzáférni egy szervezeti 1.0-s verzió-erőforrást egy nem megfelelő bérlőnek a használatával, az alábbihoz hasonló hibát visszaad. 

`AADSTS90124: Resource 'https://analysis.windows.net/powerbi/api' (Microsoft.Azure.AnalysisServices) is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.`


## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói jóváhagyás kérése
Az egy [OpenID Connect vagy az OAuth 2.0-s](active-directory-v2-protocols.md) engedélyezési kérést, egy alkalmazás kérheti a szükséges engedélyeket a `scope` lekérdezési paraméter. Például amikor egy felhasználó bejelentkezik az alkalmazásba, az alkalmazás küld egy kérést például az alábbi példa (a sortörések hozzáadva az olvashatóság érdekében):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

A `scope` paraméter, amely az alkalmazás által kért hatókörök szóközzel elválasztott listáját. Minden hatókör a hatókör értéke Hozzáfűzés, az erőforrás-azonosítója (az Alkalmazásazonosító URI-ja) jelöli. A kérelem a példában az alkalmazás olvassa el a felhasználó naptár, valamint leveleket is küldhet a felhasználóként engedélyre van szüksége.

Miután a felhasználó megadja a hitelesítő adatokat, a v2.0-végpont keres egy egyező rekordja *felhasználói beleegyezés*. Ha a felhasználó nem egyezett bele a kért engedélyek korábban, a v2.0-végpont kéri a felhasználót, hogy adja meg a kért engedélyeket.

![Munkahelyi fiók jóváhagyás](./media/v2-permissions-and-consent/work_account_consent.png)

A felhasználó jóváhagyja az engedélyt, ha a jóváhagyási rögzíti, hogy a felhasználó nem rendelkezik a későbbi bejelentkezések újból jóváhagyást.

## <a name="requesting-consent-for-an-entire-tenant"></a>Egy egész bérlő hozzájárulás kérése
Gyakran előfordul, ha egy szervezet megvásárolja egy licenc vagy egy alkalmazás-előfizetés, a szervezet célja az teljes mértékben kiépíti az alkalmazottak számára az alkalmazás. Ez a folyamat részeként a rendszergazda biztosíthat az alkalmazás bármely alkalmazott nevében való hozzájárulás. A rendszergazda engedélyezi a teljes bérlő jóváhagyás, ha a szervezeti alkalmazottak számára az alkalmazás egy hozzájárulást kérő lap nem jelenik meg.

A bérlő összes felhasználója esetében a jóváhagyás kérése, az alkalmazás használhatja a rendszergazdai jóváhagyás végpontja.

## <a name="admin-restricted-scopes"></a>Korlátozott rendszergazdai hatókörök
Néhány magas szintű jogosultságokkal a Microsoft-ökoszisztéma állítható *korlátozott rendszergazdai*. Az ilyen típusú hatókörök például a következő engedélyekkel:

* Használatával a szervezet címtáradatok olvasása `Directory.Read`
* Adatokat írni a szervezet könyvtár használatával `Directory.ReadWrite`
* Olvassa el a biztonsági csoportokat a szervezet címtárában használatával `Groups.Read.All`

Bár egy fogyasztói felhasználó engedélyezheti egy alkalmazás-hozzáférés az ilyen típusú adat, szervezeti felhasználók korlátozva vannak a ugyanazokat a bizalmas vállalati adatokhoz való hozzáférést. Ha az alkalmazás a szervezeti felhasználó hozzáférést kér az egyik ezeket az engedélyeket, a felhasználó kap olyan hibaüzenetet, amely arról tájékoztat, hogy engedélyt adjanak az engedélyeket az alkalmazás nem jogosultak.

Ha az alkalmazás a szervezetek hatóköreinek rendszergazdai korlátozott hozzáférésre van szüksége, igényeljen őket közvetlenül a egy vállalati rendszergazda is ismertetjük a rendszergazdai jóváhagyás végpont használatával.

Ha a rendszergazda engedélyezi ezeket az engedélyeket a rendszergazdai jóváhagyás végponton keresztül, engedély a bérlő összes felhasználója esetében.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai jóváhagyás végpont használatával
Kövesse az alábbi lépéseket, ha az alkalmazás a bérlő, beleértve a korlátozott felügyeleti hatókört a felhasználók engedélyeinek tudjon gyűjteni. A kódminta, amely megvalósítja a lépéseket, olvassa el a [korlátozott rendszergazdai hatókörök minta](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon az engedélyek kéréséhez
1. Nyissa meg az alkalmazását a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy [hozzon létre egy alkalmazást](quickstart-v2-register-an-app.md) Ha még nem tette.
2. Keresse meg a **Microsoft Graph-engedélyek** szakaszt, és adja hozzá az adott alkalmazáshoz szükséges engedélyeket.
3. Győződjön meg arról, hogy **mentése** az alkalmazás regisztrációját.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: Jelentkezzen be a felhasználót az alkalmazáshoz
Általában a rendszergazdai jóváhagyás végpontot használó alkalmazás létrehozását, ha az alkalmazás kell weblap vagy nézet, amelyben a rendszergazda jóváhagyhatja az Alkalmazásengedélyek. Ezen az oldalon az alkalmazás regisztrációs folyamat, az app-beállításokban, részei lehetnek, vagy egy dedikált "Csatlakozás" folyamat lehet. Sok esetben logikus jelenjen meg ez az alkalmazás "Csatlakozás" nézet csak akkor, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiókkal van bejelentkezve.

Amikor bejelentkezik a felhasználót az alkalmazáshoz, azonosíthatja a szervezet, amelyekhez a rendszergazda abból a szükséges engedélyek jóváhagyása előtt tartozik. Bár ez nem feltétlenül szükséges, segíthet a szervezeti felhasználók intuitívabb környezetet biztosít. A felhasználó jelentkezik, kövesse a [v2.0 protokoll oktatóanyagok](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Az engedélyek kéréséhez a directory-rendszergazda
Ha készen áll a szervezet felügyeleti engedélyeket kérhet, átirányíthatja a felhasználót a v2.0 *rendszergazdai jóváhagyás végpontja*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |A directory-bérlőhöz, amelyet szeretne az engedélyt. A megadott GUID vagy rövid név formátumban, vagy általános hivatkozott "közös" az a példában látható módon. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| redirect_uri |Szükséges |Az átirányítási URI-t a válasz az alkalmazás kezelni kell elküldeni kívánt helyre. Ez pontosan egyeznie kell az átirányítási URI-k, amelyek az alkalmazás regisztrációs portál regisztrált. |
| state |Ajánlott |A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat karakterlánc lehet. Az állapot használatával kódolása a felhasználói állapot az alkalmazás információ előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézetet. |

Ezen a ponton a az Azure AD bérlői rendszergazdával, jelentkezzen be a kérés teljesítéséhez szükséges. A rendszergazda hagyja jóvá a kért az alkalmazás regisztrációs portál az alkalmazáshoz tartozó összes engedélyt kell adnia.

#### <a name="successful-response"></a>A sikeres válasz
Ha a rendszergazda az alkalmazás engedélyeit jóváhagyja, a sikeres válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- | --- |
| bérlő |A directory-bérlővel, amely engedéllyel rendelkezik az alkalmazás a kért, GUID formátumú. |
| state |A kérelem is visszaadott a jogkivonat-válaszban szereplő érték. Bármilyen tartalmat karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |
| admin_consent |Értékre lesz beállítva **igaz**. |

#### <a name="error-response"></a>Hiba történt a válasz
Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- | --- |
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hiba kiváltó okának azonosításához. |

Miután már sikeres válasz érkezett a rendszergazdai jóváhagyás végpontja, az alkalmazás szerzett a kért engedélyeket. Ezt követően kérhet egy jogkivonatot a kívánt erőforrást.

## <a name="using-permissions"></a>Engedélyek használatával
Miután az alkalmazás engedélyeit a felhasználó jóváhagy, az alkalmazás lekérheti a hozzáférési jogkivonatok, amelyek valamilyen formában lévő erőforrások eléréséhez az alkalmazás számára. Hozzáférési jogkivonat csak egyetlen erőforrás használható, de a hozzáférési jogkivonat belül kódolású minden engedélyt, hogy az alkalmazás megkapta-e az adott erőforráshoz. Hozzáférési jogkivonat beszerzése az alkalmazás is győződjön meg arról, egy kérelmet a v2.0 jogkivonat végpontra, ehhez hasonló:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Használhatja az eredményül kapott hozzáférési jogkivonatot a HTTP-kérések az erőforráshoz. Akkor megbízhatóan azt jelzi, hogy az erőforráshoz, hogy az alkalmazás rendelkezik-e a megfelelő jogosultságot biztosítanak egy adott feladat végrehajtásához. 

Az OAuth 2.0 protokollt és a hozzáférési tokenek beszerzése kapcsolatos további információkért lásd: a [v2.0-végpont protokollreferenciáját](active-directory-v2-protocols.md).
