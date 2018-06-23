---
title: Mi az az Azure AD v2.0-végpontra a különböző? | Microsoft Docs
description: Az összehasonlítás között az eredeti Azure AD és a v2.0-végpontok.
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
ms.openlocfilehash: 1d82f9a8c9ba6bcdf9914337c7ca0eadaccf17a0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318474"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Mi az a v2.0-végpontra vonatkozó különböző?

Ha megismerheti az Azure Active Directory (Azure AD), vagy rendelkezik integrált alkalmazások az Azure AD a múltban, néhány különbségek vannak a v2.0-végponttal, amely előfordulhat, hogy nem várt. Ez a cikk az eltéréseket megértését hív meg.

> [!NOTE]
> Nem minden Azure AD-forgatókönyvek és funkciók a v2.0-végpontra által támogatott. Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft-fiókok és az Azure AD-fiókok

A v2.0-végpontra lehetővé teszi a fejlesztők számára, hogy a bejelentkezés a Microsoft Accounts és az Azure AD fiókokhoz, egy egyetlen hitelesítési végpont használatával kezelő alkalmazások írása. Ez lehetővé teszi az alkalmazás teljesen fiók-független, ami azt jelenti, lehet, hogy az alkalmazás milyen típusú fiókot, amely a felhasználó jelentkezik be az vizsgálatot írjanak. Hogy az alkalmazás használatát egy adott munkamenet használt fiók típusának, de nem szükséges.

