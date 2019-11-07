---
title: Microsoft Identity platform-hatókörök, engedélyek és beleegyező adatok | Microsoft Docs
description: Az engedélyezés leírása a Microsoft Identity platform végpontján, beleértve a hatóköröket, az engedélyeket és a jóváhagyást.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb6d96dfdca63f1bacf45ab0af01d18aafcf302
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667877"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Engedélyek és beleegyezett a Microsoft Identity platform végpontjában

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A Microsoft Identity platformmal integrált alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Az engedélyezési modell megvalósítása frissítve lett a Microsoft Identity platform-végponton, és megváltoztatja, hogy az alkalmazásnak hogyan kell működnie a Microsoft Identity platformmal. Ez a cikk az engedélyezési modell alapvető fogalmait ismerteti, beleértve a hatóköröket, az engedélyeket és a hozzájárulást is.

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes forgatókönyvet és funkciót. Annak megállapításához, hogy a Microsoft Identity platform-végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="scopes-and-permissions"></a>Hatókörök és engedélyek

A Microsoft Identity platform implementálja a [OAuth 2,0](active-directory-v2-protocols.md) hitelesítési protokollt. A OAuth 2,0 egy olyan módszer, amellyel a harmadik féltől származó alkalmazások a felhasználók nevében férhetnek hozzá a webkiszolgálók erőforrásaihoz. Bármely, a Microsoft Identity platformmal integrált webszolgáltatáshoz erőforrás-azonosító vagy *alkalmazás-azonosító URI*tartozik. Például a Microsoft számos webes erőforrása többek között:

* Microsoft Graph: `https://graph.microsoft.com`
* Office 365 levelezési API: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Azt javasoljuk, hogy az Azure AD Graph, az Office 365 mail API stb. helyett használjon Microsoft Graph.

