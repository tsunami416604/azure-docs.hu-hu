---
title: Mi az az Azure AD v2.0-végpont? | Microsoft Docs
description: Az eredeti Azure AD-összehasonlítása és a v2.0-végpontok.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581593"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Mi a v2.0-végponttal kapcsolatban?

Ha ismeri az Azure Active Directory (Azure AD) vagy az Azure AD-korábban integrálta az alkalmazások, nincsenek a v2.0-végpont nem várt lehet, hogy néhány eltérés. Ez a cikk hívja meg az eltéréseket a megértését.

> [!NOTE]
> Nem minden Azure AD-forgatókönyvek és funkciók támogatottak a v2.0-végpontra. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft-fiókok és az Azure AD-fiókok

A v2.0-végpont lehetővé teszi a fejlesztők számára, hogy fogadja el, jelentkezzen be Microsoft-Accounts és az Azure AD fiókok, egy egyetlen hitelesítési végpont használatával az alkalmazások írási. Ezáltal az alkalmazás teljesen fiók-független, ami azt jelenti, hogy az alkalmazás milyen típusú fiókkal, amely a felhasználó által a bejelentkezéshez vizsgálatot lehet írni. Az alkalmazás használatban van egy adott munkamenet fiókja típusának figyelembe teheti, de nem szükséges.

