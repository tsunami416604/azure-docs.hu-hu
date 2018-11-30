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
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, jesakowi, justhu
ms.custom: aaddev
ms.openlocfilehash: 5283782188eaebe3997b6de31b087da74cf10486
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620132"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Engedélyek és jóváhagyás az Azure Active Directory v2.0-végpont

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Alkalmazások, amelyek integrálhatók a Microsoft identity platform hajtsa végre egy engedélyezési modellt, amely révén miként adatok elérhetők a felhasználók és rendszergazdák szabályozhatja. Megvalósítása a használt engedélyezési modellt a v2.0-végpont frissítve lett, és hogyan kell együttműködnie egy alkalmazást a Microsoft identity platform változik. Ez a cikk ismerteti az alapvető fogalmait, az engedélyezési modellt, beleértve a hatókörök, engedélyek és jóváhagyás.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft identity platform valósítja meg a [OAuth 2.0](active-directory-v2-protocols.md) engedélyezési protokollt. OAuth 2.0 a egy módszer, amelyen keresztül egy harmadik féltől származó alkalmazások hozzáférhetnek a web-ban üzemeltetett erőforrásokhoz egy felhasználó nevében. Bármely webhelyen üzemeltetett erőforrás, amely integrálható a Microsoft identity platform rendelkezik egy erőforrás-azonosító, vagy *Alkalmazásazonosító URI-ja*. Ha például a Microsoft web-ban üzemeltetett erőforrások többek között:

* A Microsoft Graph: `https://graph.microsoft.com`
* Az Office 365 Posta API: `https://outlook.office.com`
* Az Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Javasoljuk, hogy a Microsoft Graph használata helyett az Azure AD Graph, az Office 365 Posta API, stb.

