---
title: "Az Azure Active Directory 2.0 hatókörök, engedélyek és hozzájárulási |} Microsoft Docs"
description: "Az Azure AD v2.0-végpontra, többek között a hatókörök, engedélyek és hozzájárulási engedélyezés leírását."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 04869a7627ecb3e6a0d11733fae7da2ecb04ed51
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Hatókörök, engedélyek és az Azure Active Directory v2.0-végponttól jóváhagyása
Alkalmazásokat, amelyekbe beépül az Azure Active Directory (Azure AD) hajtsa végre az olyan engedélyezési modell, amely lehetőséget ad a felhasználók szabályozhatják, hogyan az alkalmazások is hozzáférjenek az adataikhoz. A használt engedélyezési modellt v2.0 végrehajtásának már frissítve van, és módosítja, hogy kapcsolatba kell lépnie egy alkalmazást az Azure AD. Ez a cikk ismerteti a engedélyezési modell, beleértve a hatókörök, engedélyek és beleegyezése főbb fogalmait és kifejezéseit.

> [!NOTE]
> A v2.0-végpontra nem támogatja az összes Azure Active Directory forgatókönyvek és funkciók. Annak megállapításához, hogy a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek
Az Azure AD valósít meg a [OAuth 2.0](active-directory-v2-protocols.md) hitelesítési protokoll. OAuth 2.0 egy metódust, amelyen keresztül a külső alkalmazások hozzáférhetnek a webkiszolgáló által szolgáltatott erőforrásokhoz egy felhasználó nevében. A webkiszolgáló által szolgáltatott erőforrás, amely az Azure AD tartalmaz egy erőforrás-azonosítót, vagy *Application ID URI*. Például a Microsoft web által szolgáltatott erőforrások közé:

* Az Office 365 levelezési API egységes:`https://outlook.office.com`
* Az Azure AD Graph API:`https://graph.windows.net`
* A Microsoft Graph:`https://graph.microsoft.com`