Ugyanez érvényes a Microsoft Identity platformmal integrált, harmadik féltől származó erőforrások esetében is. Ezen erőforrások bármelyike meghatározhat olyan engedélyeket is, amelyek az adott erőforrás funkcióinak kisebb adattömbökbe való felosztására használhatók. A [Microsoft Graph](https://graph.microsoft.com) például a következő feladatok elvégzésére vonatkozó engedélyekkel rendelkezik, egyebek között:

* Felhasználó naptárának beolvasása
* Írás a felhasználó naptárába
* E-mail küldése felhasználóként

Az ilyen típusú engedélyek meghatározásával az erőforrás részletesen szabályozhatja az adatmennyiségét, és az API-funkciók elérhetővé válnak. Egy harmadik féltől származó alkalmazás kérheti ezeket az engedélyeket a felhasználóktól és a rendszergazdáktól, akiknek jóvá kell hagynia a kérést, mielőtt az alkalmazás hozzáférhessen az adatokhoz, vagy a felhasználó nevében cselekszik. Az erőforrás funkcióinak kisebb engedélyezési készletekbe való darabolásával a harmadik féltől származó alkalmazások csak a működésük végrehajtásához szükséges konkrét engedélyeket kérhetik. A felhasználók és a rendszergazdák pontosan tudják, hogy milyen adathozzáférést biztosítanak az alkalmazáshoz, és biztosak lehetnek abban, hogy nem a kártékony szándékkal viselkednek. A fejlesztőknek mindig meg kell felelniük a legalacsonyabb jogosultsági szint koncepciójának, és csak azokat az engedélyeket kérik, amelyekre az alkalmazásoknak szüksége van.

A OAuth 2,0-ben az ilyen típusú engedélyeket *hatóköröknek*nevezzük. Ezeket gyakran *engedélyeknek*is nevezzük. Az engedélyek a Microsoft Identity platformban karakterlánc-értékként jelennek meg. Ha folytatja a Microsoft Graph példát, az egyes engedélyek sztring értéke a következő:

* A felhasználó naptárának beolvasása `Calendars.Read` használatával
* Írás a felhasználó naptárába `Calendars.ReadWrite` használatával
* E-mail küldése felhasználóként a `Mail.Send` használatával

Az alkalmazások leggyakrabban a Microsoft Identity platform engedélyezés végpontjának megadásával kérik le ezeket az engedélyeket. Bizonyos magas jogosultsági szintű engedélyek azonban csak a rendszergazdai engedélyekkel adhatók meg, és a kérés/engedélyezés a [rendszergazdai engedélyezési végpont](v2-permissions-and-consent.md#admin-restricted-permissions)használatával lehetséges. Látogasson el lapunkra további információért.

## <a name="permission-types"></a>Engedélyezési típusok

A Microsoft Identity platform két típusú engedélyt támogat: a **delegált engedélyeket** és az **alkalmazás engedélyeit**.

* A **delegált engedélyeket** a bejelentkezett felhasználóval rendelkező alkalmazások használják. Ezekhez az alkalmazásokhoz a felhasználó vagy a rendszergazda jóváhagyja az alkalmazás által kért engedélyeket, és az alkalmazás delegált engedéllyel rendelkezik, hogy bejelentkezett felhasználóként működjön a célként megadott erőforrás meghívásakor. Egyes delegált engedélyek a nem rendszergazda felhasználók számára is megadhatók, de egyes magasabb jogosultságú engedélyek esetén [rendszergazdai hozzájárulásra](v2-permissions-and-consent.md#admin-restricted-permissions)van szükség. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../users-groups-roles/directory-assign-admin-roles.md).

* Az **alkalmazás engedélyeit** a bejelentkezett felhasználó nélkül futtató alkalmazások használják. például olyan alkalmazások, amelyek háttér-szolgáltatásként vagy démonként futnak.  Az alkalmazás engedélyeit csak a [rendszergazda](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)fogadhatja el.

A _hatályos engedélyek_ azokat az engedélyeket jelentik, amelyekkel az alkalmazás a célként megadott erőforrásra irányuló kéréseket tesz elérhetővé. Fontos megérteni az alkalmazás által biztosított delegált és alkalmazási engedélyek, valamint a célként megadott erőforrásra irányuló hívások esetén érvényes engedélyek közötti különbséget.

- A delegált engedélyek esetében az alkalmazás _érvényes engedélyei_ lesznek az alkalmazás által biztosított delegált engedélyek legkevesebb jogosultsággal rendelkező metszéspontja, valamint a jelenleg bejelentkezett felhasználó jogosultságai. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. A cégeken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagsága határozhatja meg. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../users-groups-roles/directory-assign-admin-roles.md).

   Tegyük fel például, hogy az alkalmazás megkapta a _User. ReadWrite. All_ delegált engedélyt. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó egy globális rendszergazda, az alkalmazás frissíteni tudja a cégben lévő összes felhasználó profilját. Ha azonban a bejelentkezett felhasználó nincs rendszergazdai szerepkörben, az alkalmazás csak a bejelentkezett felhasználó profilját fogja tudni frissíteni. Nem tudja frissíteni a cégben lévő többi felhasználó profilját, mert az a felhasználó nem rendelkezik ilyen jogosultságokkal, akinek a nevében eljár.
  
- Az alkalmazás engedélyei esetében az alkalmazás _hatályos engedélyei_ a jogosultságok teljes szintjének jelennek meg. Például egy olyan alkalmazás, amely rendelkezik a _User. ReadWrite. All_ Application engedélyekkel, frissítheti a szervezet minden felhasználójának profilját. 

## <a name="openid-connect-scopes"></a>OpenID Connect-hatókörök

Az OpenID Connect Microsoft Identity platform megvalósítása néhány jól definiált hatókörrel rendelkezik, amelyek nem vonatkoznak egy adott erőforrásra: `openid`, `email`, `profile`és `offline_access`. Az OpenID Connect-hatókörök `address` és `phone` nem támogatottak.

### <a name="openid"></a>OpenID

Ha egy alkalmazás az [OpenID Connect](active-directory-v2-protocols.md)használatával hajtja végre a bejelentkezést, akkor a `openid` hatókört kell kérnie. A `openid` hatókör a munkahelyi fiókra vonatkozó hozzájárulás oldalon jelenik meg, a "Bejelentkezés" engedéllyel, valamint a személyes Microsoft-fiók beleegyezés lapon a Profil megtekintése és az alkalmazásokhoz és szolgáltatásokhoz való kapcsolódás a Microsoft-fiók használatával című engedéllyel. Ezzel az engedéllyel az alkalmazás a `sub` jogcím formájában egyedi azonosítót kaphat a felhasználó számára. Emellett hozzáférést biztosít az alkalmazásnak az UserInfo-végponthoz is. A `openid` hatókört a Microsoft Identity platform token végpontján használhatja azonosító tokenek megszerzéséhez, amelyet az alkalmazás használhat hitelesítésre.

### <a name="email"></a>e-mail

A `email` hatókör a `openid` hatókörrel és másokkal is használható. Az alkalmazás az `email` jogcím formájában hozzáférést biztosít a felhasználó elsődleges e-mail-címéhez. A `email` jogcímet csak akkor tartalmazza a token, ha a felhasználói fiókhoz társított e-mail-cím, amely nem mindig az eset. Ha a `email` hatókört használja, az alkalmazásnak fel kell készülnie arra az esetre, amikor az `email` jogcím nem létezik a jogkivonatban.

### <a name="profile"></a>Profil

A `profile` hatókör a `openid` hatókörrel és másokkal is használható. Hozzáférést biztosít az alkalmazásnak a felhasználóval kapcsolatos jelentős mennyiségű információhoz. Az általa elérhető információ magában foglalja a következőket:, de nem kizárólagosan, a felhasználó vezetéknevét, vezetéknevét, előnyben részesített felhasználónevét és objektum-AZONOSÍTÓját. Az adott felhasználóhoz tartozó id_tokens paraméterben elérhető profil jogcímek teljes listájáért tekintse meg a [`id_tokens` referenciát](id-tokens.md).

### <a name="offline_access"></a>offline_access

A [`offline_access` hatóköre](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) lehetővé teszi az alkalmazás számára, hogy a felhasználó nevében egy hosszabb ideig hozzáférjen az erőforrásokhoz. A hozzájárulás lapon ez a hatókör jelenik meg, mivel "a hozzáférés biztosítása az Ön számára biztosított adathozzáféréshez" jogosultságot. Ha a felhasználó jóváhagyja a `offline_access` hatókört, az alkalmazás a Microsoft Identity platform jogkivonat-végpontján fogadhatja a frissítési jogkivonatokat. A frissítési tokenek hosszú életűek. Az alkalmazás új hozzáférési jogkivonatokat kaphat, mint a régebbiek lejárnak.

Ha az alkalmazás nem kéri explicit módon a `offline_access` hatókörét, nem kap frissítési jogkivonatokat. Ez azt jelenti, hogy amikor bevált egy engedélyezési kódot a [OAuth 2,0-es engedélyezési kód folyamatában](active-directory-v2-protocols.md), csak a `/token` végpont hozzáférési jogkivonatát fogja kapni. A hozzáférési jogkivonat rövid ideig érvényes. A hozzáférési jogkivonat általában egy órán belül lejár. Ezen a ponton az alkalmazásnak újra kell irányítani a felhasználót a `/authorize` végpontra egy új engedélyezési kód beszerzéséhez. Az átirányítás során az alkalmazás típusától függően előfordulhat, hogy a felhasználónak újra be kell írnia a hitelesítő adatait, vagy újra meg kell adnia az engedélyeket. Amíg a kiszolgáló automatikusan kéri a `offline_access` hatókörét, az ügyfélnek továbbra is kérnie kell, hogy megkapja a frissítési jogkivonatokat.

További információ a frissítési tokenek beszerzéséről és használatáról: a [Microsoft Identity platform protokolljának referenciája](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Egyéni felhasználói engedély kérése

Az [OpenID Connect vagy a OAuth 2,0](active-directory-v2-protocols.md) engedélyezési kérelmében az alkalmazás a `scope` lekérdezési paraméterrel kérheti le a szükséges engedélyeket. Ha például egy felhasználó bejelentkezik egy alkalmazásba, az alkalmazás a következő példához hasonló kérelmet küld (az olvashatóság érdekében sortöréssel bővült):

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

A `scope` paraméter az alkalmazás által kért delegált engedélyek szóközzel tagolt listája. Az engedélyek az erőforrás azonosítójának (az alkalmazás-azonosító URI-ja) hozzáfűzésével vannak jelezve. A kérelem példájában az alkalmazásnak engedéllyel kell rendelkeznie a felhasználó naptárának olvasásához és a levelezés felhasználóként való elküldéséhez.

Miután a felhasználó beírja a hitelesítő adatait, a Microsoft Identity platform-végpont ellenőrzi a *felhasználói beleegyezési*rekordokat. Ha a felhasználó nem járult hozzá a korábban kért engedélyekhez, és a rendszergazda nem fogadta el ezeket az engedélyeket a teljes szervezet nevében, a Microsoft Identity platform végpontja kéri a felhasználót, hogy adja meg a kért engedélyeket.

> [!NOTE]
> Ebben az esetben a `offline_access` ("a hozzáférés megtartása az Ön által megadott adathozzáféréshez") és a `user.read` ("bejelentkezés és a profil olvasása") engedélyek automatikusan beletartoznak az alkalmazás kezdeti beleegyezőbe.  Ezek az engedélyek általában szükségesek az alkalmazás megfelelő működéséhez – `offline_access` lehetővé teszi az alkalmazás számára, hogy a natív és a webes alkalmazások számára kritikus frissítési jogkivonatokat biztosítson, míg a `user.read` hozzáférést biztosít a `sub` jogcímek számára, így az ügyfél vagy az alkalmazás helyesen azonosíthatja a felhasználót az idő múlásával és a felhasználói adatokhoz való hozzáféréshez.  

![A munkahelyi fiók beleegyezikét bemutató képernyőkép](./media/v2-permissions-and-consent/work_account_consent.png)

Ha a felhasználó jóváhagyja az engedély kérését, a rendszer rögzíti a hozzájárulást, és a felhasználónak nem kell újból megadnia az alkalmazásba való bejelentkezést követően.

## <a name="requesting-consent-for-an-entire-tenant"></a>A teljes bérlőre vonatkozó belefoglalási kérelem

Ha egy szervezet egy alkalmazás licencét vagy előfizetését vásárolja meg, a szervezet a szervezet minden tagja számára proaktívan szeretné beállítani az alkalmazást. Ennek a folyamatnak a részeként a rendszergazda beleegyezik abba, hogy az alkalmazás a bérlő bármely felhasználója nevében működjön. Ha a rendszergazda hozzájárulást ad a teljes bérlőhöz, a szervezet felhasználóinak nem fog megjelenni az alkalmazás beleegyező lapja.

Ha a bérlő összes felhasználója számára szeretne beleegyezni a delegált engedélyekkel, az alkalmazás használhatja a rendszergazdai engedélyezési végpontot.

Emellett az alkalmazásoknak a rendszergazdai engedélyezési végpontot kell használniuk az alkalmazások engedélyeinek igényléséhez.

## <a name="admin-restricted-permissions"></a>Rendszergazdai hozzáférésre korlátozott engedélyek

A Microsoft ökoszisztéma bizonyos magas jogosultsági szintű engedélyei a *rendszergazda által korlátozottra*állíthatók be. Ilyen típusú engedélyek például a következők:

* Az összes felhasználó teljes profiljának olvasása `User.Read.All` használatával
* Adatírás a szervezet könyvtárába `Directory.ReadWrite.All` használatával
* A szervezet címtárában lévő összes csoport olvasása `Groups.Read.All` használatával

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

A rendszergazdai jóváhagyás nem fogad el hatókör-paramétert, ezért a kért engedélyeket statikusan meg kell határozni az alkalmazás regisztrációjában. Általánosságban az ajánlott eljárás az, hogy az adott alkalmazáshoz statikusan meghatározott engedélyek a dinamikusan/növekményes igényeknek megfelelő engedélyek kibővítettek legyenek.

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

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Paraméter     | Állapot     | Leírás                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Kötelező | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. A (z) GUID vagy a felhasználóbarát név formátumban adható meg, vagy általános módon hivatkozik `common`re, ahogy az a példában látható. |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `redirect_uri` | Kötelező |Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével. |
| `state` | Ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. A kívánt tartalom sztringje lehet. Az állapot használatával kódolja a felhasználó állapotára vonatkozó adatokat az alkalmazásban, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
|`scope`        | Kötelező      | Meghatározza az alkalmazás által igényelt engedélyek készletét. Ez lehet statikus (/.default használatával) vagy dinamikus hatókörök használata.  Ilyenek lehetnek a OIDC hatókörök (`openid`, `profile`, `email`). | 


Ezen a ponton az Azure AD-nek a bérlői rendszergazdának kell bejelentkeznie a kérelem teljesítéséhez. A rendszergazda a `scope` paraméterben kért összes engedély jóváhagyását kéri.  Ha statikus (`/.default`) értéket használt, úgy fog működni, mint a v 1.0 rendszergazdai engedélyezési végpont, és az alkalmazáshoz szükséges engedélyekben található összes hatókörre vonatkozó kérelem beleegyezését kéri.

#### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva. |
| `state` | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |
| `admin_consent` | `True`értékre lesz állítva. |

#### <a name="error-response"></a>Hiba válasza

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőre hasonlít:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hibák kiváltó okának azonosításában. |

Miután sikeres választ kapott a rendszergazdai jogosultsági végponttól, az alkalmazás megszerezte a kért engedélyeket. Ezután kérheti a kívánt erőforráshoz tartozó jogkivonatot.

## <a name="using-permissions"></a>Engedélyek használata

Miután a felhasználó beleegyezett az alkalmazás engedélyeibe, az alkalmazás képes olyan hozzáférési jogkivonatok beszerzésére, amelyek képviselik az alkalmazás számára, hogy bizonyos kapacitásban hozzáférjenek az erőforrásokhoz. Hozzáférési jogkivonat csak egyetlen erőforráshoz használható, de a hozzáférési jogkivonaton belül kódolt minden engedély, amelyet az alkalmazás az adott erőforráshoz adott meg. Hozzáférési jogkivonat beszerzéséhez az alkalmazás kérheti a Microsoft Identity platform jogkivonat-végpontját, amely a következőképpen néz ki:

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

Az eredményül kapott hozzáférési jogkivonatot a HTTP-kérelmekben az erőforráshoz is használhatja. Megbízhatónak tekinti azt az erőforrást, amelyet az alkalmazás megfelelő engedéllyel adott feladat végrehajtásához. 

A OAuth 2,0 protokollról és a hozzáférési jogkivonatok beszerzéséről a [Microsoft Identity platform Endpoint Protocol-referenciájában](active-directory-v2-protocols.md)talál további információt.

## <a name="the-default-scope"></a>A/.default hatóköre

A `/.default` hatókörrel az alkalmazások a 1.0-s végpontról a Microsoft Identity platform végpontra történő áttelepíthetők. Ez egy beépített hatókör minden olyan alkalmazáshoz, amely az alkalmazás regisztrációján konfigurált engedélyek statikus listájára hivatkozik. A `https://graph.microsoft.com/.default` `scope` értéke ugyanúgy működik, mint a v 1.0-s végpontok `resource=https://graph.microsoft.com` – nevezetesen, ha az alkalmazás által a Azure Portalban regisztrált hatókörrel rendelkező jogkivonatot kér Microsoft Graph.

A/.default-hatókör bármely OAuth 2,0 [-](v2-oauth2-on-behalf-of-flow.md) flow-ban használható, de szükséges a folyamaton belüli és [ügyfél-hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md)során.  

> [!NOTE]
> Az ügyfelek nem kombinálhatók egyetlen kérelemben a statikus (`/.default`) és a dinamikus beleegyező engedélyekkel. Így a `scope=https://graph.microsoft.com/.default+mail.read` a hatókör-típusok kombinációja miatt hibát okoz.

### <a name="default-and-consent"></a>/.default és beleegyezett

A `/.default` hatókör a v 1.0 végpont viselkedését váltja fel `prompt=consent` esetén is. Az alkalmazás által regisztrált összes engedélyre vonatkozó beleegyezik, az erőforrástól függetlenül. Ha a kérelem részeként szerepel, a `/.default` hatókör olyan tokent ad vissza, amely tartalmazza a kért erőforrás hatóköreit.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, ha a felhasználó már megadott egy engedélyt

Mivel a `/.default` a `resource`-központú v 1.0 végpont működésével azonos, az a v 1.0 végpont beleegyezési viselkedésével is jár. A `/.default` csak akkor aktiválja a beleegyezési kérést, ha a felhasználó nem kapott engedélyt az ügyfél és az erőforrás között. Ha ilyen beleegyezik, akkor a rendszer visszaadja a tokent, amely a felhasználó által az adott erőforráshoz megadott összes hatókört tartalmazza. Ha azonban nem adta meg az engedélyt, vagy a `prompt=consent` paraméter meg van adva, a rendszer egy beleegyezési kérést jelenít meg az ügyfélalkalmazás által regisztrált összes hatókörhöz.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>1\. példa: a felhasználó vagy a bérlő rendszergazdája engedélyt kapott

A felhasználó (vagy egy bérlői rendszergazda) engedélyezte az ügyfél számára a Microsoft Graph engedélyeket `mail.read` és `user.read`. Ha az ügyfél `scope=https://graph.microsoft.com/.default`kérelmet küld, akkor a rendszer nem jelenít meg beleegyező kérést, függetlenül attól, hogy az ügyfélalkalmazások milyen tartalmat regisztráltak a Microsoft Graphhoz. `mail.read` és `user.read`hatóköröket tartalmazó tokent ad vissza.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>2\. példa: a felhasználó nem adott meg engedélyeket az ügyfél és az erőforrás között

Nem létezik beleegyezés a felhasználó számára az ügyfél és a Microsoft Graph között. Az ügyfél regisztrálva van a `user.read` és `contacts.read` engedélyekhez, valamint a Azure Key Vault hatókör `https://vault.azure.net/user_impersonation`. Amikor az ügyfél `scope=https://graph.microsoft.com/.default`jogkivonatot kér, a felhasználó a `user.read`, `contacts.read`és Key Vault `user_impersonation` hatókörökhöz tartozó beleegyezési képernyőt fog látni. A visszaadott token csak a `user.read` és `contacts.read` hatóköröket fogja tartalmazni.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>3\. példa: a felhasználó beleegyezett, és az ügyfél további hatóköröket kér

A felhasználó már beleegyezett `mail.read` az ügyfélhez. Az ügyfél regisztrálva van a `contacts.read` hatókörében a regisztrációjában. Ha az ügyfél `scope=https://graph.microsoft.com/.default` és a kérések beleegyezésével kéri a tokent a `prompt=consent`, a felhasználó csak a beleegyezési képernyőt fogja látni, és az alkalmazás által regisztrált összes engedélyt. a `contacts.read` a beleegyezési képernyőn jelennek meg, de `mail.read` nem fog megjelenni. A visszaadott token Microsoft Graph lesz, és `mail.read` és `contacts.read`tartalmaz majd.

### <a name="using-the-default-scope-with-the-client"></a>A/.default hatókör használata az ügyféllel

A `/.default` hatókörének különleges esete létezik, ahol az ügyfél a saját `/.default` hatókörét kéri le. A következő példa bemutatja ezt a forgatókönyvet.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ez egy beleegyezési képernyőt hoz létre minden regisztrált engedélyhez (ha alkalmazható a beleegyezés és a `/.default`fenti leírása alapján), akkor egy hozzáférési jogkivonat helyett egy id_token ad vissza.  Ez a viselkedés bizonyos, a ADAL-ből a MSAL-re áthelyezett örökölt ügyfelek esetében létezik, és a Microsoft Identity platform végpontját célzó új ügyfelek nem használhatják.  

## <a name="troubleshooting-permissions-and-consent"></a>Hibaelhárítási engedélyek és beleegyezett

Ha Ön vagy az alkalmazás felhasználóinak váratlan hibába ütközik a beleegyező folyamat során, tekintse meg ezt a cikket a hibaelhárítási lépésekhez: [váratlan hiba történt az alkalmazáshoz való beleegyező művelet végrehajtásakor](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