Ugyanez érvényes, amely integrálva van a Microsoft identity platform külső erőforrásokat. A következő erőforrások is adhatja meg, hogy a funkcionalitás az erőforrás szeletekre használható engedélyek egy készletét. Tegyük fel [Microsoft Graph](https://graph.microsoft.com) definiált engedélyek, többek között a következő feladatokat végezheti el:

* Olvassa el a felhasználó-naptár
* A felhasználó naptárba írása
* E-mailek küldése más felhasználó nevében

Az ilyen típusú engedélyek megadásával az erőforrás rendelkezik részletesen szabályozhatja az adatok, valamint hogyan API funkció érhető el. Egy harmadik féltől származó alkalmazások ezeket az engedélyeket kérhet a felhasználók és rendszergazdák férhetnek hozzá az adatokat, vagy ki kell hagynia a kérelmet, mielőtt az alkalmazás egy felhasználó nevében cselekedhet. Darabolás kisebb engedélycsoportok az erőforrás funkciókat, a harmadik féltől származó alkalmazások csak a függvény végrehajtásához szükséges konkrét engedélyeket kérhet építhetők fel. A felhasználók és rendszergazdák is ismeri, pontosan milyen adatokat az alkalmazás rendelkezik hozzáféréssel, és biztos lehet, hogy ez nem mindig rosszindulatú is lehet. A fejlesztők kell mindig betartja a legalacsonyabb jogosultsági fogalma csak azok az alkalmazások működéséhez szükséges engedélyeket kér.

Az OAuth, az ilyen típusú engedélyek nevezzük *hatókörök*. Ezek gyakran egyszerűen néven *engedélyek*. Engedély a Microsoft identity platform jelenik meg egy karakterláncértéket. A Microsoft Graph példa folytatása, karakterlánc minden egyes engedély értéke:

* Olvassa el a felhasználó naptár használatával `Calendars.Read`
* A felhasználó naptár írni használatával `Calendars.ReadWrite`
* E-mailek küldésére, egy felhasználó használatával `Mail.Send`

Egy alkalmazás leggyakrabban kérelmek ezeket az engedélyeket a hatókörök megadásával a 2.0-s verziójú irányuló authorize végponton. Azonban bizonyos magas jogosultsági engedélyek csak akkor engedélyezhetők rendszergazdai jóváhagyást, és általában a kért és kapott használatával a [rendszergazdai jóváhagyás végpontja](v2-permissions-and-consent.md#admin-restricted-scopes). Látogasson el lapunkra további információért.

## <a name="permission-types"></a>Engedély típusa

A Microsoft identity platform az engedélyek két típusát támogatja: **delegált engedélyek** és **Alkalmazásengedélyek**.

* **Delegált engedélyek** használják az alkalmazásokat, amelyeket a bejelentkezett felhasználó található. Az ilyen alkalmazások esetében a felhasználó vagy rendszergazda járul hozzá a szükséges engedélyeket, hogy az alkalmazás kéréseket és az alkalmazás-e a célként megadott erőforrás hívása esetén a bejelentkezett felhasználó nevében eljárni delegált engedély. Az egyes delegált engedélyeket is beleegyezés a nem rendszergazda jogosultságú felhasználók, de néhány magasabb jogosultsági szintű engedélyeket igényel [rendszergazdai jóváhagyást](v2-permissions-and-consent.md#admin-restricted-scopes). Melyik rendszergazda szerepkörök jóváhagyhat delegált engedélyeket kapcsolatban lásd: [rendszergazdája szerepkör engedélyei az Azure ad-ben](../users-groups-roles/directory-assign-admin-roles.md).

* **Alkalmazásengedélyek** alkalmazások által használt futtató nincs bejelentkezett felhasználó található, például alkalmazások, amelyek futtatását háttérszolgáltatások vagy démonok.  Alkalmazásengedélyek csak akkor lehet [egy rendszergazda által jóváhagyott](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_A hatályos engedélyek_ az engedélyek, amelyek a az alkalmazás lesz, amikor a célként megadott erőforrás-kérelem indítására. Fontos megérteni a különbség a delegált és az alkalmazás számára biztosított Alkalmazásengedélyek és a hatályos engedélyek a célként megadott erőforrás hívása esetén.

- A delegált engedélyeket a _hatályos engedélyek_ az alkalmazás a legalacsonyabb jogosultsági szintű metszet (jóváhagyás) n keresztül kiadott az alkalmazás delegált engedélyeit és a jelenleg bejelentkezett felhasználó jogosultságával lesz. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. A cégeken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagsága határozhatja meg. Melyik rendszergazda szerepkörök jóváhagyhat delegált engedélyeket kapcsolatban lásd: [rendszergazdája szerepkör engedélyei az Azure ad-ben](../users-groups-roles/directory-assign-admin-roles.md).
  Tegyük fel például, hogy az alkalmazáshoz kapott a _User.ReadWrite.All_ delegált engedély. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó egy globális rendszergazda, az alkalmazás frissíteni tudja a cégben lévő összes felhasználó profilját. Ha azonban a bejelentkezett felhasználó nem rendszergazdai szerepkörben van, az alkalmazás csak a bejelentkezett felhasználó profilját tudja frissíteni. Nem tudja frissíteni a cégben lévő többi felhasználó profilját, mert az a felhasználó nem rendelkezik ilyen jogosultságokkal, akinek a nevében eljár.
  
- Az Alkalmazásengedélyek a _hatályos engedélyek_ az alkalmazás lesz a teljes körű szintű jogosultságokat hallgatólagos vonatkozó engedélyeket. Például, ha egy alkalmazást, amely rendelkezik a _User.ReadWrite.All_ alkalmazásnak frissítheti a profilt minden felhasználó a szervezetben. 

## <a name="openid-connect-scopes"></a>OpenID Connect hatókörök

OpenID Connect v2.0 végrehajtására van néhány jól meghatározott hatókörök nem alkalmazható egy adott erőforrás: `openid`, `email`, `profile`, és `offline_access`.

### <a name="openid"></a>openid

Ha egy alkalmazás segítségével hajtja végre a bejelentkezési [OpenID Connect](active-directory-v2-protocols.md), azt kell igényelnie a `openid` hatókör. A `openid` hatókör jeleníti meg a munkahelyi fiók hozzájárulást kérő lap, a "Bejelentkezés" engedéllyel, és a személyes Microsoft fiók hozzájárulást kérő lap, a "Saját profil megtekintése és a Microsoft-fiókját használó alkalmazásokhoz és szolgáltatásokhoz való csatlakozás" engedéllyel. Ezzel az engedéllyel, egy alkalmazás akkor fogadhat egy egyedi azonosítót a felhasználó formájában a `sub` jogcím. Azt is hozzáférést biztosít az alkalmazás a UserInfo végpontra. A `openid` hatókör azonosító-jogkivonatokat, ami használható alkalmazás különböző összetevői közötti HTTP-hívások biztonságossá tételéhez beszerzésére használható jogkivonat 2.0-s verziójú végpontján.

### <a name="email"></a>e-mail

A `email` hatókör használható a `openid` hatókörrel és a többi. Az alkalmazás-hozzáférés a felhasználó elsődleges e-mail címének formájában nyújt a `email` jogcím. A `email` jogcím szerepel egy token csak akkor, ha e-mail-címmel társítva, a felhasználói fiókkal, amely nem mindig a helyzet. Ha az a `email` hatókör, az alkalmazás fel kell készülnöm kezelésére egy esetet, amelyben a `email` jogcím nem szerepel a jogkivonatban.

### <a name="profile"></a>profil

A `profile` hatókör használható a `openid` hatókörrel és a többi. Jelentős mennyiségű felhasználóval kapcsolatos információkat nyújt az alkalmazás eléréséhez. Az hozzá tudjon férni információkat tartalmaz, de nem korlátozódik, a felhasználó Utónév, Vezetéknév, elsődleges felhasználónév és objektum azonosítója. Egy adott felhasználó a id_tokens paraméter elérhető profil jogcímek teljes listáját lásd: a [ `id_tokens` referencia](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

A [ `offline_access` hatókör](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) lehetővé teszi az alkalmazás hozzáférjen az erőforrásokhoz a felhasználó nevében hosszabb ideig. A munkahelyi fiók hozzájárulást kérő lap a hatókör megjelenik az "Az adatok elérése bármikor" engedélyt. A személyes Microsoft fiók hozzájárulást kérő lap jelenik meg a "Saját adatok elérése bármikor" engedéllyel. Amikor egy felhasználó jóváhagyja a `offline_access` hatókör, az alkalmazás frissítési biztonsági jogkivonat kap a v2.0 jogkivonat-végpont. Frissítési jogkivonatok olyan hosszú élettartamú. Az alkalmazás is szerezhet új hozzáférési jogkivonatok jár le a régieket.

Ha az alkalmazás nem kér a `offline_access` hatókör, hogy nem kapja meg frissítési biztonsági jogkivonat. Ez azt jelenti, hogy ha a hozzáférési kód beváltása az [OAuth 2.0 hitelesítési kódfolyamat](active-directory-v2-protocols.md), csak a hozzáférési jogkivonatot kap a `/token` végpont. A hozzáférési jogkivonat érvénytelen, rövid ideig. A hozzáférési jogkivonatot általában egy óra múlva lejár. Biztonsági másolatot at, hogy pont, az alkalmazás kell átirányítja a felhasználót a `/authorize` végpontot, hogy egy új hozzáférési kód lekérése. Az átirányítás, az alkalmazás típusától függően során a felhasználó előfordulhat, hogy szükség írja be újra a hitelesítő adatait, vagy újra járul hozzá az engedélyeket.

Letöltheti a frissítési biztonsági jogkivonat kapcsolatos további információkért lásd: a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

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

A `scope` paraméter, amely az alkalmazás által kért delegált engedélyeket szóközzel elválasztott listáját. Minden egyes engedély Hozzáfűzés, az erőforrás-azonosítója (az Alkalmazásazonosító URI-ja) engedély értékét jelöli. A kérelem a példában az alkalmazás olvassa el a felhasználó naptár, valamint leveleket is küldhet a felhasználóként engedélyre van szüksége.

Miután a felhasználó megadja a hitelesítő adatokat, a v2.0-végpont keres egy egyező rekordja *felhasználói beleegyezés*. Ha a felhasználó nem egyezett bele a kért engedélyek a múltban, és nem a rendszergazda hozzájárult ezeket az engedélyeket az egész munkahely nevében megkérdezi a felhasználót a v2.0-végpont, a kért engedélyek megadására.

![Munkahelyi fiók jóváhagyás](./media/v2-permissions-and-consent/work_account_consent.png)

Amikor a felhasználó a engedély kérelmet jóváhagyja, jóváhagyás rögzíti, és a felhasználó nem rendelkezik a későbbi bejelentkezések az alkalmazásnak újra jóváhagyást.

## <a name="requesting-consent-for-an-entire-tenant"></a>Egy egész bérlő hozzájárulás kérése

Gyakran előfordul amikor egy szervezet megvásárolja egy licenc vagy egy alkalmazás-előfizetés, a szervezet célja proaktív módon állíthatja be az alkalmazás a szervezet minden tagja számára. Ez a folyamat részeként a rendszergazda biztosíthat az alkalmazás a bérlő bármely felhasználó nevében való hozzájárulás. Ha a rendszergazda engedélyezi a jóváhagyásról a teljes bérlőn, a szervezet felhasználói számára az alkalmazás egy hozzájárulást kérő lap nem jelenik meg.

Beleegyezés kérése egy bérlő összes felhasználója esetében delegált engedélyeket kér, az alkalmazás segítségével a rendszergazdai jóváhagyás végpontja.

Emellett az alkalmazásoknak kell használniuk az a rendszergazdai jóváhagyás végpontja alkalmazás engedélyek kéréséhez.

## <a name="admin-restricted-permissions"></a>Korlátozott rendszergazdai engedélyek

Néhány magas szintű jogosultságokkal a Microsoft-ökoszisztéma állítható *korlátozott rendszergazdai*. Példák ilyen típusú engedélyek a következők:

* A felhasználó összes profiladatának olvasása használatával `User.Read.All`
* Adatokat írni a szervezet könyvtár használatával `Directory.ReadWrite.All`
* Egy szervezet címtárában lévő összes csoport olvasása használatával `Groups.Read.All`

Bár egy fogyasztói felhasználó engedélyezheti egy alkalmazás-hozzáférés az ilyen típusú adat, szervezeti felhasználók korlátozva vannak a ugyanazokat a bizalmas vállalati adatokhoz való hozzáférést. Ha az alkalmazás a szervezeti felhasználó hozzáférést kér az egyik ezeket az engedélyeket, a felhasználó kap olyan hibaüzenetet, amely arról tájékoztat, hogy engedélyt adjanak az engedélyeket az alkalmazás nem jogosultak.

Ha az alkalmazás a szervezetek hatóköreinek rendszergazdai korlátozott hozzáférésre van szüksége, igényeljen őket közvetlenül a egy vállalati rendszergazda is ismertetjük a rendszergazdai jóváhagyás végpont használatával.

Ha az alkalmazás magas jogosultsági delegált engedélyeket és a egy rendszergazda e engedélyt ad a rendszergazdai jóváhagyás végponton keresztül, engedély a bérlő összes felhasználója esetében.

Ha az alkalmazás által kért alkalmazás engedélyeit, és a rendszergazda engedélyezi ezeket az engedélyeket a rendszergazda keresztül hozzájárulás végpont, nem történik az engedélyhez bármely adott felhasználó nevében. Ehelyett az ügyfélalkalmazás jogosultságot *közvetlenül*. Az ilyen típusú engedélyek általában csak démon függőkártartalékát és más nem interaktív, a háttérben futó alkalmazások által használt.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai jóváhagyás végpont használatával

Ha egy vállalati rendszergazda az alkalmazás használ, és a hitelesítési végpontra irányul, a Microsoft identity platform észleli a felhasználói szerepkör, és kérje meg, ha szeretnék, hogy engedélyt adjanak az egész bérlő számára az Ön által kért engedélyeket nevében. Azonban van egy dedikált rendszergazdai jóváhagyás végpontot is használhatja, ha szeretné, hogy proaktív módon, hogy a rendszergazda engedélyt ad a teljes bérlő nevében kéréséhez. Ez a végpont használata esetén is szükséges kérő Alkalmazásengedélyek (amely nem igényelhető a hitelesítési végpontra használatával).

Kövesse az alábbi lépéseket, ha az alkalmazás minden felhasználó számára a bérlő, beleértve a hatókörök korlátozott rendszergazdai engedélyeket kérhetnek a. Ez magas jogosultsági művelet, és csak a forgatókönyv szükség esetén kell elvégezni.

A kódminta, amely megvalósítja a lépéseket, olvassa el a [korlátozott rendszergazdai hatókörök minta](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon az engedélyek kéréséhez

A rendszergazdai jóváhagyás nem fogadja el a hatókör-paramétert, ezért a kért engedélyek statikusan definiálni kell az alkalmazás regisztrációja. Ez általában annak érdekében, hogy az egy adott alkalmazás statikusan meghatározott engedélyek felülbírálja az engedélyeket, hogy azt fogja kér dinamikusan/növekményes az ajánlott eljárás.

A lista az alkalmazás statikusan kért engedélyeket konfigurálása: 
1. Nyissa meg az alkalmazását a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy [hozzon létre egy alkalmazást](quickstart-v2-register-an-app.md) Ha még nem tette.
2. Keresse meg a **Microsoft Graph-engedélyek** szakaszt, és adja hozzá az adott alkalmazáshoz szükséges engedélyeket.
3. **Mentés** az alkalmazás regisztrációját.

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
| `tenant` | Szükséges | A directory-bérlőhöz, amelyet szeretne az engedélyt. A megadott GUID vagy rövid név formátumban, vagy általános hivatkozott "közös" az a példában látható módon. |
| `client_id` | Szükséges | Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| `redirect_uri` | Szükséges |Az átirányítási URI-t a válasz az alkalmazás kezelni kell elküldeni kívánt helyre. Ez pontosan egyeznie kell az átirányítási URI-k, amelyek az alkalmazás regisztrációs portál regisztrált. |
| `state` | Ajánlott | A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat karakterlánc lehet. Az állapot használatával kódolása a felhasználói állapot az alkalmazás információ előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézetet. |

Ezen a ponton a az Azure AD bérlői rendszergazdával, jelentkezzen be a kérés teljesítéséhez szükséges. A rendszergazda hagyja jóvá a kért az alkalmazás regisztrációs portál az alkalmazáshoz tartozó összes engedélyt kell adnia.

#### <a name="successful-response"></a>A sikeres válasz

Ha a rendszergazda az alkalmazás engedélyeit jóváhagyja, a sikeres válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- | --- |
| `tenant` | A directory-bérlővel, amely engedéllyel rendelkezik az alkalmazás a kért, GUID formátumú. |
| `state` | A kérelem is visszaadott a jogkivonat-válaszban szereplő érték. Bármilyen tartalmat karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |
| `admin_consent` | Értékre lesz beállítva **igaz**. |

#### <a name="error-response"></a>Hiba történt a válasz

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- | --- |
| `error` |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| `error_description` |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hiba kiváltó okának azonosításához. |

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

## <a name="troubleshooting"></a>Hibaelhárítás

Ha Ön vagy az alkalmazás váratlan hibák jelennek a jóváhagyási folyamat során, ebben a cikkben talál a hibaelhárítási lépéseket lásd: [váratlan hiba történt, amikor beleegyezést ad egy alkalmazás](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