Ugyanez vonatkozik, amely integrálva van az Azure AD külső erőforrásokat. Ezeket az erőforrásokat is adhat meg, amelyek segítségével a funkciók erőforrás felosztani kisebb csoportjai engedélyekkel. Tegyük fel [Microsoft Graph](https://graph.microsoft.io) többek között a következő feladatok végrehajtására engedélyek definiálva van:

* A felhasználó naptár olvasása
* A felhasználó naptár írása
* E-mailek küldése más felhasználó nevében

Ilyen típusú engedélyek megadásával az erőforrás adatait, és hogyan van téve az adatok minden részletre kiterjedő szabályozhatják rendelkezik. A külső alkalmazások is azokat az engedélyeket kérhet egy alkalmazás felhasználói. Az alkalmazás felhasználó működhet-e az alkalmazás a felhasználó nevében előtt jóvá kell hagynia az engedélyeket. Adattömbösítő kisebb engedélycsoportok funkciókat az erőforrás, amelyet csak a meghatározott engedélyek, hogy a művelet végrehajtására szolgál(nak) kell kéréséhez harmadik felek alkalmazásaihoz építhető ki. Alkalmazás felhasználói tudhatja, pontosan egy alkalmazás általi felhasználásának módja az adatokat, és azokat lehet több abban, hogy az alkalmazás viselkedik rosszindulatú.

Az Azure AD és az OAuth, ezek az engedélyek nevezik *hatókörök*. Akkor is néha nevezzük *oAuth2Permissions*. A hatókör karakterlánc-érték jelzi az Azure ad-ben. A Microsoft Graph példát folytatva, a hatókör minden engedély értéke:

* Olvassa el a felhasználó naptár használatával`Calendar.Read`
* A felhasználó naptár írni használatával`Mail.ReadWrite`
* Egy felhasználó használt által e-maileket küldjön`Mail.Send`

Egy alkalmazás kérhetnek ezeket az engedélyeket ad meg a hatókörök a kéréseket a v2.0-végponttól.

## <a name="openid-connect-scopes"></a>Hatókörök OpenID Connect
Az OpenID Connect v2.0 végrehajtására van néhány jól meghatározott hatókörök nem alkalmazható egy adott erőforrás: `openid`, `email`, `profile`, és `offline_access`.

### <a name="openid"></a>openid
Ha egy alkalmazás segítségével hajtja végre a bejelentkezési [OpenID Connect](active-directory-v2-protocols.md), azt kell igényelnie a `openid` hatókör. A `openid` hatókör jeleníti meg a munkahelyi fiók hozzájárulási oldalán a "Bejelentkezés" engedélyt, és a személyes Microsoft-fiók hozzájárulási oldalán "Profilját és alkalmazások és a Microsoft-fiókjával szolgáltatásokhoz való csatlakozás" engedélyt. Ezzel az engedéllyel, az alkalmazás a felhasználó egyedi azonosítóját fogadhat formájában a `sub` jogcímek. Az alkalmazásnak a hozzáférést a UserInfo végpontnak is biztosít. A `openid` hatóköre lehet használni a v2.0 jogkivonat végpontjához azonosító-jogkivonatokat, amely biztonságos HTTP-hívásokat az alkalmazások különböző összetevői közötti megszerzésére.

### <a name="email"></a>E-mailek
A `email` hatókör használható a `openid` hatókörben és bármely más. Az alkalmazásnak a hozzáférést biztosít a felhasználó elsődleges e-mail címéhez formájában a `email` jogcímek. A `email` jogcím egy jogkivonatba tartalmazza, csak akkor, ha egy e-mail-címmel társítva a felhasználói fiók, amely nem mindig a helyzet. Ha használja a `email` hatókör, kezelni olyan esetben, amikor az alkalmazás kell készíteni a `email` jogcímet a jogkivonat nem létezik.

### <a name="profile"></a>Profil
A `profile` hatókör használható a `openid` hatókörben és bármely más. Az alkalmazásnak a hozzáférést ad a felhasználó adatai jelentős mennyiségű. Az adatokat, hogy elérhesse közé tartoznak, de nem kizárólag a felhasználó utóneve, Vezetéknév, elsődleges felhasználónév és objektum azonosítójával. Egy adott felhasználó a id_tokens paraméter elérhető profil jogcímek teljes listáját lásd: a [v2.0 jogkivonatok hivatkozás](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
A [ `offline_access` hatókör](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) hozzáférést biztosít a az alkalmazás erőforrások a felhasználó nevében hosszabb ideig. Munkahelyi fiók hozzájárulási megjelenik a lapon, a hatókör "Az adatok elérése bármikor" engedélyt. A személyes Microsoft fiók hozzájárulási lapon jelenik meg a hozzáférés-adataihoz bármikor"engedéllyel. Amikor a felhasználó elfogadja a `offline_access` hatókör, az alkalmazás fogadhat frissítési jogkivonatokat a v2.0 jogkivonat végpontjához. Frissítési jogkivonatok hosszú élettartamú. Az alkalmazás kérheti le új jogkivonatot, ahogyan a régieket érvényessége lejár.

Ha az alkalmazás nem kér a `offline_access` hatókör, hogy nem kapja a frissítési jogkivonatokat. Ez azt jelenti, hogy ha egy engedélyezési kódot beváltja az [OAuth 2.0 hitelesítésikód-folyamata](active-directory-v2-protocols.md), csak a hozzáférési tokent kap a `/token` végpont. A hozzáférési jogkivonat érvénytelen rövid időre. A hozzáférési jogkivonat általában egy óra múlva lejár. Biztonsági at, hogy a pont, az alkalmazás kell átirányítja a felhasználót a `/authorize` végpontot, kérjen új hitelesítési kódot. Az átirányítás, attól függően, hogy az alkalmazás, típusa során a felhasználó előfordulhat, hogy szükség írja be újra a hitelesítő adataikat, vagy újra járul hozzá az engedélyeket.

Kérheti le és használja a frissítési jogkivonatokat kapcsolatos további információkért lásd: a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói hozzájárulás kérése
Az egy [OpenID Connect vagy OAuth 2.0](active-directory-v2-protocols.md) engedélyt kér, egy alkalmazás kérheti a szükséges engedélyekkel a `scope` lekérdezési paraméter. Például amikor egy felhasználó bejelentkezik egy alkalmazást, az alkalmazás küld kérelmet tetszés az alábbi példa (olvashatóságát hozzá sortörést):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

A `scope` paraméter, amely az alkalmazás által kért hatókörök szóközökkel elválasztott listáját. Minden hatókör jelzi a hatókör értéke Hozzáfűzés, az erőforrás-azonosítója (az alkalmazás azonosítója URI). A kérelem a példában az alkalmazás olvassa a felhasználói naptár és a felhasználó e-maileket küldjön engedélyre van szüksége.

Után a felhasználó megadja a hitelesítő adatokat, a v2.0-végpontra keres egy egyező rekordot *felhasználói hozzájárulás*. Ha a felhasználó nem hozzájárult a kért engedélyek a múltban, a v2.0-végpontra megkérdezi a felhasználót, hogy a kért engedélyeket.

![Munkahelyi fiók hozzájárulás](../../media/active-directory-v2-flows/work_account_consent.png)

Amikor a felhasználó elfogadja az engedély, a hozzájárulási, hogy a felhasználó nem rendelkezik a későbbi bejelentkezések újra beleegyezését rögzíti.

## <a name="requesting-consent-for-an-entire-tenant"></a>Egy teljes bérlő hozzájárulás kérése
Gyakran Ha egy szervezet vásárol egy licenc- vagy egy alkalmazás-előfizetés, a szervezet által az alkalmazottak számára az alkalmazás teljesen kiépítéséhez. Ez a folyamat részeként egy rendszergazda biztosíthat az alkalmazás bármelyik munkavállaló nevében végezze a hozzájárulásukat adják. Ha a rendszergazda a teljes bérlő hozzájárulási biztosít, az a szervezeti alkalmazottak számára az alkalmazás hozzájárulási lap nem jelenik meg.

A bérlő lévő összes felhasználó számára kérelmezni kér, az alkalmazás segítségével a rendszergazda jóváhagyását végpont.

## <a name="admin-restricted-scopes"></a>Rendszergazda által korlátozott hatókör
A Microsoft-ökoszisztéma az egyes magas szintű jogosultságokkal állítható be *admin korlátozott*. Az ilyen típusú hatókörök például a következő engedélyekkel:

* Segítségével a szervezetek címtáradatok olvasása`Directory.Read`
* Adatokat írni egy szervezet címtárához használatával`Directory.ReadWrite`
* Olvassa el a munkahely biztonsági csoportok segítségével`Groups.Read.All`

Bár a fogyasztói felhasználó előfordulhat, hogy hozzáférést egy alkalmazáshoz ilyen típusú adat, ugyanazokat a bizalmas vállalati adatokhoz való hozzáférés biztosítása a szervezeti felhasználók korlátozza. Ha az alkalmazás a szervezeti felhasználó hozzáférést kér egy ezeket az engedélyeket, a felhasználó kap egy hibaüzenet, amely szerint nem jogosultak az Alkalmazásengedélyek engedélyezése.

Ha az alkalmazás szervezetek hatókört felügyeleti korlátozott hozzáférésre van szüksége, igényeljen őket a vállalati rendszergazda közvetlenül a is leírtak alapján. az admin jóváhagyását végpont használatával.

A rendszergazda engedélyezi ezeket az engedélyeket a rendszergazda jóváhagyását végpont keresztül, hozzájárulási jár a bérlő összes felhasználója számára.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazda jóváhagyását végpont használatával
Kövesse az alábbi lépéseket, ha az alkalmazás gyűjthet bérlőjében, beleértve a rendszergazda által korlátozott hatókörök minden felhasználó engedélyeit. A kódminta, amely megvalósítja a lépéseket, olvassa el a [admin korlátozott hatókörök minta](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon engedélyek kéréséhez
1. Keresse fel az alkalmazást a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy [hozzon létre egy alkalmazást](active-directory-v2-app-registration.md) Ha még nem tette meg.
2. Keresse meg a **Microsoft Graph engedélyek** szakaszt, és adja meg az alkalmazáshoz szükséges engedélyeket.
3. Győződjön meg arról, hogy **mentése** az alkalmazás regisztrálása.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: Beléptetni a felhasználót az alkalmazáshoz
Általában a rendszergazda jóváhagyását végpont használó alkalmazás építésekor az alkalmazás egy lap vagy igényel, amelyben a rendszergazda jóváhagyhatja a Alkalmazásengedélyek megtekintése. Ezen a lapon lehet az alkalmazás-előfizetési folyamat, az alkalmazás beállításokban része, vagy egy dedikált "Csatlakozás" folyamat lehet. Sok esetben érdemes ezt az alkalmazást a "Csatlakozás" nézet csak, miután egy felhasználó a munkahelyi vagy iskolai Microsoft-fiókkal van bejelentkezve.

Amikor bejelentkezik a felhasználó az alkalmazáshoz, azonosíthatja a szervezet, amelyhez a rendszergazda kéri a felhasználót a szükséges engedélyek jóváhagyása előtt tartozik. Bár nem feltétlenül szükséges, ez segíthet a szervezeti felhasználók intuitívabb környezetet. Írja alá a felhasználót, hajtsa végre a [v2.0 protokoll oktatóanyagok](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Az engedélyeket kérhet a directory-rendszergazda
Amikor készen áll a szervezet felügyeleti engedélyeket kérhet, akkor is átirányítja a felhasználót a v2.0 *rendszergazda jóváhagyását végpont*.

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

| Paraméter | Az állapot | Leírás |
| --- | --- | --- |
| Bérlői |Szükséges |A directory-bérlőt, amelyet az engedélyt. Megadható a GUID vagy rövid név formátumban. |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz hozzárendelni. |
| redirect_uri |Szükséges |Az átirányítási URI, ha azt szeretné, hogy a válasz küldését az alkalmazások kezeléséhez. Ez pontosan egyeznie kell az átirányítási URI-k, az alkalmazás regisztrációs portálon regisztrált. |
| state |Ajánlott |A kérelemhez, amely a token válaszul is visszaadott szerepel érték. Bármilyen tartalmat karakterlánc lehet. Az állapot használata az alkalmazás a felhasználói állapot információt kódolására, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a. |

Az Azure AD ezen a ponton a bérlői rendszergazda bejelentkezhet a kérés teljesítéséhez szükséges. A rendszergazda felkéri, hogy hagyja jóvá az alkalmazás az app-regisztrációs portálon kért összes engedélyt.

#### <a name="successful-response"></a>A sikeres válasz
Ha a rendszergazda engedélyeit, az alkalmazás jóváhagyása, a sikeres válasz jelenik meg:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- | --- |
| Bérlői |A directory-bérlőhöz, amely engedéllyel rendelkezik az alkalmazás a kért, GUID formátumban. |
| state |A kérelem is visszaad a biztonságijogkivonat-válaszban szereplő érték. Bármilyen tartalmat karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a használatos. |
| admin_consent |Úgy lesz beállítva, **igaz**. |

#### <a name="error-response"></a>Hibaválaszba
Ha a rendszergazda nem hagyja jóvá az engedélyek beállítása az alkalmazáshoz, a sikertelen válasz jelenik meg:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- | --- |
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hiba okának azonosításához. |

A rendszergazda jóváhagyását végpont már a sikeres válasz érkezett, miután az alkalmazás a kért engedélyeket köszönhetően. Ezután a kívánt erőforráshoz tartozó jogkivonatot kérhet.

## <a name="using-permissions"></a>Engedélyek használata
Miután a felhasználó hozzájárul engedélyeinek beállítása az alkalmazáshoz, az alkalmazás szerezhetnek be a hozzáférési jogkivonatok, amelyek megfelelnek az alkalmazás engedély az egyes kapacitás erőforrások eléréséhez. Olyan hozzáférési jogkivonatot csak egyetlen használható, de a hozzáférési jogkivonat belül kódolású minden engedélyt, hogy az alkalmazás rendelkezik az adott erőforráshoz. Olyan hozzáférési jogkivonatot szerezni, az alkalmazás képes indítson egy lekérdezést a v2.0 jogkivonat végpontjához, ehhez hasonló:

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

Az eredményül kapott hozzáférési jogkivonat használható HTTP-kérelmek az erőforráshoz. Azt megbízhatóan jelzi az erőforráshoz, hogy az alkalmazás rendelkezik-e a megfelelő engedéllyel az adott feladat végrehajtásához.  

Az OAuth 2.0 protokollt és a hozzáférési jogkivonatok beolvasásával kapcsolatos további információkért lásd: a [v2.0 protokoll végponthivatkozás](active-directory-v2-protocols.md).
