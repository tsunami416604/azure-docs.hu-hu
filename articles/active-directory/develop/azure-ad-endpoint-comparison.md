---
title: Az Azure AD v2.0-végpont az 1.0-s verziójú végponttal összehasonlítása |} A Microsoft Docs
description: Ismeri az Azure AD v2.0-végpont és az 1.0-s verziójú végpont közötti különbségek
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
ms.date: 09/27/2018
ms.author: andret
ms.reviewer: hirsin, andret
ms.custom: aaddev
ms.openlocfilehash: b75b31ddfc77be5ed651e7b8484e41a4ae73d8d8
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406532"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Az Azure AD v2.0-végpont az 1.0-s verziójú végponttal összehasonlítása

Új alkalmazás fejlesztése esetén fontos tudni, hogy az 1.0-s és 2.0-s verziójú végpontok közötti különbségeket. Az alábbiakban a fő különbségeket, valamint néhány meglévő korlátozást a v2.0-végpont.

> [!NOTE]
> Nem minden Azure Active Directory (Azure AD-) forgatókönyveket és funkciókat támogatja a v2.0-végpontra. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](#limitations).

## <a name="who-can-sign-in"></a>Ki jelentkezhet be

![Aki bejelentkezhet, 1.0-s és 2.0-s verziójú végpontok](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Az 1.0-s verziójú végpont lehetővé teszi, hogy csak munkahelyi és iskolai fiókjába való bejelentkezéshez az alkalmazást (Azure AD)

* A v2.0-végpont lehetővé teszi a munkahelyi és iskolai fiókok Azure AD-ből és személyes fiókok (MSA) (hotmail.com, outlook.com, msn.com) való bejelentkezéshez.

* 1.0-s verziója és a v2.0-végpontokra is fogadja el a bejelentkezések *[vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* egy konfigurált alkalmazások az Azure AD-címtár *[egybérlős](single-and-multi-tenant-apps.md)* vagy *több-bérlős* bérlőspecifikus végpontjára mutató konfigurált alkalmazások (`https://login.microsoftonline.com/{TenantId_or_Name}`).

A v2.0-végpont írhat alkalmazásokat, amelyek be személyes és munkahelyi és iskolai fiókok esetében fogadja el, így az alkalmazás teljesen független fiók írási teszi lehetővé. Például ha az alkalmazás meghívja a [Microsoft Graph](https://graph.microsoft.io), néhány további funkciók és az adatok munkahelyi fiókokat, például a SharePoint-webhelyek vagy a címtáradatok számára elérhető lesz. Azonban számos műveletek végrehajtásához például [a felhasználók e-mailjeinek olvasása](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), ugyanazt a kódot férhet hozzá az e-mailt a személyes és munkahelyi és iskolai fiókok esetében.

A v2.0-végpont egyetlen tár (MSAL) használatával éri el a fogyasztói, oktatási és a vállalati világok.

 Az Azure AD-1.0-s verziójú végpont csak a munkahelyi és iskolai fiókjába történő bejelentkezések fogad el.

## <a name="incremental-and-dynamic-consent"></a>A növekményes és a dinamikus jóváhagyás

Adja meg az OAuth 2.0 szükséges engedélyeiket előre, például az 1.0-s verziójú Azure AD-végpont használatával alkalmazások szükségesek:

![Engedélyek regisztrációs felhasználói felület](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Közvetlenül az alkalmazás regisztrálása a beállított engedélyeket is **statikus**. Az alkalmazás engedélyei statikus definiálva az Azure Portalon és tárolni a kódot már jól néz kis- és egyszerű, előfordulhat, hogy jelenthet olyan problémák fejlesztők számára:

* Az alkalmazás kell tudnia minden eddiginél kellene alkalmazás létrehozáskor engedélyeit. Engedélyek hozzáadása idővel volt bonyolult folyamat.

* Az alkalmazás kell tudni az összes olyan erőforrást, minden eddiginél kelljen elérésére. Alkalmazások létrehozása, amelyek hozzáférnek az erőforrásokhoz tetszőleges számú nehéz volt.

* Az alkalmazás kell kérnie a felhasználó első bejelentkezés után minden eddiginél kellene összes engedélyt. Bizonyos esetekben ez vezetett engedélyek hosszú listáját, amelyek végfelhasználók számára az alkalmazás eléréséhez a kezdeti bejelentkezési jóváhagyása nem ajánlott.

A v2.0-végponttal, figyelmen kívül hagyja a statikusan meghatározott engedélyek, az Azure Portalon, az alkalmazás regisztrációs adatok meghatározott és az engedélyek megadása az alkalmazás igényeinek megfelelően **dinamikusan** futásidőben, a normál használat során a alkalmazás, függetlenül az alkalmazás regisztrációs adatok a statikusan meghatározott engedélyek.

Ehhez adja meg a hatókörök, az alkalmazásnak kell lekérdezhet az alkalmazása időben azzal az új hatókört a `scope` paraméter egy hozzáférési jogkivonata – nem szükséges előre megadhatja ezeket az alkalmazás regisztrációs adatainak kérésekor .

Ha a felhasználó még nem egyezett bele az új hatókörök szerepel a kérelemben, kéri az új engedélyek csak a jóváhagyást. További tudnivalókért olvashat a [engedélyek, beleegyezése és hatókörök](v2-permissions-and-consent.md).

Engedélyek kéréséhez alkalmazás dinamikusan keresztül lehetővé teszi a `scope` paraméter biztosítja a fejlesztők számára teljes hozzáférést a felhasználói felületen keresztül. Ha szeretné, is beállíthatja az előtérbeli betölteni a jóváhagyás környezetet, és kérje meg az összes engedélyt egy kezdeti engedélyezési kérésben. Vagy ha az adott alkalmazáshoz szükséges engedélyek nagy számú, ha szeretné, gyűjtsön ezeket az engedélyeket a felhasználó Növekményesen, módon tudják használni az alkalmazás egyes funkcióinak idővel.

Vegye figyelembe, hogy a szervezet nevében végzett rendszergazdai jóváhagyás továbbra is használja a statikus engedélyeket, ezért ajánlott beállítani ezeket az engedélyeket az alkalmazások a v2.0-végpont használatával, ha egy rendszergazda a teljes nevében beleegyezés van szüksége az alkalmazás regisztrálva szervezet. Ez csökkenti a ciklusokat a szervezet rendszergazdája megköveteli, hogy az alkalmazás beállítása

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

Az 1.0-s verziójú végpont használó alkalmazásokhoz, mint egy alkalmazás működhet a **erőforrás**, vagy egy címzett jogkivonatokat. Erőforrás definiálhat számos **hatókörök** vagy **oAuth2Permissions** , hogy tisztában van azzal, így a hatókörök bizonyos készletének erőforrás-jogkivonat kérése az alkalmazásokat. Az Azure AD Graph API egy adott erőforrás példaként, vegye figyelembe:

* Erőforrás-azonosító, vagy `AppID URI`: `https://graph.windows.net/`

* Hatókörök, vagy `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, és így tovább.

Mindezt igaznak a v2.0-végpont. Egy alkalmazás továbbra is működhet, erőforrásként hatókörök meghatározása, és egy URI segítségével azonosítható. Ügyfélalkalmazások továbbra is kérhetnek ezeket a hozzáférést. Azonban, hogy egy ügyfél igényel-e ezeket az engedélyeket a módszer megváltozott. Az 1.0-s verziójú végpont, az OAuth 2.0-s engedélyezik a kérést az Azure AD előfordulhat, hogy rendelkezik hasonlított:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Ha a **erőforrás** paraméter jelzi, hogy melyik erőforrást az ügyfélalkalmazásnak az engedélyezési kér. Azure ad-ben az alkalmazás az Azure Portalon statikus konfiguráció alapján, és ennek megfelelően kiadott jogkivonatokat szükséges engedélyek számított. Alkalmazások a v2.0-végpont használatával az ugyanazon az OAuth 2.0 engedélyezi kérelem tűnik:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Ha a **hatókör** paraméter azt jelzi, hogy melyik erőforrást, és az engedélyek az alkalmazás hitelesítést kér. A kívánt erőforrást. a kérés továbbra is megtalálhatók –, egyszerűen a az egyes értékei közül a hatókör-paramétert vonatkozik. Ily módon a hatókör-paraméter használatával lehetővé teszi, hogy az OAuth 2.0 ismertetőjének több megfeleljenek a v2.0-végpont, és jobban igazodik az általános iparági eljárásoknak megfelelő beállításában. Lehetővé teszi alkalmazások végrehajtásához [növekményes hozzájárulási](#incremental-and-dynamic-consent), amely korábban leírt.

## <a name="well-known-scopes"></a>Az ismert hatókörök

### <a name="offline-access"></a>Offline elérés

Alkalmazások a v2.0-végpont használatával előfordulhat, hogy egy új jól ismert engedély használata szükséges az alkalmazások – a `offline_access` hatókör. Minden alkalmazás kell kérnie ezt az engedélyt, ha egy hosszabb ideig, még akkor is, ha a felhasználó lehet, hogy nem lehet aktívan használja az alkalmazást a felhasználó nevében lévő erőforrások eléréséhez szükséges. A `offline_access` hatókör fog megjelenni a felhasználó hozzájárulási párbeszédpanelek, mint a **az adatok elérése bármikor**, amelyhez a felhasználónak el kell fogadnia. Kér a `offline_access` engedély lehetővé teszi a webes alkalmazás OAuth 2.0 refresh_tokens fogadjon a v2.0-végpontra. Frissítési jogkivonatok hosszú élettartamú, és új OAuth 2.0 hozzáférési jogkivonatok hozzáférés hosszabb ideig kicserélhetők.

Ha az alkalmazás nem kér a `offline_access` hatókörét, a frissítési biztonsági jogkivonat nem kap. Ez azt jelenti, hogy az OAuth 2.0 hitelesítési kódfolyamat a hozzáférési kód beváltásához csak kap vissza a hozzáférési jogkivonatot a `/token` végpont. A hozzáférési jogkivonatot rövid időn (általában egy órán keresztül) érvényes marad, de végül le fog járni. Ebben az időpontban, az alkalmazás átirányítja a felhasználót kell biztonsági másolatot a `/authorize` végpont egy új hozzáférési kód lekéréséhez. Az átirányítás során a felhasználó is, vagy előfordulhat, hogy nem kell újra adja meg a hitelesítő adataikat, vagy újra járul hozzá az engedélyeket, az alkalmazás típusától függően.

További információ az OAuth 2.0, `refresh_tokens`, és `access_tokens`, tekintse meg a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, a profil és az e-mailben

Hagyományosan a legalapvetőbb OpenID Connect bejelentkezést folyamat az Azure AD biztosítja a információ a felhasználó a létrejövő rengeteg *id_token*. A jogcímeket egy *id_token* is tartalmazhat, a felhasználó nevét, előnyben részesített felhasználónév, e-mail címét, objektumazonosító: és több.

Az adatokat, amelyek a `openid` hatókör számára biztosítja az alkalmazás való hozzáférés jelenleg korlátozott. A `openid` hatókör csak akkor engedélyezi az alkalmazás jelentkezzen be a felhasználó és a felhasználó alkalmazás-specifikus azonosítót kap. Kívánja beszerezni az alkalmazásban a felhasználó személyes adatait, ha az alkalmazás a felhasználó további engedélyek kéréséhez kell. Két új hatókört – a `email` és `profile` hatókörök – lehetővé teszi, hogy további engedélyek kéréséhez.

A `email` hatókör lehetővé teszi, hogy a felhasználó elsődleges e-mail címét, az alkalmazás hozzáférjen a `email` a id_token a jogcím. A `profile` hatókör számára biztosítja az alkalmazás hozzáférjen a felhasználó számára – a név, elsődleges felhasználónév, minden egyéb alapvető adatait a objektumazonosító, és így tovább.

Ez lehetővé teszi, hogy az alkalmazás minimális nyilvánosságra módon code – a felhasználó csak a fontos, hogy az alkalmazás ehhez szükséges információkat kérhet. Ezeken a hatókörökön további információkért lásd: [a 2.0-s verziójú hatókör-hivatkozást](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Jogkivonat

A v2.0-végpont által kiállított jogkivonatokban lévő jogcímek nem lehet az általánosan elérhető által kiállított jogkivonatokban azonos az Azure AD-végpontok. Alkalmazások migrálása az új szolgáltatást nem azt feltételezik, egy adott jogcím id_tokens vagy access_tokens jelen. További részleteket a v2.0-végpont használt tokenek különböző típusú a [hozzáférési jogkivonat](access-tokens.md) referencia és [ `id_token` referencia](id-tokens.md)

## <a name="limitations"></a>Korlátozások

Érdemes figyelembe vennie v2.0 használatakor néhány korlátozások vonatkoznak.

Alkalmazások, amelyek integrálhatók a Microsoft identity platform buildelésekor kell eldöntheti, megfelel-e a 2.0-s végpont és a hitelesítési protokollok az igényeinek. Az 1.0-s verziójú végpont és a platform továbbra is teljes mértékben támogatott, és bizonyos szempontokból további szolgáltatás gazdag, mint a 2.0-s verzió. Azonban v2.0 [vezet be, jelentős előnyöket](azure-ad-endpoint-comparison.md) fejlesztők számára.

Íme egy egyszerűsített javaslatot a fejlesztők ezen a ponton az időben:

* Ha az alkalmazás támogatnia kell a személyes Microsoft-fiókok, használja a 2.0-s verzió. De előtt, fontos tisztában legyen azzal, hogy az ebben a cikkben tárgyalt korlátozások.

* Ha az alkalmazás csak a Microsoft munkahelyi és iskolai fiókok van szüksége, ne használja a 2.0-s verzió. Helyette tekintse meg a [1.0-s verziójú útmutató](azure-ad-developers-guide.md).

A v2.0-végpont az itt felsorolt korlátozásokkal kiküszöböléséhez, így minden eddiginél csak kell használni a v2.0-végpont fejlődik. Addig is ez a cikk segítségével ellenőrizze, hogy a v2.0-végpont az Ön számára megfelelő. Ez a cikk a v2.0-végpont aktuális állapotát tükröző update továbbra is. Ellenőrizze, hogy vissza kiértékeli a v2.0-képességek követelményei.

### <a name="restrictions-on-app-types"></a>Alkalmazástípusok korlátozásai

A következő típusú alkalmazások jelenleg nem támogatottak a v2.0-végpont által. Támogatott alkalmazástípusok ismertetését lásd: [alkalmazástípusok az v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>Önálló webes API-k

Használhatja a v2.0-végpont [hozhat létre a webes API-hoz biztosított az OAuth 2.0-val](v2-app-types.md#web-apis). Azonban, hogy a webes API képes jogkivonatokat fogadni csak egy alkalmazásból, amely rendelkezik az ugyanazon alkalmazás azonosítóját. Nem lehet hozzáférni egy webes API-ügyfélről, amely rendelkezik egy másik alkalmazás azonosítóját. Az ügyfél nem fog tudni kérje vagy szerezze meg a webes API-hoz való engedélyek.

Hogyan hozhat létre egy webes API-t, amely ugyanazon Alkalmazásazonosítóval rendelkező ügyfél származó jogkivonatokat, olvassa el a 2.0-s verziójú végpont webes API-t minták a [bevezetés v2.0](v2-overview.md#getting-started) szakaszban.

### <a name="restrictions-on-app-registrations"></a>Alkalmazásregisztrációk korlátozásai

Jelenleg minden alkalmazáshoz, amely szeretné integrálni a v2.0-végponttal, létre kell hoznia egy alkalmazásregisztráció az új [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Meglévő Azure ad-ben vagy a Microsoft-fiók alkalmazások nem kompatibilisek a v2.0-végpontra. Minden olyan portálon kívül az alkalmazásregisztrációs portálon regisztrált alkalmazások nem kompatibilisek a v2.0-végpontra.

Ezenkívül a létrehozott alkalmazásregisztrációk a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alábbi korlátozásokkal rendelkeznek:

* Az Alkalmazáskulcs csak két használható alkalmazás azonosítóját.

* Egy személyes Microsoft-fiókkal rendelkező felhasználó által regisztrált alkalmazásregisztráció tudja megtekinteni és felügyelt csak egyetlen developer-fiók használatával. Több fejlesztő között nem lehet megosztani. Ha meg szeretné osztani az alkalmazás regisztrációját, többek között a több fejlesztő, létrehozhat az alkalmazás a regisztrációs portál egy olyan Azure AD-fiókkal való bejelentkezés révén.

* Az átirányítási URL-cím engedélyezett formátumnak több korlátozások vonatkoznak. Átirányítási URL-CÍMMEL kapcsolatos további információkért lásd: a következő szakaszban.

### <a name="restrictions-on-redirect-urls"></a>A korlátozások átirányítási URL-címek

Az alkalmazásregisztrációs portálon a regisztrált alkalmazások korlátozott számú átirányítási URL-címértékekre korlátozódnak. Az átirányítási URL-címet a webalkalmazások és szolgáltatások a sémát kell kezdődnie `https`, és az összes átirányítási URL-cím értékét meg kell osztani egyetlen DNS-tartományba. Például nem lehet regisztrálni egy webalkalmazást, amely egy ezek közül tartalmaz átirányítási URL-címek:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő átirányítási URL-címet a DNS-nevével, az átirányítási URL-cím, amely a hozzáadni kívánt teljes DNS-nevét. A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URL-cím teljes DNS-neve nem egyezik a meglévő átirányítási URL-cím DNS-nevét.

* Az új átirányítási URL-cím teljes DNS-neve nincs a meglévő átirányítási URL-cím altartomány.

Például ha az alkalmazás az átirányítási URL-cím:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha szeretné, hogy rendelkezik `login-east.contoso.com` és `login-west.contoso.com` , átirányítási URL-címek, hozzá kell adnia azokat átirányítási URL-ek az itt látható sorrendben:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi két adhat hozzá, mert az első átirányítási URL-cím altartományába tartoznak a contoso.com. Ez a korlátozás egy soron következő kiadásban törlődni fog.

Vegye figyelembe azt is, egy adott alkalmazásban csak 20 válasz URL-címmel rendelkezhet.

Alkalmazás regisztrálása az alkalmazásregisztrációs portálon az kezelésével kapcsolatos információkért lásd: [alkalmazás regisztrálása a v2.0-végponttal](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Függvénytárak és SDK-k korlátozásai

Klienskódtár-támogatásával a v2.0-végpont jelenleg korlátozott. Ha azt szeretné, használhatja a v2.0-végpontra egy éles alkalmazásban, ezen lehetőségek állnak rendelkezésére:

* Ha egy webalkalmazást hoz létre, a bejelentkezéshez és jogkivonat-ellenőrzéshez biztonságosan használhatja a Microsoft mindenki számára elérhető kiszolgálóoldali közbenső szoftver. Ezek közé tartozik az OWIN Open ID Connect közbenső szoftvert, az ASP.NET és a Node.js Passport beépülő modult. Használja a Microsoft közbenső kódmintákért tekintse a [bevezetés v2.0](v2-overview.md#getting-started) szakaszban.

* Ha egy asztali vagy mobil alkalmazást fejleszt, a Microsoft hitelesítési tárak (MSAL) előzetes egyikét használhatja. Ezek a kódtárak vannak egy éles által támogatott előzetes verzióban érhető el, így biztonságosan használhatja őket az éles környezetben. További tudnivalók az előzetes verzió és a rendelkezésre álló kódtárak a feltételek [hitelesítési tárak referencia](reference-v2-libraries.md).

* A platformon nem fedi le a Microsoft-kódtárak integrálható a v2.0-végpont által közvetlenül üzenetek küldése és fogadása protokoll az alkalmazás kódjában. A 2.0-s OpenID Connectet és az OAuth-protokollok [explicit módon vannak dokumentálva](active-directory-v2-protocols.md) ilyen az integráció végrehajtásához.

* Végül nyílt forráskódú ID Connect megnyitásához és az OAuth-kódtárak segítségével integrálhatók az a v2.0-végpontra. A 2.0-s protokoll jelentős módosítások nélkül számos nyílt forráskódú protokoll kódtár kompatibilisnek kell lennie. Az ilyen típusú kódtárak rendelkezésre állásának nyelvtől és platformtól függ. A [Open ID Connect](http://openid.net/connect/) és [OAuth 2.0](http://oauth.net/2/) webhelyek népszerű megvalósításokhoz listának a karbantartására. További információkért lásd: [Azure Active Directory v2.0 és hitelesítési kódtárai](reference-v2-libraries.md), és a nyílt forráskódú ügyféloldali függvénytárak és minták a v2.0-végponttal tesztelt listáját.

* Referenciaként a `.well-known` végpont a közös v2.0-végpont `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Cserélje le `common` az adatok adott lekérni a bérlő a bérlő Azonosítóját.  

### <a name="restrictions-on-protocols"></a>Korlátozásokat protokollokkal

A v2.0-végpont nem támogatja a SAML vagy WS-Federation; csak a támogatott Open ID Connect és az OAuth 2.0. Nem minden funkciók és képességek OAuth protokollok bekerültek a v2.0-végpontra.

A következő protokoll funkciók és képességek jelenleg *nem érhető el* vagy *nem támogatott* a v2.0-végpont:

* A `email` csak jogcímet ad vissza, ha egy nem kötelező jogcím van konfigurálva, és hatókör hatókör = e-mail lett megadva a kérelemben. Azonban várhatóan további szabványoknak való megfelelés az Open ID Connect és OAuth2.0 frissítésekor a v2.0-végpont módosíthatja ezt a viselkedést.

* A v2.0-végpont nem támogatja kiállító szerepkör vagy csoport jogcímek azonosító-jogkivonatokat.

* A v2.0-végpont nem támogatja a [OAuth 2.0 erőforrás tulajdonosának jelszava hitelesítő adatok engedélyezés](https://tools.ietf.org/html/rfc6749#section-4.3).

Jobb megértése érdekében a v2.0-végpont támogatott protokoll funkciók körét, olvassa el a [OpenID Connectet és az OAuth 2.0 protokoll referenciája](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-korlátozások

Ha korábban már használta az Active Directory Authentication Library (ADAL) a Windows-alkalmazások, előfordulhat, hogy elvégezte Windows integrált hitelesítést, amely használja a Security Assertion Markup Language (SAML) helyességi feltétel grant veheti. Az engedélyhez a felhasználók Azure AD összevont bérlők is nélkül csendes hitelesítést a helyszíni Active Directory-példánynak a hitelesítő adatok megadása. Az SAML helyességi feltétel megadása a v2.0-végpont jelenleg nem támogatott.