Például ha az alkalmazás meghívja a [Microsoft Graph](https://graph.microsoft.io), néhány további funkciókat és adat lesz elérhető a vállalati felhasználók számára, például a SharePoint-webhelyek vagy a címtáradatok. Azonban számos műveletek végrehajtásához például [a felhasználók e-mailjeinek olvasása](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), a kód is írható ugyanúgy Microsoft Accounts és az Azure AD-fiókok. 

Az alkalmazás integrálása az Azure ad-ben és a Microsoft Accounts már egy egyszerű folyamattal. Végpontokat, a egyetlen szalagtár és a egy egyetlen alkalmazásregisztráció egyetlen készletét használhatja a fogyasztói és a vállalati világok eléréséhez. A v2.0-végpont kapcsolatos további információkért tekintse meg [az Áttekintés](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Új alkalmazás-regisztrációs portál

Regisztráljon egy alkalmazást, amely a v2.0-végponttal működik, a Microsoft app-regisztrálási portál kell használnia: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Ez az a portálon, hol szerezheti be az Alkalmazásazonosítót, szabja testre az alkalmazás bejelentkezési oldalára, és egyéb. A portál elérésére szüksége egy működtetett Microsoft-fiók – személyes vagy a munkahelyi vagy iskolai fiókjával.

## <a name="one-app-id-for-all-platforms"></a>Az összes platformra egy alkalmazás azonosítója

Ha korábban már használta az Azure ad-ben, valószínűleg regisztrálta egy projekthez számos különböző alkalmazásokat. Például ha a webhely és a egy iOS-alkalmazást, kellett külön-külön, regisztrálja őket két különböző alkalmazás-azonosító használatával. Az Azure AD alkalmazás-regisztrációs portál kényszerített, ezt a különbséget regisztrációja során győződjön meg arról, hogy:

![Régi felhasználói felület Alkalmazásregisztráció](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

Hasonlóképpen ha a webhely- és webes api-háttérrendszer, előfordulhat, hogy regisztrált minden egyes elkülönített alkalmazásként az Azure ad-ben. Vagy ha iOS-alkalmazás és a egy Android-alkalmazás, is előfordulhat, hogy regisztrált két különböző alkalmazásokat. Az alkalmazás minden egyes összetevő regisztrálása alapján néhány váratlan viselkedést a fejlesztők és ügyfelek:

* Az egyes összetevők jelent meg az egyes ügyfelek az Azure AD-bérlő külön alkalmazásként.
* A megkísérelt egy Bérlői rendszergazda a alkalmazni a szabályzatot, való hozzáférés kezelése, vagy törölhet alkalmazásokat, azok ehhez az alkalmazás mindegyik összetevőjéhez kellene.
* Ha ügyfél által jóváhagyott egy alkalmazást, az egyes összetevők a jóváhagyást kérő képernyőt, külön alkalmazásként jelenik meg.

A v2.0-végponttal ezután a projekt összes összetevő regisztráljon egy egyetlen alkalmazás regisztrációs, és egyetlen alkalmazás-azonosító használata a teljes projekt. Számos "platformra" hozzáadása egy minden projekthez, és adja meg a megfelelő adatokat ad hozzá minden egyes platformhoz. Természetesen annyi alkalmazások követelményektől függően, de a legtöbb esetben csak egy alkalmazásazonosítót kell szükséges is létrehozhat.

Az célja, hogy ezzel vezethet egy több egyszerűsített felügyeleti és fejlesztési környezetet biztosít, és hozzon létre több nézetet jelenít meg a projekten dolgozik, az egyetlen projektet.

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

Az Azure ad-ben, egy alkalmazás működhet, mint egy **erőforrás**, vagy egy címzett jogkivonatokat. Erőforrás definiálhat számos **hatókörök** vagy **oAuth2Permissions** , hogy tisztában van azzal, így a hatókörök bizonyos készletének erőforrás-jogkivonat kérése az alkalmazásokat. Az Azure AD Graph API egy adott erőforrás példaként, vegye figyelembe:

* Erőforrás-azonosító, vagy `AppID URI`: `https://graph.windows.net/`
* Hatókörök, vagy `OAuth2Permissions`: `Directory.Read`, `Directory.Write`stb. 

Mindezt igaznak a v2.0-végpont. Egy alkalmazás továbbra is működhet, erőforrásként hatókörök meghatározása, és egy URI segítségével azonosítható. Ügyfélalkalmazások továbbra is kérhetnek ezeket a hozzáférést. Azonban, hogy egy ügyfél igényel-e ezeket az engedélyeket a módszer megváltozott. Múltbeli időpont, az OAuth 2.0 engedélyezése az Azure AD-kérelem lehet, hogy rendelkezik hasonlított:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

Ha a **erőforrás** paraméter jelzi, hogy melyik erőforrást az ügyfélalkalmazásnak az engedélyezési kér. Azure ad-ben az alkalmazás az Azure Portalon statikus konfiguráció alapján, és ennek megfelelően kiadott jogkivonatokat szükséges engedélyek számított. Most az ugyanazon az OAuth 2.0 engedélyezi a kérelem tűnik:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Ha a **hatókör** paraméter azt jelzi, hogy melyik erőforrást, és az engedélyek az alkalmazás hitelesítést kér. A kívánt erőforrást. a kérés továbbra is megtalálhatók –, egyszerűen a az egyes értékei közül a hatókör-paramétert vonatkozik. Ily módon a hatókör-paraméter használatával lehetővé teszi, hogy az OAuth 2.0 ismertetőjének több megfeleljenek a v2.0-végpont, és jobban igazodik az általános iparági eljárásoknak megfelelő beállításában. Lehetővé teszi alkalmazások végrehajtásához [növekményes hozzájárulási](#incremental-and-dynamic-consent), amely a következő szakaszban leírt.

## <a name="incremental-and-dynamic-consent"></a>A növekményes és a dinamikus jóváhagyás

Alkalmazások korábban szükséges az OAuth 2.0 szükséges engedélyeket adja meg az Azure Portalon alkalmazás létrehozáskor az Azure AD-ben regisztrált:

![Engedélyek regisztrációs felhasználói felület](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

A szükséges alkalmazás konfigurált engedélyekkel **statikusan**. Bár ez az alkalmazás az Azure Portalon található konfigurációs engedélyezett, és tárolni a kódot már jól néz kis- és egyszerű, azt is problémás lehet néhány fejlesztők számára:

* Egy alkalmazás kellett ismeri az összes alkalmazás létrehozáskor minden eddiginél kellene az engedélyeket. Engedélyek hozzáadása idővel volt bonyolult folyamat.
* Egy alkalmazás összes olyan erőforrást, minden eddiginél elérésére előre tudni rendelkezett. Alkalmazások létrehozása, amelyek hozzáférnek az erőforrásokhoz tetszőleges számú nehéz volt.
* Alkalmazás volna valaha kellene a felhasználó első bejelentkezés után minden engedélyek kéréséhez. Bizonyos esetekben ez vezetett engedélyek hosszú listáját, amelyek végfelhasználók számára az alkalmazás eléréséhez a kezdeti bejelentkezési jóváhagyása nem ajánlott.

A v2.0-végponttal, megadhatja az engedélyeket az alkalmazás igényeinek megfelelően **dinamikusan**, futásidőben, az alkalmazás normál használat során. Ehhez adja meg az alkalmazás van szüksége adott időben fel őket a hatóköröket a `scope` egy engedélyezési kérést paraméteréhez:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

A fenti kérelmek engedélyt az alkalmazásnak a directory-adatokat egy Azure AD-felhasználó, valamint adatokat írni a címtár olvasását. Ha a felhasználó hozzájárult, ezeket az engedélyeket az adott alkalmazás esetében a múltban, adja meg a hitelesítő adataikat, és be van jelentkezve az alkalmazás. Ha a felhasználó nem egyezett bele bármelyik ezeket az engedélyeket, a v2.0-végpont ilyen engedéllyel, hogy a felhasználó kéri. További tudnivalókért olvashat a [engedélyek, beleegyezése és hatókörök](v2-permissions-and-consent.md).

Engedélyek kéréséhez alkalmazás dinamikusan keresztül lehetővé teszi a `scope` paraméter teljes körűen felügyelve az a felhasználói élményt biztosít. Ha szeretné, ha szeretné frontload Ön hozzájárul a környezetet, és kérje meg az összes engedélyt egy kezdeti engedélyezési kérésben. Vagy ha az adott alkalmazáshoz szükséges engedélyek nagy számú, ha szeretné, gyűjtsön ezeket az engedélyeket a felhasználó Növekményesen, módon tudják használni az alkalmazás egyes funkcióinak idővel.

## <a name="well-known-scopes"></a>Az ismert hatókörök

### <a name="offline-access"></a>Offline elérés

Alkalmazások a v2.0-végpont használatával előfordulhat, hogy egy új jól ismert engedély használata szükséges az alkalmazások – a `offline_access` hatókör. Minden alkalmazás kell kérnie ezt az engedélyt, ha egy hosszabb ideig, még akkor is, ha a felhasználó lehet, hogy nem lehet aktívan használja az alkalmazást a felhasználó nevében lévő erőforrások eléréséhez szükséges. A `offline_access` hatókör fog megjelenni a felhasználó hozzájárulási párbeszédpanelek, mint a "Hozzáférés az adatok offline", amely a felhasználónak el kell fogadnia. Kér a `offline_access` engedély lehetővé teszi a webes alkalmazás OAuth 2.0 refresh_tokens fogadjon a v2.0-végpontra. Refresh_tokens hosszú élettartamú, és hosszabb ideig hozzáférés új OAuth 2.0 access_tokens kicserélhetők. 

Ha az alkalmazás nem kér a `offline_access` hatókör, nem fog kapni a refresh_tokens. Ez azt jelenti, hogy az az OAuth 2.0 hitelesítési kódfolyamat egy authorization_code beváltásához csak kap vissza egy access_token származó a `/token` végpont. Adott access_token rövid időn (általában egy órán keresztül) érvényes marad, de végül le fog járni. Ebben az időpontban, az alkalmazás átirányítja a felhasználót kell biztonsági másolatot a `/authorize` végpont egy új authorization_code lekéréséhez. Az átirányítás során a felhasználó is, vagy előfordulhat, hogy nem kell újra adja meg a hitelesítő adataikat, vagy újra járul hozzá az engedélyeket, az alkalmazás típusától függően.

OAuth 2.0, refresh_tokens és access_tokens kapcsolatos további információkért tekintse meg a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, a profil és az e-mailben

A legalapvetőbb OpenID Connect bejelentkezést folyamat az Azure ad-vel hagyományosan biztosítja a sok az eredményül kapott id_token a felhasználóval kapcsolatos információkat. Id_token jogcímeket a felhasználó neve, előnyben részesített felhasználónév, e-mail címét, objektumazonosító: és további tartalmazhatnak.

Az adatokat, amelyek a `openid` hatókör számára biztosítja az alkalmazás való hozzáférés jelenleg korlátozott. A `openid` hatókör csak akkor engedélyezi az alkalmazás jelentkezzen be a felhasználó és a felhasználó alkalmazás-specifikus azonosítót kap. Kívánja beszerezni az alkalmazásban a felhasználó személyes adatait, ha az alkalmazás a felhasználó további engedélyek kéréséhez kell. Két új hatókört – a `email` és `profile` hatókörök – lehetővé teszi, hogy további engedélyek kéréséhez.

A `email` hatókör lehetővé teszi, hogy a felhasználó elsődleges e-mail címét, az alkalmazás hozzáférjen a `email` a id_token a jogcím. 

A `profile` hatókör számára biztosítja az alkalmazás hozzáférjen a felhasználó számára – a név, elsődleges felhasználónév, minden egyéb alapvető adatait a objektumazonosító, és így tovább.

Ez lehetővé teszi, hogy az alkalmazás minimális nyilvánosságra módon code – a felhasználó csak a fontos, hogy az alkalmazás ehhez szükséges információkat kérhet. Ezeken a hatókörökön további információkért lásd: [a 2.0-s verziójú hatókör-hivatkozást](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Jogkivonatok jogcímei

A v2.0-végpont által kiállított jogkivonatokban lévő jogcímek nem lehet az általánosan elérhető által kiállított jogkivonatokban azonos az Azure AD-végpontok. Alkalmazások migrálása az új szolgáltatást nem azt feltételezik, egy adott jogcím id_tokens vagy access_tokens jelen. A 2.0-s verziójú jogkivonatok bocsásson ki adott jogcímek kapcsolatos további információkért tekintse meg a [v2.0 jogkivonat referenciái](v2-id-and-access-tokens.md).

## <a name="limitations"></a>Korlátozások

Érdemes figyelembe vennie a 2.0-s verziójú pont használatakor néhány korlátozások vonatkoznak. Ha bármelyik ezek a korlátozások vonatkoznak az adott forgatókönyv kapcsolatban lásd: a [v2.0 korlátozások doc](active-directory-v2-limitations.md).
