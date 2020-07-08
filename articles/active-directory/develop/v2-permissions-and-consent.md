---
title: Microsoft Identity platform-hatókörök, engedélyek és beleegyezik
description: Az engedélyezés leírása a Microsoft Identity platform végpontján, beleértve a hatóköröket, az engedélyeket és a jóváhagyást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: d513dbd8449dad1d34117e06970f0c0881462aa3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263227"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Engedélyek és hozzájárulás a Microsoft-identitásplatform végpontján

A Microsoft Identity platformmal integrált alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Az engedélyezési modell megvalósítása frissítve lett a Microsoft Identity platform-végponton, és megváltoztatja, hogy az alkalmazásnak hogyan kell működnie a Microsoft Identity platformmal. Ez a cikk az engedélyezési modell alapvető fogalmait ismerteti, beleértve a hatóköröket, az engedélyeket és a hozzájárulást is.

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft Identity platform implementálja a [OAuth 2,0](active-directory-v2-protocols.md) hitelesítési protokollt. A OAuth 2,0 egy olyan módszer, amellyel a harmadik féltől származó alkalmazások a felhasználók nevében férhetnek hozzá a webkiszolgálók erőforrásaihoz. Bármely, a Microsoft Identity platformmal integrált webszolgáltatáshoz erőforrás-azonosító vagy *alkalmazás-azonosító URI*tartozik. Például a Microsoft számos webes erőforrása többek között:

* Microsoft Graph:`https://graph.microsoft.com`
* Office 365 mail API:`https://outlook.office.com`
* Azure Key Vault:`https://vault.azure.net`

> [!NOTE]
> Javasoljuk, hogy az Office 365 mail API helyett az Microsoft Graph-et használja.