Például ha az alkalmazás a [Microsoft Graph](https://graph.microsoft.io), néhány további funkciók és adat lesz elérhető a vállalati felhasználók számára, például a SharePoint-webhelyek vagy a címtáradatok. De több művelethez például [a felhasználó e-mail olvasása](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), a kódot is beírhatók ugyanúgy Microsoft Accounts és az Azure AD-fiókok. 

Az alkalmazás integrálása Microsoft Accounts és az Azure AD-fiókok mostantól egy egyszerű folyamat. Végpontok, egyetlen könyvtár és egyetlen meghatározott alkalmazás regisztráció egyetlen halmazába használhatja az ügyfél és a vállalati világot eléréséhez. A v2.0-végpontra vonatkozó további információkért tekintse meg [a áttekintése](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Új alkalmazás-regisztrációs portál

Egy alkalmazás, amely kompatibilis a v2.0-végpontra regisztrálásához a Microsoft app-regisztrálási portál kell használnia: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Ez az a portál egy alkalmazás azonosítója szerezhet az alkalmazás bejelentkezési oldalak megjelenésének testreszabásához. A portál eléréséhez szüksége az alkalmazás bekapcsolja Microsoft-fiók - személyes vagy a munkahelyi vagy iskolai fiókjával.

## <a name="one-app-id-for-all-platforms"></a>Az összes platformra egy alkalmazás azonosítója

Ha már használta az Azure AD, valószínűleg már regisztrálta egy projekthez több különböző alkalmazást. Például ha a webhely és a iOS-alkalmazást, kellett külön-külön, regisztrálja őket két különböző alkalmazás-azonosító segítségével. Az Azure AD alkalmazás-regisztrációs portál módosítására kényszeríti, így ezt a különbséget a regisztráció során:

![Régi regisztrációja felhasználói felület](../../media/active-directory-v2-flows/old_app_registration.PNG)

Hasonlóképpen ha egy webhely és a háttérkiszolgáló webes API-t, előfordulhat, hogy regisztrálta az egyes külön alkalmazásként az Azure AD. Vagy ha egy iOS-alkalmazás- és Android-alkalmazás, is előfordulhat, hogy regisztrálta két különböző alkalmazások. Az alkalmazás minden összetevő regisztrálása alapján néhány váratlan viselkedés a fejlesztők és az ügyfeleknek:

* Minden egyes összetevő jelent meg az Azure AD-bérlő minden ügyfél az külön alkalmazásként.
* A bérlői rendszergazda megkísérelt szeretné alkalmazni a szabályzatot, való hozzáférés kezelése, vagy törölje az alkalmazást, akkor ehhez az alkalmazáshoz tartozó kellene.
* Ha az ügyfelek átadni kívánt hozzájárult e egy alkalmazást, minden egyes összetevő egy különálló alkalmazás, a hozzájárulási képernyő jelenik meg.

A v2.0-végponttal mostantól egy egyetlen meghatározott alkalmazás regisztrálása a projekt összetevők regisztrálásához, és a teljes projektet egy egyetlen alkalmazás-azonosító használata. Több "platformok" hozzáadása egy minden olyan projekthez, és adja meg a megfelelő adatokat ad hozzá minden egyes platformhoz. Természetesen a követelményeitől függően, de a legtöbb esetben csak egy alkalmazásazonosítót legyen szükség annyi alkalmazásokat is létrehozhat.

Az célja, hogy ezzel előfordulhat, hogy egy több egyszerűsített felügyeleti és fejlesztői környezetet, és hozzon létre egy projekt, amely akkor működik a több egyesített nézetét.

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

Az Azure AD-alkalmazás viselkedhet, mint egy **erőforrás**, vagy a jogkivonatok címzett. Erőforrás definiálhat számos **hatókörök** vagy **oAuth2Permissions** , hogy megértette, így az ügyfél-alkalmazások jogkivonatai és az adott erőforrás hatókörök bizonyos számú kérelem. Fontolja meg az Azure AD Graph API példa bemutatja, erőforrás:

* Erőforrás-azonosítót, vagy `AppID URI`: `https://graph.windows.net/`
* Hatókörök, vagy `OAuth2Permissions`: `Directory.Read`, `Directory.Write`stb. 

Mindez igaz a v2.0-végponttól. Az alkalmazások továbbra is viselkedhet erőforrásként, adja meg a hatókörök és azonosítható, ha egy URI-t. Ügyfél alkalmazások továbbra is kérhet ezeket a hozzáférést. Azonban, hogy egy ügyfél lekérdezi ezeket az engedélyeket a módszer megváltozott. A múltban, az OAuth 2.0 engedélyezik az Azure AD-kérelem lehet, hogy rendelkezik keresést végrehajtani, például:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

Ha a **erőforrás** paraméter jelzi, hogy melyik erőforrást az ügyfélalkalmazás engedélyt kér. Az Azure AD számított az alkalmazást az Azure portálon statikus konfigurációján alapul, és ennek megfelelően kiadott jogkivonatokat szükséges engedélyeket. Most az azonos OAuth 2.0 engedélyezik a kérelem tűnik:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Ha a **hatókör** paraméter azt jelzi, melyik erőforrást és engedélyek a az alkalmazás által kért engedélyezését. A kérelem jelen-e a kívánt erőforrás - egyszerűen lefedett minden, a hatókör-paraméter értékének. Ezen a módon kikapcsolja a hatókör-paraméter használata lehetővé teszi, hogy a v2.0-végponttal kell több specifikációnak az OAuth 2.0-s, és igazodik jobban közös iparági eljárásokat. Emellett lehetővé teszi az alkalmazások végrehajtásához [növekményes hozzájárulási](#incremental-and-dynamic-consent), amely a következő szakaszban ismertetett.

## <a name="incremental-and-dynamic-consent"></a>Növekményes és dinamikus hozzájárulás

Alkalmazások korábban szükséges app létrehozáskor az Azure-portálon az OAuth 2.0 szükséges engedélyeket adja meg Azure AD-ben regisztrált:

![Engedélyek regisztrációs felhasználói felület](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

A szükséges alkalmazás be lett állítva engedélyek **statikusan**. Ennek az alkalmazásnak, hogy az Azure portálon található konfigurációja engedélyezett, és tartani a kód töltött és egyszerű, azt is problémás lehet néhány fejlesztőknek:

* Egy alkalmazás kellett ismeri az összes alkalmazás létrehozáskor átállítására lenne kellene engedélyeit. Engedélyek hozzáadása adott idő alatt volt nehéz folyamat.
* Egy alkalmazás kellett ismeri az összes azt minden eddiginél elérésére időben az erőforrásokat. Hozzon létre, amely hozzáférhet erőforrások tetszőleges számú alkalmazások bonyolult volt.
* Egy alkalmazás kellett legalább egyszer kellene a felhasználó első bejelentkezés után minden engedélyek kéréséhez. Bizonyos esetekben a kereslet az engedélyek listája túl hosszú, amely javasolt az alkalmazás eléréséhez a kezdeti bejelentkezés jóváhagyása a végfelhasználók számára.

A v2.0-végpontra is megadhat az engedélyeket az alkalmazás igényeinek **dinamikusan**, futásidőben, az alkalmazás rendszeres használat során. Ehhez az szükséges, adja meg a hatókörök a kell álljon idő szerint, beleértve azokat a `scope` engedélyezési kérések paraméter:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

A fenti kérelmek engedéllyel az alkalmazásnak, hogy az Azure AD-felhasználó címtáradatok, valamint adatokat írni a címtár olvasása. Ha a felhasználó a korábban az adott alkalmazás hozzájárult ezeket az engedélyeket, adja meg a hitelesítő adataikat, és írja alá az alkalmazásba. Ha a felhasználó nem hozzájárult bármelyik ezeket az engedélyeket, a v2.0-végpontra kéri a felhasználó hozzájárul ezeket az engedélyeket. További tudnivalókért olvashatók a [engedélyek, beleegyezése és hatókörök](active-directory-v2-scopes.md).

Így dinamikusan osztályig egy alkalmazást, és kérjen engedélyeket a `scope` paraméter teljes ellenőrzést a felhasználói élmény ad meg. Ha kívánja, ha szeretné, Ön hozzájárul észlelnek, és egy kezdeti hitelesítési kérelem minden engedélyt kér frontload. Vagy ha az alkalmazás számos engedélyek megköveteli, ha szeretné, gyűjtsön ezeket az engedélyeket a felhasználó Növekményesen, módon tudják használni az alkalmazás egyes funkcióinak adott idő alatt.

## <a name="well-known-scopes"></a>Az ismert hatókörök

### <a name="offline-access"></a>Kapcsolat nélküli hozzáférés

A v2.0-végpontra használó alkalmazásokat lehet szükség a egy új jól ismert engedély - alkalmazások a `offline_access` hatókör. Minden alkalmazás kell igényelnie ezt az engedélyt, ha egy hosszabb ideig, még akkor is, ha a felhasználó nem aktívan használhatja az alkalmazást egy felhasználó nevében lévő erőforrások eléréséhez szükséges. A `offline_access` hatókör fog megjelenni a felhasználónak a hozzájárulási párbeszédpanelek, mint a "Fér hozzá az adatokhoz offline", amely a felhasználó elfogadja kell. A kért a `offline_access` engedély lehetővé teszi a webes alkalmazás OAuth 2.0 refresh_tokens fogadása a v2.0-végponttól. Refresh_tokens hosszú élettartamú, és új OAuth 2.0 access_tokens access hosszú időn keresztül kicserélhetők. 

Ha az alkalmazás nem kér a `offline_access` hatókör, nem kapják meg refresh_tokens. Ez azt jelenti, hogy ha egy authorization_code az OAuth 2.0 hitelesítésikód-folyamata a beváltásához csak megkapja vissza egy access_token a a `/token` végpont. Adott access_token rövid időn (általában egy órán keresztül) érvényes marad, de végül lejár. Ebben az időpont, az alkalmazás átirányítja a felhasználót kell biztonsági másolatot a `/authorize` egy új authorization_code beolvasni végpontot. Az átirányítás során a felhasználó is, vagy nem esetleg írja be újra a hitelesítő adataikat, vagy újra járul hozzá az engedélyeket, attól függően, hogy az alkalmazás típusa.

OAuth 2.0-s, refresh_tokens és access_tokens kapcsolatos további tudnivalókért tekintse meg a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, a profil és az e-mailek

Hagyományosan a legalapvetőbb OpenID Connect bejelentkezési folyamata az Azure AD akkor adja meg az eredményül kapott id_token a felhasználóra vonatkozó információk számos. Egy id_token lévő jogcímet a felhasználó nevét, elsődleges felhasználónév, e-mail címét, objektumazonosító: és több tartalmazhatnak.

Az adatokat, amelyek a `openid` hatókör számára biztosítja az alkalmazáshoz való hozzáférés jelenleg korlátozott. A `openid` hatókör csak akkor engedélyezi az alkalmazásnak, hogy jelentkezzen be a felhasználó és kap a felhasználó alkalmazás-specifikus azonosítója. Ha le szeretné tölteni az alkalmazásban a felhasználó személyes adatait, az alkalmazás további engedélyek kéréséhez a felhasználónak a kell. Két új hatókört – a `email` és `profile` hatókörök – lehetővé teszi további engedélyek kéréséhez.

A `email` hatókör lehetővé teszi, hogy az alkalmazás eléréséhez a felhasználó elsődleges e-mail címéhez keresztül a `email` a id_token a jogcímek. 

A `profile` objektumazonosító hatókör számára biztosítja az alkalmazás eléréséhez a felhasználó – a neve, az elsődleges felhasználónév, minden más alapvető adatait, és így tovább.

Ez lehetővé teszi, hogy az alkalmazás minimális nyilvánosságra módon code – csak a felhasználó megkérheti a fontos, hogy az alkalmazás a feladat elvégzéséhez szükséges információkat. Ezek a hatókörök további információkért lásd: [a v2.0 hatókör-hivatkozást](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Jogkivonatok jogcímei

A v2.0-végpontra által kiállított jogkivonatokat a jogcím nem lesz a nyilvánosan elérhető által kiállított jogkivonatokat megegyezik az Azure AD-végpontok. Alkalmazások áttelepítése az új szolgáltatáshoz nem azt feltételezik, egy adott jogcím van jelen id_tokens vagy access_tokens. A megadott v2.0 jogkivonatokba kibocsátott jogcímeket, lásd: a [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md).

## <a name="limitations"></a>Korlátozások

Nincsenek néhány korlátozásokat kell ügyelnie, ha a v2.0 ponttal. Ha bármelyik e korlátozások vonatkoznak az adott forgatókönyv kapcsolatban lásd: a [v2.0 korlátozások doc](active-directory-v2-limitations.md).
