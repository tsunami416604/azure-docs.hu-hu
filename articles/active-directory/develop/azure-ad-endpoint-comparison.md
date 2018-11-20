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
ms.date: 11/17/2018
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.openlocfilehash: 3e9765bf2c6b746b892f7fbc97ea3124f80d772e
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976010"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Az Azure AD v2.0-végpont az 1.0-s verziójú végponttal összehasonlítása

Új alkalmazás fejlesztése esetén fontos tudni, hogy az Azure Active Directory (Azure AD) 1.0-s és 2.0-s verziójú végpontok közötti különbségeket. Ez a cikk a végpontok és néhány meglévő korlátozások a v2.0-végpont fő különbségeit ismerteti.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure AD-forgatókönyvek és funkciók. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](#limitations).

## <a name="who-can-sign-in"></a>Ki jelentkezhet be

![Aki bejelentkezhet, 1.0-s és 2.0-s verziójú végpontok](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Az 1.0-s verziójú végpont lehetővé teszi, hogy csak munkahelyi és iskolai fiókjába való bejelentkezéshez az alkalmazást (Azure AD)
* A v2.0-végpont lehetővé teszi a munkahelyi és iskolai fiókok Azure AD-ből és a személyes Microsoft-fiókok (MSA), például hotmail.com, Outlook.com-os és msn.com, hogy jelentkezzen be.
* 1.0-s verziója és a v2.0-végpontokra is fogadja el a bejelentkezések *[vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* egy konfigurált alkalmazások az Azure AD-címtár *[egybérlős](single-and-multi-tenant-apps.md)* vagy *több-bérlős* bérlőspecifikus végpontjára mutató konfigurált alkalmazások (`https://login.microsoftonline.com/{TenantId_or_Name}`).

A v2.0-végpont, amely fogadja el a személyes Microsoft-fiókok és a munkahelyi és iskolai fiókjába történő bejelentkezések alkalmazások vizsgálatát teszi lehetővé. Ezáltal az alkalmazás teljesen független fiók írni. Például, ha az alkalmazás meghívja a [Microsoft Graph](https://graph.microsoft.io), néhány további funkciók és az adatok munkahelyi fiókokat, például a SharePoint-webhelyek vagy a címtáradatok számára elérhető lesz. Azonban számos műveletek végrehajtásához például [a felhasználók e-mailjeinek olvasása](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), ugyanazt a kódot férhet hozzá az e-mailt a személyes és munkahelyi és iskolai fiókok esetében.

A v2.0-végpont használhatja a Microsoft-hitelesítési tár (MSAL) próbál a jeggyel hozzáférést a fogyasztónak, oktatási, és a vállalati világok. Az Azure AD-1.0-s verziójú végpont csak a munkahelyi és iskolai fiókjába történő bejelentkezések fogad el.

## <a name="incremental-and-dynamic-consent"></a>A növekményes és a dinamikus jóváhagyás

Adja meg az OAuth 2.0 szükséges engedélyeiket előre, például az 1.0-s verziójú Azure AD-végpont használatával alkalmazások szükségesek:

![Engedélyek regisztrációs felhasználói felület](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Közvetlenül az alkalmazás regisztrálása a beállított engedélyeket is **statikus**. Az alkalmazás az Azure Portalon megadott statikus engedélyei tartsa a kódot, jó és egyszerű, míg a fejlesztők tükrözze lehetséges problémák:

* Az alkalmazásnak szüksége van, minden eddiginél kellene a felhasználó első bejelentkezés után minden engedélyek kéréséhez. Ez egy hosszú engedélyek listája, amely megelőzi a végfelhasználók számára az alkalmazás eléréséhez a kezdeti bejelentkezési jóváhagyása vezethet.

* Az alkalmazás tudnia kell, minden erőforrást, minden eddiginél kelljen elérésére. Alkalmazások létrehozása, amelyek hozzáférnek az erőforrásokhoz tetszőleges számú nehéz volt.

A v2.0-végponttal figyelmen kívül hagyhatja a statikus definiált engedélyek az alkalmazás regisztrációs információkat az Azure portal és a kérés engedélyek növekményes helyett, ami azt jelenti, hogy kérése engedélyekkel előre operációs csoportját, és idővel több halmoz fel mivel az ügyfél az alkalmazás további funkciókat használ. Ehhez adja meg a hatókörök, bármikor, azzal az új hatókört kell az alkalmazás a `scope` paraméter egy hozzáférési jogkivonata – nem szükséges előre megadhatja ezeket az alkalmazás regisztrációs adatainak kérésekor. Ha a felhasználó még nem még hagyta jóvá az új hatóköröket szerepel a kérelemben, hogy engedélyt adjanak csak az új engedélyek kéri. További tudnivalókért lásd: [engedélyek, beleegyezése és hatókörök](v2-permissions-and-consent.md).

Engedélyek kéréséhez alkalmazás dinamikusan keresztül lehetővé teszi a `scope` paraméter biztosítja a fejlesztők számára teljes hozzáférést a felhasználói felületen keresztül. Akkor is is előtér-terhelést, Ön hozzájárul a környezetet, és kérje meg az összes engedélyt egy kezdeti engedélyezési kérésben. Ha az adott alkalmazáshoz szükséges engedélyek nagy számú, gyűjthet ezeket az engedélyeket az a felhasználó Növekményesen, amikor az alkalmazás bizonyos funkcióinak használata idővel.

Rendszergazdai jóváhagyás kész a szervezet nevében továbbra is a az az alkalmazás regisztrálva, így az alkalmazások ezeket az engedélyeket kell beállítania az alkalmazás regisztrációs portálon, ha egy rendszergazda adja meg a teljes szervezet nevében beleegyezését kell statikus engedélyekkel kell rendelkeznie. Ez csökkenti a szervezet rendszergazdája megköveteli, hogy az alkalmazás beállítása a ciklusok.

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

Az 1.0-s verziójú végpont használó alkalmazásokhoz, mint egy alkalmazás működhet a **erőforrás**, vagy egy címzett jogkivonatokat. Egy erőforrás definiálhat számos **hatókörök** vagy **oAuth2Permissions** , hogy tisztában van azzal, így a jogkivonatokat kérhet ennek az erőforrásnak hatókörök meghatározott készlete alkalmazásokat. Az Azure AD Graph API egy adott erőforrás példaként, vegye figyelembe:

* Erőforrás-azonosító, vagy `AppID URI`: `https://graph.windows.net/`
* Hatókörök, vagy `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, és így tovább.

Ez vonatkozik a v2.0-végpont. Egy alkalmazás továbbra is működhet, erőforrásként hatókörök meghatározása, és egy URI segítségével azonosítható. Ügyfélalkalmazások továbbra is kérhetnek ezeket a hozzáférést. Azonban, hogy egy ügyfél igényel-e ezeket az engedélyeket a módszer megváltozott. 

Az 1.0-s verziójú végpont, az OAuth 2.0-s engedélyezik a kérést az Azure AD előfordulhat, hogy rendelkezik hasonlított:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Itt a **erőforrás** paraméter erőforrás-jelzi az ügyféloldali alkalmazás engedélyt kér. Azure ad-ben az alkalmazás az Azure Portalon statikus konfiguráció alapján, és ennek megfelelően kiadott jogkivonatokat szükséges engedélyek számított. 

Alkalmazások a v2.0-végpont használatával az ugyanazon az OAuth 2.0 engedélyezi kérelem tűnik:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Itt a **hatókör** paraméter azt jelzi, hogy melyik erőforrás és az engedélyek az alkalmazás által kért hitelesítési. A kívánt erőforrást. a kérés továbbra is megtalálhatók –, akkor vonatkozik az egyes értékei közül a hatókör-paramétert. Ily módon a hatókör-paraméter használatával lehetővé teszi, hogy az OAuth 2.0 ismertetőjének több megfeleljenek a v2.0-végpont, és jobban igazodik az általános iparági eljárásoknak megfelelő beállításában. Lehetővé teszi alkalmazások végrehajtásához [növekményes hozzájárulási](#incremental-and-dynamic-consent) – csak az engedélyek kérése, amikor az alkalmazás megköveteli őket, nem pedig meghozni.

## <a name="well-known-scopes"></a>Az ismert hatókörök

### <a name="offline-access"></a>Offline elérés

Alkalmazások a v2.0-végpont használatával előfordulhat, hogy egy új jól ismert engedély használata szükséges az alkalmazások – a `offline_access` hatókör. Minden alkalmazás kell kérnie ezt az engedélyt, ha egy hosszabb ideig, még akkor is, ha a felhasználó lehet, hogy nem lehet aktívan használja az alkalmazást a felhasználó nevében lévő erőforrások eléréséhez szükséges. A `offline_access` hatókör fog megjelenni a felhasználó hozzájárulási párbeszédpanelek, mint a **az adatok elérése bármikor**, amelyhez a felhasználónak el kell fogadnia. Kér a `offline_access` engedély lehetővé teszi a webes alkalmazás OAuth 2.0 refresh_tokens fogadjon a v2.0-végpontra. Frissítési jogkivonatok hosszú élettartamú, és új OAuth 2.0 hozzáférési jogkivonatok hozzáférés hosszabb ideig kicserélhetők.

Ha az alkalmazás nem kéri a `offline_access` hatókörét, a frissítési biztonsági jogkivonat nem kap. Ez azt jelenti, hogy amikor az OAuth 2.0 hitelesítési kódfolyamat a hozzáférési kód beváltásához csak kap vissza a hozzáférési jogkivonatot a `/token` végpont. Amely hozzáférési token marad érvényes rövid időn (általában egy órán keresztül), de végül le fog járni. Ebben az időpontban, az alkalmazás átirányítja a felhasználót kell biztonsági másolatot a `/authorize` végpont egy új hozzáférési kód lekéréséhez. Az átirányítás során a felhasználó is, vagy előfordulhat, hogy nem szükséges írja be újra a hitelesítő adatait és engedélyek alkalmazás típusától függően reconsent.

További információ az OAuth 2.0, `refresh_tokens`, és `access_tokens`, tekintse meg a [v2.0 protokoll referenciái](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, a profil és az e-mailben

Hagyományosan a legalapvetőbb OpenID Connect bejelentkezést folyamat az Azure AD biztosítja a információ a felhasználó a létrejövő rengeteg *id_token*. Id_token jogcímeket a felhasználó neve, előnyben részesített felhasználónév, e-mail címét, objektumazonosító: és további tartalmazhatnak.

Az adatokat, amelyek a `openid` hatókör számára biztosítja az alkalmazás való hozzáférés jelenleg korlátozott. A `openid` hatókör csak akkor engedélyezi az alkalmazás jelentkezzen be a felhasználó és a felhasználó alkalmazás-specifikus azonosítót kap. Ha szeretne kaphat az alkalmazás a felhasználó személyes adatait, az alkalmazás kell további engedélyek kéréséhez a felhasználó elől. Két új hatókört `email` és `profile`, lehetővé teszi, hogy további engedélyek kéréséhez.

* A `email` hatókör lehetővé teszi, hogy a felhasználó elsődleges e-mail címét, az alkalmazás hozzáférjen a `email` a id_token, feltéve, hogy a felhasználó rendelkezik egy címmel rendelkező e-mail-címet a jogcím. 
* A `profile` hatókör számára biztosítja az alkalmazás hozzáférjen a nevük előnyben részesített felhasználónév, például a felhasználó minden egyéb alapvető adatait objektumának azonosítója, és így tovább a id_token.

Ezeken a hatókörökön lehetővé teszik az alkalmazás minimális nyilvánosságra módon code, így a felhasználó csak a fontos információkat, az alkalmazás által igényelt ehhez kérhet. Ezeken a hatókörökön további információkért lásd: [a 2.0-s verziójú hatókör-hivatkozást](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Jogkivonat

A v2.0-végpont a tokenek az egy kisebb jogcímkészletet maradjon kicsi is észleltünk adattartalmakat. alapértelmezés szerint a problémák. Ha alkalmazások és szolgáltatások, amelyek függnek az 1.0-s verziójú jogkivonatot, amely már nem áll rendelkezésre a v2.0 jogkivonat alapértelmezés szerint egy adott jogcím, fontolja meg a [választható jogcímek](active-directory-optional-claims.md) funkció, amellyel a jogcímszabályok tartalmazzák.

## <a name="limitations"></a>Korlátozások

Érdemes figyelembe vennie v2.0 használatakor néhány korlátozások vonatkoznak.

Alkalmazások, amelyek integrálhatók a Microsoft identity platform buildelésekor kell eldöntheti, megfelel-e a 2.0-s végpont és a hitelesítési protokollok az igényeinek. Az 1.0-s verziójú végpont és a platform továbbra is teljes mértékben támogatott, és bizonyos szempontokból további szolgáltatás gazdag, mint a 2.0-s verzió. Azonban v2.0 [vezet be, jelentős előnyöket](azure-ad-endpoint-comparison.md) fejlesztők számára.

Íme egy egyszerűsített javaslatot a fejlesztők ezen a ponton az időben:

* Ha azt szeretné, vagy személyes Microsoft-fiókok támogatják az alkalmazásban kell, vagy új alkalmazások írása, használja a 2.0-s verzió. De előtt, győződjön meg arról, hogy megismerkedett az ebben a cikkben tárgyalt korlátozások.
* Ha áttelepítés vagy frissítése egy alkalmazás, amely SAML támaszkodik, 2.0-s verzió nem használható. Helyette tekintse meg a [1.0-s verziójú útmutató](v1-overview.md).

A v2.0-végpont az itt felsorolt korlátozásokkal kiküszöböléséhez, így minden eddiginél csak kell használni a v2.0-végpont fejlődik. Addig is ez a cikk segítségével ellenőrizze, hogy a v2.0-végpont az Ön számára megfelelő. Ez a cikk a v2.0-végpont aktuális állapotát tükröző update továbbra is. Ellenőrizze, hogy vissza kiértékeli a v2.0-képességek követelményei.

### <a name="restrictions-on-app-registrations"></a>Alkalmazásregisztrációk korlátozásai

Az egyes alkalmazások integrálása a v2.0-végpont kívánt, az alkalmazás regisztrációját a hozhat létre a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Másik lehetőségként regisztrálhat egy alkalmazást a a [ **alkalmazásregisztrációk (előzetes verzió)** élmény](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) az Azure Portalon. Meglévő Microsoft-fiók alkalmazások nem kompatibilisek a betekintő portálon, de minden AAD-alkalmazás, függetlenül attól, hol és mikor azok regisztrálva lett. 

Alkalmazásregisztrációk, amelyek támogatják a munkahelyi és iskolai fiókokhoz és személyes fiókok az alábbi korlátozásokkal rendelkeznek:

* Az Alkalmazáskulcs csak két használható alkalmazás azonosítója.
* Az alkalmazás regisztrációját a regisztrációs portálon alkalmazás személyes Microsoft-fiókkal rendelkező felhasználó által regisztrált tudja megtekinteni és felügyelt csak egyetlen developer-fiók használatával. Több fejlesztő között nem lehet megosztani. Ha meg szeretné osztani az alkalmazás regisztrációját a több fejlesztő, akkor az alkalmazást az Alkalmazásregisztrációk (előzetes verzió) szakaszban az Azure Portal használatával hozhat létre.
* Az átirányítási URL-cím engedélyezett formátumnak több korlátozások vonatkoznak. Átirányítási URL-CÍMMEL kapcsolatos további információkért lásd: a következő szakaszban.

### <a name="restrictions-on-redirect-urls"></a>A korlátozások átirányítási URL-címek

A 2.0-s verziójú regisztrált alkalmazások korlátozott számú átirányítási URL-címértékekre korlátozódnak. Az átirányítási URL-címet a webalkalmazások és szolgáltatások a sémát kell kezdődnie `https`, és az összes átirányítási URL-cím értékét meg kell osztani egyetlen DNS-tartományba.  A regisztrációs rendszer összehasonlítja a meglévő átirányítási URL-címet a DNS-nevét az átirányítási URL-címet, adja hozzá a teljes DNS-nevét. `http://localhost` átirányítási URL-ként is támogatott.  

A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URL-cím teljes DNS-neve nem egyezik a meglévő átirányítási URL-cím DNS-nevét.
* Az új átirányítási URL-cím teljes DNS-neve nem a meglévő átirányítási URL-cím altartomány.

#### <a name="example-1"></a>1. példa

Ha az alkalmazás átirányítási URL-Címének `https://login.contoso.com`, adhat hozzá egy átirányítási URL-címet, ahol a DNS-név pontosan egyezik, az alábbi példában látható módon:

`https://login.contoso.com/new`

Vagy tekintse meg a DNS-altartományára, az alábbi példában látható módon:

`https://new.login.contoso.com`

#### <a name="example-2"></a>2. példa

Ha szeretné, hogy rendelkezik `login-east.contoso.com` és `login-west.contoso.com` , átirányítási URL-címek, hozzá kell adnia azokat átirányítási URL-EK, a következő sorrendben:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi két adhat hozzá, mert az első átirányítási URL-cím altartományába tartoznak a contoso.com. Ez a korlátozás egy soron következő kiadásban törlődni fog.

Vegye figyelembe azt is, használhat egy adott alkalmazásban csak 20 válasz URL-címek – Ez a korlátozás vonatkozik különböző típusú alkalmazás, hogy a regisztráció támogatja-e (SPA, natív ügyfél, webes alkalmazás és szolgáltatás).  

Ismerje meg, hogyan használható az alkalmazás regisztrálása az v2.0, tekintse meg az alábbi gyorsútmutatókkal:

* [Az alkalmazásregisztrációs portálon használó alkalmazás regisztrálása](quickstart-v2-register-an-app.md)
* [Az alkalmazás felhasználói felülete regisztrációk (előzetes verzió) használatával alkalmazás regisztrálása](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>Függvénytárak és SDK-k korlátozásai

Klienskódtár-támogatásával a v2.0-végpont jelenleg korlátozott. Ha azt szeretné, használhatja a v2.0-végpontra egy éles alkalmazásban, ezen lehetőségek állnak rendelkezésére:

* Egy webalkalmazás létrehozásakor, biztonságosan használható az általánosan elérhető a kiszolgálóoldali közbenső szoftvert a bejelentkezéshez és jogkivonat-ellenőrzéshez. Ezek közé tartozik az OWIN OpenID Connect közbenső szoftvert, az ASP.NET és a Node.js Passport beépülő modult. Használja a Microsoft közbenső kódmintákért tekintse a [bevezetés v2.0](v2-overview.md#getting-started) szakaszban.
* Ha egy asztali vagy mobil alkalmazást fejleszt, a Microsoft hitelesítési tárak (MSAL) előzetes egyikét használhatja. Ezek a kódtárak vannak egy éles által támogatott előzetes verzióban érhető el, így biztonságosan használhatja őket az éles környezetben. További tudnivalók az előzetes verzió és a rendelkezésre álló kódtárak a feltételek [hitelesítési tárak referencia](reference-v2-libraries.md).
* A platformon nem fedi le a Microsoft-kódtárak integrálható a v2.0-végpont által közvetlenül üzenetek küldése és fogadása protokoll az alkalmazás kódjában. A 2.0-s OpenID Connectet és az OAuth-protokollok [explicit módon vannak dokumentálva](active-directory-v2-protocols.md) ilyen az integráció végrehajtásához.
* Végül nyílt forráskódú OpenID Connectet és az OAuth-kódtárak segítségével integrálhatók az a v2.0-végpontra. A v2.0-végpont módosítás nélkül számos nyílt forráskódú protokoll kódtár kompatibilisnek kell lennie. Az ilyen típusú kódtárak rendelkezésre állásának nyelvtől és platformtól függ. A [OpenID Connect](http://openid.net/connect/) és [OAuth 2.0](http://oauth.net/2/) webhelyek népszerű megvalósításokhoz listának a karbantartására. További információkért lásd: [Azure Active Directory v2.0 és hitelesítési kódtárai](reference-v2-libraries.md), és a nyílt forráskódú ügyféloldali függvénytárak és minták a v2.0-végponttal tesztelt listáját.
* Referenciaként a `.well-known` végpont a közös v2.0-végpont `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Cserélje le `common` az adatok adott lekérni a bérlő a bérlő Azonosítóját.  

### <a name="protocol-changes"></a>Protokoll módosítások

A v2.0-végpont nem támogatja a SAML vagy WS-Federation; OpenID Connect és az OAuth 2.0 csak támogatja.  Az 1.0-s verziójú végpontról az OAuth 2.0 protokollt a jelentős változtatások a következők: 

* A `email` jogcímet ad vissza, ha egy nem kötelező jogcím van konfigurálva **vagy** hatókör = e-mail lett megadva a kérelemben. 
* A `scope` paraméter most már támogatott helyére a `resource` paraméter.  
* Számos válaszok módosítva lett, így jobban megfelel az OAuth 2.0 ismertetőjének, például az megfelelően visszaadó `expires_in` , egy int karakterlánc helyett.  

Jobb megértése érdekében a v2.0-végpont támogatott protokoll funkciók körét, lásd: [OpenID Connectet és az OAuth 2.0 protokoll referenciája](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-korlátozások

Ha korábban már használta az Active Directory Authentication Library (ADAL) a Windows-alkalmazások, előfordulhat, hogy elvégezte Windows integrált hitelesítést, amely használja a Security Assertion Markup Language (SAML) helyességi feltétel grant veheti. Az engedélyhez a felhasználók Azure AD összevont bérlők is nélkül csendes hitelesítést a helyszíni Active Directory-példánynak a hitelesítő adatok megadása. Az SAML helyességi feltétel megadása nem támogatott a v2.0-végpont.