Ugyanez érvényes a Microsoft Identity platformmal integrált, harmadik féltől származó erőforrások esetében is. Ezen erőforrások bármelyike meghatározhat olyan engedélyeket is, amelyek az adott erőforrás funkcióinak kisebb adattömbökbe való felosztására használhatók. A [Microsoft Graph](https://graph.microsoft.com) például a következő feladatok elvégzésére vonatkozó engedélyekkel rendelkezik, egyebek között:

* Felhasználó naptárának beolvasása
* Írás a felhasználó naptárába
* E-mail küldése felhasználóként

Az ilyen típusú engedélyek meghatározásával az erőforrás részletesen szabályozhatja az adatmennyiségét, és az API-funkciók elérhetővé válnak. Egy harmadik féltől származó alkalmazás kérheti ezeket az engedélyeket a felhasználóktól és a rendszergazdáktól, akiknek jóvá kell hagynia a kérést, mielőtt az alkalmazás hozzáférhessen az adatokhoz, vagy a felhasználó nevében cselekszik. Az erőforrás funkcióinak kisebb engedélyezési készletekbe való darabolásával a harmadik féltől származó alkalmazások csak a működésük végrehajtásához szükséges konkrét engedélyeket kérhetik. A felhasználók és a rendszergazdák pontosan tudják, hogy milyen adathozzáférést biztosítanak az alkalmazáshoz, és biztosak lehetnek abban, hogy nem a kártékony szándékkal viselkednek. A fejlesztőknek mindig meg kell felelniük a legalacsonyabb jogosultsági szint koncepciójának, és csak azokat az engedélyeket kérik, amelyekre az alkalmazásoknak szüksége van.

A OAuth 2,0-ben az ilyen típusú engedélyeket *hatóköröknek*nevezzük. Ezeket gyakran *engedélyeknek*is nevezzük. Az engedélyek a Microsoft Identity platformban karakterlánc-értékként jelennek meg. Ha folytatja a Microsoft Graph példát, az egyes engedélyek sztring értéke a következő:

* A felhasználó naptárának beolvasása a következő használatával:`Calendars.Read`
* Írás a felhasználó naptárába a következő használatával:`Calendars.ReadWrite`
* E-mail küldése felhasználóként a általi használatával`Mail.Send`

Az alkalmazások leggyakrabban a Microsoft Identity platform engedélyezés végpontjának megadásával kérik le ezeket az engedélyeket. Bizonyos magas jogosultsági szintű engedélyek azonban csak a rendszergazdai engedélyekkel adhatók meg, és a kérés/engedélyezés a [rendszergazdai engedélyezési végpont](v2-permissions-and-consent.md#admin-restricted-permissions)használatával lehetséges. További információért olvassa el a következőt:.

## <a name="permission-types"></a>Engedélyezési típusok

A Microsoft Identity platform két típusú engedélyt támogat: a **delegált engedélyeket** és az **alkalmazás engedélyeit**.

* A **delegált engedélyeket** a bejelentkezett felhasználóval rendelkező alkalmazások használják. Ezekhez az alkalmazásokhoz a felhasználó vagy a rendszergazda jóváhagyja az alkalmazás által kért engedélyeket, és az alkalmazás delegált engedéllyel rendelkezik, hogy bejelentkezett felhasználóként működjön a célként megadott erőforrás meghívásakor. Egyes delegált engedélyek a nem rendszergazda felhasználók számára is megadhatók, de egyes magasabb jogosultságú engedélyek esetén [rendszergazdai hozzájárulásra](v2-permissions-and-consent.md#admin-restricted-permissions)van szükség. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../users-groups-roles/directory-assign-admin-roles.md).

* Az **alkalmazás engedélyeit** a bejelentkezett felhasználó nélkül futtató alkalmazások használják. például olyan alkalmazások, amelyek háttér-szolgáltatásként vagy démonként futnak.  Az alkalmazás engedélyeit csak a [rendszergazda](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)fogadhatja el.

A _hatályos engedélyek_ azokat az engedélyeket jelentik, amelyekkel az alkalmazás a célként megadott erőforrásra irányuló kéréseket tesz elérhetővé. Fontos megérteni az alkalmazás által biztosított delegált és alkalmazási engedélyek, valamint a célként megadott erőforrásra irányuló hívások esetén érvényes engedélyek közötti különbséget.

- A delegált engedélyek esetében az alkalmazás _érvényes engedélyei_ lesznek az alkalmazás által biztosított delegált engedélyek legkevesebb jogosultsággal rendelkező metszéspontja, valamint a jelenleg bejelentkezett felhasználó jogosultságai. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. A cégeken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagsága határozhatja meg. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../users-groups-roles/directory-assign-admin-roles.md).

   Tegyük fel például, hogy az alkalmazás megkapta a _User. ReadWrite. All_ delegált engedélyt. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó egy globális rendszergazda, az alkalmazás frissíteni tudja a cégben lévő összes felhasználó profilját. Ha azonban a bejelentkezett felhasználó nincs rendszergazdai szerepkörben, az alkalmazás csak a bejelentkezett felhasználó profilját fogja tudni frissíteni. Nem tudja frissíteni a cégben lévő többi felhasználó profilját, mert az a felhasználó nem rendelkezik ilyen jogosultságokkal, akinek a nevében eljár.

- Az alkalmazás engedélyei esetében az alkalmazás _hatályos engedélyei_ a jogosultságok teljes szintjének jelennek meg. Például egy olyan alkalmazás, amely rendelkezik a _User. ReadWrite. All_ Application engedélyekkel, frissítheti a szervezet minden felhasználójának profilját.

## <a name="openid-connect-scopes"></a>OpenID Connect-hatókörök

Az OpenID Connect Microsoft Identity platform megvalósítása néhány jól definiált hatókörrel rendelkezik, amelyek a Microsoft Graph:,, és rendszeren is futnak `openid` `email` `profile` `offline_access` . Az `address` és az `phone` OpenID Connect hatókörök nem támogatottak.

A OIDC-hatókörök és a tokenek kérése megadja az [UserInfo-végpont](userinfo.md)meghívásához szükséges tokent.

### <a name="openid"></a>OpenID

Ha egy alkalmazás az [OpenID Connect](active-directory-v2-protocols.md)használatával hajtja végre a bejelentkezést, akkor a `openid` hatókört kell kérnie. A `openid` hatókör a munkahelyi fiókra vonatkozó hozzájárulás oldalon jelenik meg, a "Bejelentkezés" engedéllyel, valamint a személyes Microsoft-fiók beleegyezési lapján a Profil megtekintése és az alkalmazásokhoz és szolgáltatásokhoz való kapcsolódás a Microsoft-fiók használatával "engedéllyel. Ezzel az engedéllyel az alkalmazás a kérelem formájában egyedi azonosítót kaphat a felhasználó számára `sub` . Emellett hozzáférést biztosít az alkalmazásnak az UserInfo-végponthoz is. A `openid` hatókör a Microsoft Identity platform token végpontján használható azonosító tokenek beszerzéséhez, amelyeket az alkalmazás használhat a hitelesítéshez.

### <a name="email"></a>e-mail

A `email` hatókör a `openid` hatókörrel és másokkal is használható. Az alkalmazás a kérelem formájában hozzáférést biztosít a felhasználó elsődleges e-mail-címéhez `email` . A `email` jogcímek csak akkor szerepelnek a jogkivonatban, ha az e-mail-cím a felhasználói fiókhoz van társítva, amely nem mindig az eset. Ha a `email` hatókört használja, az alkalmazásnak fel kell készülnie egy olyan eset kezelésére, amelyben a `email` jogcím nem létezik a jogkivonatban.

### <a name="profile"></a>profil

A `profile` hatókör a `openid` hatókörrel és másokkal is használható. Hozzáférést biztosít az alkalmazásnak a felhasználóval kapcsolatos jelentős mennyiségű információhoz. Az általa elérhető információ magában foglalja a következőket:, de nem kizárólagosan, a felhasználó vezetéknevét, vezetéknevét, előnyben részesített felhasználónevét és objektum-AZONOSÍTÓját. Egy adott felhasználó id_tokens paraméterében elérhető profil jogcímek teljes listájáért tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md).

### <a name="offline_access"></a>offline_access

A [ `offline_access` hatókör](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) lehetővé teszi az alkalmazás számára, hogy a felhasználó nevében egy hosszabb ideig hozzáférjen az erőforrásokhoz. A hozzájárulás lapon ez a hatókör jelenik meg, mivel "a hozzáférés biztosítása az Ön számára biztosított adathozzáféréshez" jogosultságot. Amikor a felhasználó jóváhagyja a `offline_access` hatókört, az alkalmazás a Microsoft Identity platform jogkivonat-végpontján fogadhatja a frissítési jogkivonatokat. A frissítési tokenek hosszú életűek. Az alkalmazás új hozzáférési jogkivonatokat kaphat, mint a régebbiek lejárnak.

> [!NOTE]
> Ez az engedély jelenleg az összes beleegyezési képernyőn jelenik meg, még olyan folyamatok esetében is, amelyek nem biztosítanak frissítési jogkivonatot (az [implicit folyamat](v2-oauth2-implicit-grant-flow.md)).  Ennek célja, hogy olyan forgatókönyveket Fedezzen fel, amelyekben az ügyfél az implicit folyamaton belül megkezdődhet, majd a kód azon folyamatára helyezi át, ahol a frissítési token várható.

A Microsoft Identity platformon (a v 2.0-végpontra irányuló kérelmek esetében) az alkalmazásnak explicit módon kell megkérnie a `offline_access` hatókört a frissítési tokenek fogadásához. Ez azt jelenti, hogy amikor bevált egy engedélyezési kódot a [OAuth 2,0 engedélyezési kód folyamatában](active-directory-v2-protocols.md), csak a végponttól kap hozzáférési jogkivonatot `/token` . A hozzáférési jogkivonat rövid ideig érvényes. A hozzáférési jogkivonat általában egy órán belül lejár. Ezen a ponton az alkalmazásnak újra kell irányítani a felhasználót a `/authorize` végpontra egy új engedélyezési kód beszerzéséhez. Az átirányítás során az alkalmazás típusától függően előfordulhat, hogy a felhasználónak újra be kell írnia a hitelesítő adatait, vagy újra meg kell adnia az engedélyeket.

További információ a frissítési tokenek beszerzéséről és használatáról: a [Microsoft Identity platform protokolljának referenciája](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói engedély kérése

Egy [OpenID Connect vagy OAuth 2,0](active-directory-v2-protocols.md) engedélyezési kérelemben az alkalmazás a lekérdezési paraméter használatával kérheti le a szükséges engedélyeket `scope` . Ha például egy felhasználó bejelentkezik egy alkalmazásba, az alkalmazás a következő példához hasonló kérelmet küld (az olvashatóság érdekében sortöréssel bővült):

```HTTP
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

A `scope` paraméter az alkalmazás által kért delegált engedélyek szóközzel tagolt listája. Az engedélyek az erőforrás azonosítójának (az alkalmazás-azonosító URI-ja) hozzáfűzésével vannak jelezve. A kérelem példájában az alkalmazásnak engedéllyel kell rendelkeznie a felhasználó naptárának olvasásához és a levelezés felhasználóként való elküldéséhez.

Miután a felhasználó beírja a hitelesítő adatait, a Microsoft Identity platform-végpont ellenőrzi a *felhasználói beleegyezési*rekordokat. Ha a felhasználó nem járult hozzá a korábban kért engedélyekhez, és a rendszergazda nem fogadta el ezeket az engedélyeket a teljes szervezet nevében, a Microsoft Identity platform végpontja kéri a felhasználót, hogy adja meg a kért engedélyeket.

> [!NOTE]
>Ebben az esetben a `offline_access` ("a hozzáférés megtartása az Ön által megadott adathozzáféréshez") és a `user.read` ("bejelentkezés és a profil olvasása") engedélyek automatikusan belekerülnek a kezdeti beleegyező alkalmazásba.  Ezek az engedélyek általában szükségesek az alkalmazás megfelelő működéséhez – lehetővé teszi, `offline_access` hogy az alkalmazás hozzáférjen a natív és a webes alkalmazások számára kritikus frissítési tokenekhez, miközben `user.read` hozzáférést biztosít a `sub` jogcímek számára, így az ügyfél vagy az alkalmazás helyesen azonosíthatja a felhasználót az idő függvényében, és elérheti az alapvető felhasználói adatokat.

![A munkahelyi fiók beleegyezikét bemutató képernyőkép](./media/v2-permissions-and-consent/work_account_consent.png)

Ha a felhasználó jóváhagyja az engedély kérését, a rendszer rögzíti a hozzájárulást, és a felhasználónak nem kell újból megadnia az alkalmazásba való bejelentkezést követően.

## <a name="requesting-consent-for-an-entire-tenant"></a>A teljes bérlőre vonatkozó belefoglalási kérelem

Ha egy szervezet egy alkalmazás licencét vagy előfizetését vásárolja meg, a szervezet a szervezet minden tagja számára proaktívan szeretné beállítani az alkalmazást. Ennek a folyamatnak a részeként a rendszergazda beleegyezik abba, hogy az alkalmazás a bérlő bármely felhasználója nevében működjön. Ha a rendszergazda hozzájárulást ad a teljes bérlőhöz, a szervezet felhasználóinak nem fog megjelenni az alkalmazás beleegyező lapja.

Ha a bérlő összes felhasználója számára szeretne beleegyezni a delegált engedélyekkel, az alkalmazás használhatja a rendszergazdai engedélyezési végpontot.

Emellett az alkalmazásoknak a rendszergazdai engedélyezési végpontot kell használniuk az alkalmazások engedélyeinek igényléséhez.

## <a name="admin-restricted-permissions"></a>Rendszergazdai hozzáférésre korlátozott engedélyek

A Microsoft ökoszisztéma bizonyos magas jogosultsági szintű engedélyei a *rendszergazda által korlátozottra*állíthatók be. Ilyen típusú engedélyek például a következők:

* Az összes felhasználó teljes profiljának olvasása a következő használatával:`User.Read.All`
* Adatírás a szervezet könyvtárába a következő használatával:`Directory.ReadWrite.All`
* A szervezet címtárában lévő összes csoport olvasása a következő használatával:`Groups.Read.All`

Bár a felhasználói felhasználók hozzáférést biztosíthatnak az ilyen típusú adatokhoz, a szervezeti felhasználók csak a bizalmas vállalati adatokhoz való hozzáférést biztosítják. Ha az alkalmazás egy szervezeti felhasználótól kéri a fenti engedélyek egyikének elérését, a felhasználó hibaüzenetet kap, amely szerint nem jogosult beleegyezni az alkalmazás engedélyeivel.

Ha az alkalmazásnak hozzáférést kell biztosítania a szervezeteknek a rendszergazdai korlátozás alá eső hatókörökhöz, a következő témakörben leírtak szerint közvetlenül a vállalati rendszergazdától kell kérnie őket.

Ha az alkalmazás magas jogosultságú delegált engedélyeket kér, és a rendszergazda a rendszergazdai hozzájárulási végponton keresztül engedélyezi ezeket az engedélyeket, akkor a bérlő összes felhasználója számára meg kell adni a hozzájárulást.

Ha az alkalmazás alkalmazás-engedélyeket kér, és a rendszergazda a rendszergazdai hozzájárulási végponton keresztül engedélyezi ezeket az engedélyeket, az adott felhasználó nevében nem hajtja végre ezt az engedélyt. Ehelyett az ügyfélalkalmazás *közvetlenül*kap engedélyeket. Az ilyen típusú engedélyeket csak a Daemon Services és a háttérben futó egyéb nem interaktív alkalmazások használják.

## <a name="using-the-admin-consent-endpoint"></a>A rendszergazdai engedélyezési végpont használata

> [!NOTE]
> Vegye figyelembe, hogy a rendszergazdai jóváhagyás megadása után a rendszergazdai jóváhagyás megadása után a rendszergazda beleegyezik, és a felhasználóknak semmilyen további műveletet nem kell elvégezniük. A rendszergazdai hozzájárulás megadása után a felhasználók egy tipikus hitelesítési folyamaton keresztül kaphatnak hozzáférési jogkivonatot, és az eredményül kapott hozzáférési jogkivonat is rendelkezik a jóváhagyáshoz szükséges engedélyekkel.

Ha a vállalati rendszergazda az alkalmazást használja, és az engedélyezés végpontra irányítja, a Microsoft Identity platform felismeri a felhasználó szerepkörét, és megkérdezi, hogy szeretné-e jóváhagyni a teljes bérlő nevében a kért engedélyeket. Van azonban egy dedikált rendszergazdai hozzájárulási végpont is, amelyet akkor használhat, ha proaktívan szeretné megkérni, hogy a rendszergazda a teljes bérlő nevében engedélyt biztosítson. Ennek a végpontnak a használata szükséges az alkalmazás engedélyeinek kérelmezéséhez is (amelyek nem kérhetők az engedélyezés végpontjának használatával).

Ha követi ezeket a lépéseket, az alkalmazás engedélyt kérhet a bérlő összes felhasználója számára, beleértve a rendszergazdai korlátozás alá eső hatóköröket is. Ez egy magas jogosultsági szintű művelet, és csak akkor végezhető el, ha a forgatókönyvhöz szükséges.

Ha meg szeretne tekinteni egy kódot, amely megvalósítja a lépéseket, tekintse meg a [rendszergazda által korlátozott hatókörű mintát](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az engedélyek igénylése az alkalmazás regisztrációs portálján

Az alkalmazások megtekinthetik, hogy mely engedélyek szükségesek (delegált és alkalmazásként egyaránt) az alkalmazás regisztrációs portálján.  Ez lehetővé teszi a `/.default` hatókör használatát, valamint a Azure Portal "rendszergazdai jóváhagyás megadása" lehetőséget.  Általánosságban az ajánlott eljárás az, hogy az adott alkalmazáshoz statikusan meghatározott engedélyek a dinamikusan/növekményes igényeknek megfelelő engedélyek kibővítettek legyenek.

> [!NOTE]
>Az alkalmazás engedélyei csak akkor kérhetők le a használatával, ha az alkalmazásnak [`/.default`](#the-default-scope) szüksége van az alkalmazás engedélyeire, győződjön meg róla, hogy szerepelnek az alkalmazás regisztrációs portálján.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Az alkalmazáshoz tartozó statikusan kért engedélyek listájának konfigurálása

1. Ha még nem tette meg, nyissa meg az alkalmazást az [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felületen, vagy [hozzon létre egy alkalmazást](quickstart-register-app.md) .
2. Keresse meg az **API-engedélyek** szakaszt, és az API-engedélyek területen kattintson az engedély hozzáadása lehetőségre.
3. Válassza a **Microsoft Graph** lehetőséget az elérhető API-k listájából, majd adja hozzá az alkalmazás által igényelt engedélyeket.
3. **Mentse** az alkalmazás regisztrációját.

### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: a felhasználó aláírása az alkalmazásba

Ha olyan alkalmazást hoz létre, amely a rendszergazdai jogosultságok végpontját használja, akkor az alkalmazásnak szüksége van egy olyan oldalra vagy nézetre, amelyben a rendszergazda jóváhagyhatja az alkalmazás engedélyeit. Ezen a lapon lehet az alkalmazás regisztrációs folyamatának része, az alkalmazás beállításainak egy része, vagy egy dedikált "kapcsolat" folyamat is. Sok esetben érdemes megmutatni, hogy az alkalmazás csak akkor jelenjen meg ez a "kapcsolódás" nézet, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiók bejelentkezett.

Amikor aláírja a felhasználót az alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a rendszergazda tartozik, mielőtt megkéri őket a szükséges engedélyek jóváhagyására. Bár ez nem feltétlenül szükséges, így könnyebben hozhat létre egy intuitív felhasználói élményt a szervezeti felhasználók számára. A felhasználó aláírásához kövesse a [Microsoft Identity platform protokoll oktatóanyagokat](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyt kérjen a szervezete rendszergazdájától, átirányíthatja a felhasználót a Microsoft Identity platform *rendszergazdai engedélyezési végpontján*.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Paraméter        | Állapot        | Leírás                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Kötelező | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. A GUID vagy a felhasználóbarát név formátumban adható meg, vagy általános módon hivatkozik a szervezetekre, ahogyan az a példában látható. Ne használja a "Common" kulcsszót, mert a személyes fiókok nem biztosíthatnak rendszergazdai jogosultságot, kivéve a bérlő kontextusát. A bérlőket kezelő személyes fiókokkal való legjobb kompatibilitás érdekében használja a bérlői azonosítót, ha lehetséges. |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `redirect_uri` | Kötelező |Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. A kívánt tartalom sztringje lehet. Az állapot használatával kódolja a felhasználó állapotára vonatkozó adatokat az alkalmazásban, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
|`scope`        | Kötelező        | Meghatározza az alkalmazás által igényelt engedélyek készletét. Ez lehet statikus (használ [`/.default`](#the-default-scope) ) vagy dinamikus hatókörök.  Ebbe beletartozhatnak a OIDC hatókörök ( `openid` , `profile` ,) is `email` . Ha az alkalmazásra vonatkozó engedélyekre van szüksége, a használatával kell `/.default` kérnie a statikusan konfigurált engedélyek listáját.  |


Ezen a ponton az Azure AD-nek a bérlői rendszergazdának kell bejelentkeznie a kérelem teljesítéséhez. A rendszer felkéri a rendszergazdát, hogy hagyja jóvá a paraméterben kért összes engedélyt `scope` .  Ha statikus ( `/.default` ) értéket használt, úgy fog működni, mint a v 1.0 rendszergazdai engedélyezési végpont, és az alkalmazáshoz szükséges engedélyekben található összes hatókörre vonatkozó kérelem beleegyezése.

#### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva. |
| `state` | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |
| `admin_consent` | A következőre lesz beállítva: `True` . |

#### <a name="error-response"></a>Hiba válasza

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőre hasonlít:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hibák kiváltó okának azonosításában. |

Miután sikeres választ kapott a rendszergazdai jogosultsági végponttól, az alkalmazás megszerezte a kért engedélyeket. Ezután kérheti a kívánt erőforráshoz tartozó jogkivonatot.

## <a name="using-permissions"></a>Engedélyek használata

Miután a felhasználó beleegyezett az alkalmazás engedélyeibe, az alkalmazás képes olyan hozzáférési jogkivonatok beszerzésére, amelyek képviselik az alkalmazás számára, hogy bizonyos kapacitásban hozzáférjenek az erőforrásokhoz. Hozzáférési jogkivonat csak egyetlen erőforráshoz használható, de a hozzáférési jogkivonaton belül kódolt minden engedély, amelyet az alkalmazás az adott erőforráshoz adott meg. Hozzáférési jogkivonat beszerzéséhez az alkalmazás kérheti a Microsoft Identity platform jogkivonat-végpontját, amely a következőképpen néz ki:

```HTTP
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

Az eredményül kapott hozzáférési jogkivonatot a HTTP-kérelmekben az erőforráshoz is használhatja. Megbízhatónak tekinti azt az erőforrást, amelyet az alkalmazás megfelelő engedéllyel adott feladat végrehajtásához.

A OAuth 2,0 protokollról és a hozzáférési jogkivonatok beszerzéséről a [Microsoft Identity platform Endpoint Protocol-referenciájában](active-directory-v2-protocols.md)talál további információt.

## <a name="the-default-scope"></a>A/.default hatóköre

A `/.default` hatókör segítségével áttelepítheti az alkalmazásokat a 1.0-s végpontról a Microsoft Identity platform-végpontra. Ez egy beépített hatókör minden olyan alkalmazáshoz, amely az alkalmazás regisztrációján konfigurált engedélyek statikus listájára hivatkozik. A `scope` értéke `https://graph.microsoft.com/.default` funkcionálisan megegyezik a v 1.0-s végpontokkal `resource=https://graph.microsoft.com` – azaz az alkalmazás által a Azure Portalban regisztrált Microsoft Graph hatókörökkel rendelkező tokent kér.  Az erőforrás URI + `/.default` -ja (például ha az erőforrás URI `https://contosoApp.com` -ja, a kért hatókör a következő lesz: `https://contosoApp.com/.default` ).  Tekintse [meg](#trailing-slash-and-default) az olyan eseteket, amikor egy második perjelet kell tartalmaznia a token megfelelő kéréséhez.

A/.default-hatókör bármely OAuth 2,0-flow-ban használható, de szükséges a [folyamaton](v2-oauth2-on-behalf-of-flow.md) belüli és az [ügyfél-hitelesítő adatok folyamatában](v2-oauth2-client-creds-grant-flow.md), valamint a v2 rendszergazdai jóváhagyás végpontjának használatakor az alkalmazás engedélyeinek kérelmezéséhez.

> [!NOTE]
> Az ügyfelek egyetlen kérelemben nem kombinálhatók statikus ( `/.default` ) és dinamikus beleegyező engedéllyel. Így `scope=https://graph.microsoft.com/.default+mail.read` a hatókör-típusok kombinációja miatt hibát okozhat.

### <a name="default-and-consent"></a>/.default és beleegyezett

A `/.default` hatókör a v 1.0 végpont viselkedését `prompt=consent` is elindítja. Az alkalmazás által regisztrált összes engedélyre vonatkozó beleegyezik, az erőforrástól függetlenül. Ha a kérelem részeként szerepel, a `/.default` hatókör olyan tokent ad vissza, amely tartalmazza a kért erőforrás hatóköreit.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, ha a felhasználó már megadott egy engedélyt

Mivel a `/.default` funkció megegyezik a `resource` -centrikus v 1.0 végpont működésével, az a v 1.0 végpont beleegyezési viselkedésével is jár. A `/.default` csak akkor aktiválja a beleegyezési kérést, ha a felhasználó nem kapott engedélyt az ügyfél és az erőforrás között. Ha ilyen beleegyezik, akkor a rendszer visszaadja a tokent, amely a felhasználó által az adott erőforráshoz megadott összes hatókört tartalmazza. Ha azonban nem adta meg az engedélyt, vagy a paraméter meg van adva, a rendszer `prompt=consent` egy beleegyezési kérést jelenít meg az ügyfélalkalmazás által regisztrált összes hatókörhöz.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>1. példa: a felhasználó vagy a bérlő rendszergazdája engedélyt kapott

Ebben a példában a felhasználó (vagy egy bérlői rendszergazda) engedélyezte az ügyfél számára a Microsoft Graph engedélyeket és a-t `mail.read` `user.read` . Ha az ügyfél kérelmet küld a szolgáltatáshoz, akkor a rendszer nem jelenít meg `scope=https://graph.microsoft.com/.default` beleegyező kérést, függetlenül attól, hogy az ügyfélalkalmazások milyen tartalmakat regisztráltak a Microsoft Graph. A rendszer visszaadja a jogcímeket és a hatókört tartalmazó jogkivonatot `mail.read` `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>2. példa: a felhasználó nem adott meg engedélyeket az ügyfél és az erőforrás között

Ebben a példában a felhasználó nem rendelkezik beleegyezéssel az ügyfél és a Microsoft Graph között. Az ügyfél regisztrálva van a `user.read` és az `contacts.read` engedélyekhez, valamint a Azure Key Vault hatókörhöz `https://vault.azure.net/user_impersonation` . Amikor az ügyfél jogkivonatot kér `scope=https://graph.microsoft.com/.default` , a felhasználó a `user.read` , a `contacts.read` és a Key Vault hatókörökhöz tartozó beleegyezési képernyőt fogja látni `user_impersonation` . A visszaadott jogkivonat csak a és a hatókörökkel fog rendelkezni, `user.read` `contacts.read` és csak Microsoft Graphon használható.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>3. példa: a felhasználó beleegyezett, és az ügyfél további hatóköröket kér

Ebben a példában a felhasználó már beleegyezett az `mail.read` ügyfélhez. Az ügyfél regisztrálva van a `contacts.read` hatókörben a regisztrációjában. Ha az ügyfél kérelmet küld egy jogkivonat számára a és a kérelmének megadásához `scope=https://graph.microsoft.com/.default` `prompt=consent` , akkor a felhasználó az alkalmazás által regisztrált engedélyeket az összes (és csak) beleegyezési képernyője fogja látni. `contacts.read`jelen lesz a beleegyezési képernyőn, de `mail.read` nem. A visszaadott token Microsoft Graph lesz, és a következőt fogja tartalmazni: `mail.read` és `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>A/.default hatókör használata az ügyféllel

A hatókör speciális esete `/.default` létezik, ahol az ügyfél a saját `/.default` hatókörét kéri. A következő példa bemutatja ezt a forgatókönyvet.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ez egy beleegyezési képernyőt hoz létre az összes regisztrált engedélyhez (ha alkalmazható a fenti és a beleegyezési leírás alapján `/.default` ), akkor egy hozzáférési jogkivonat helyett egy id_token ad vissza.  Ez a viselkedés bizonyos, a ADAL-ből a MSAL-re áthelyezett örökölt ügyfelek esetében létezik, és a Microsoft Identity platform végpontját célzó új ügyfelek **nem** használhatják.

### <a name="trailing-slash-and-default"></a>Záró perjel és/.default

Egyes erőforrás-URI-k záró perjeltel rendelkeznek ( `https://contoso.com/` ellentétben a `https://contoso.com` következővel), ami problémákat okozhat a jogkivonat-ellenőrzés során.  Ez elsősorban akkor fordulhat elő, ha tokent kér az Azure Resource Management () szolgáltatáshoz `https://management.azure.com/` , amely záró perjeltel rendelkezik az erőforrás-URI-n, és azt igényli, hogy a jogkivonat kérésekor legyen jelen.  Így ha tokent kér `https://management.azure.com/` és használ `/.default` , meg kell adnia `https://management.azure.com//.default` a kettős perjelet!

Általánosságban – ha ellenőrizte, hogy a jogkivonat ki van-e állítva, és a jogkivonatot el kell utasítani az API-nak, amelynek el kell fogadnia, vegye fontolóra egy második perjel hozzáadását, és próbálkozzon újra. Ez azért történik, mert a bejelentkezési kiszolgáló olyan tokent bocsát ki, amely a paraméterben szereplő URI azonosítókkal egyezik, a `scope` `/.default` végéről eltávolítva.  Ha ez eltávolítja a záró perjelet, a bejelentkezési kiszolgáló továbbra is feldolgozza a kérést, és érvényesíti az erőforrás URI-ja alapján, annak ellenére, hogy már nem egyeznek – ez nem szabványos, és az alkalmazás nem hivatkozhat rá.

## <a name="troubleshooting-permissions-and-consent"></a>Hibaelhárítási engedélyek és beleegyezett

Ha Ön vagy az alkalmazás felhasználóinak váratlan hibába ütközik a beleegyező folyamat során, tekintse meg ezt a cikket a hibaelhárítási lépésekhez: [váratlan hiba történt az alkalmazáshoz való beleegyező művelet végrehajtásakor](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
