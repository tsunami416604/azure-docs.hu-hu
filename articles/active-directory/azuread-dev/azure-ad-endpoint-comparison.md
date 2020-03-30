---
title: Miért frissítsen a Microsoft identity platformra (2.0-s verzió) | Azure
description: Ismerje meg a Microsoft identity platform (2.0) végpont és az Azure Active Directory (Azure AD) 1.0-s verzióbeli végpont közötti különbségeket, és ismerje meg a 2.0-s verzióra való frissítés előnyeit.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154916"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Miért érdemes Microsoft Identitásplatformra (a 2.0-s verzióra) frissíteni?

Új alkalmazás fejlesztése során fontos tudni, hogy a microsoftidentity platform (2.0) és az Azure Active Directory (1.0-s verzió) végpontok közötti különbségeket. Ez a cikk ismerteti a végpontok közötti főbb különbségeket és a Microsoft identity platform néhány meglévő korlátozását.

> [!NOTE]
> A Microsoft identity platform végpont jatttal nem támogatja az összes Azure AD-forgatókönyvek és -szolgáltatások. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](#limitations)

## <a name="who-can-sign-in"></a>Ki jelentkezhet be?

![Ki tud bejelentkezni az 1.0-s és a 2.0-s végponttal?](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* A v1.0-s végpont lehetővé teszi, hogy csak a munkahelyi és iskolai fiókok jelentkezzen be az alkalmazásba (Azure AD)
* A Microsoft identity platform végpont lehetővé teszi az Azure AD-ből és a személyes Microsoft-fiókokból (MSA), például hotmail.com, outlook.com és msn.com, a munkahelyi és iskolai fiókok bejelentkezését.
* Mindkét végpont elfogadja az *[Egybérlősként](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* konfigurált alkalmazások vagy a bérlőspecifikus végpontra (`https://login.microsoftonline.com/{TenantId_or_Name}`való pontra való pontra beállított *több-bérlős* alkalmazások számára az Azure AD-címtár *[vendégfelhasználóinak](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* bejelentkezését is.

A Microsoft identity platform végpont lehetővé teszi, hogy olyan alkalmazásokat írjon, amelyek személyes Microsoft-fiókokból, valamint munkahelyi és iskolai fiókokból fogadnak bejelentkezéseket. Ez lehetővé teszi, hogy írjon az alkalmazás teljesen fiók-agnosztikus. Ha például az alkalmazás meghívja a [Microsoft Graph programot,](https://graph.microsoft.io)néhány további funkció és adat is elérhető lesz a munkahelyi fiókokhoz, például a SharePoint-webhelyekhez vagy a címtáradatokhoz. De sok művelet, mint például [a Reading a felhasználó e-mail](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0), ugyanazt a kódot érheti el az e-mail mind a személyes és a munkahelyi és iskolai számlák.

A Microsoft identity platform végpontja, használhatja a Microsoft Authentication Library (MSAL) a fogyasztói, oktatási és vállalati világok eléréséhez. Az Azure AD v1.0-s végpont csak munkahelyi és iskolai fiókokból fogadja el a bejelentkezéseket.

## <a name="incremental-and-dynamic-consent"></a>Növekményes és dinamikus hozzájárulás

Az Azure AD v1.0-s végpontját használó alkalmazásoknak előzetesen meg kell adniuk a szükséges OAuth 2.0-s engedélyeket, például:

![Példa az Engedélyek regisztrációs felhasználói felületére](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

A közvetlenül az alkalmazás **static**regisztrációján beállított engedélyek statikusak. Bár az Azure Portalon definiált alkalmazás statikus engedélyei a kódot szépen és egyszerűen tartják, a fejlesztők számára néhány lehetséges problémát jelent:

* Az alkalmazásnak minden olyan engedélyt meg kell kérnie, amelyre a felhasználó első bejelentkezése kor szüksége lenne. Ez az engedélyek hosszú listájához vezethet, amely megakadályozza a végfelhasználókat az alkalmazás hozzáférésének jóváhagyásától a kezdeti bejelentkezéskor.

* Az alkalmazásnak tudnia kell az összes erőforrást, amelyhez valaha is hozzáférhet. Nehéz volt olyan alkalmazásokat létrehozni, amelyek tetszőleges számú erőforráshoz férhetnek hozzá.

A Microsoft identity platform végpont, figyelmen kívül hagyhatja az alkalmazás regisztrációs adatait az Azure Portalon meghatározott statikus engedélyeket, és ehelyett növekményesen kérhet engedélyeket, ami azt jelenti, hogy a legminimálisabb engedélykészletet kell kérnie előre és idővel egyre nagyobb lesz, mivel az ügyfél további alkalmazásfunkciókat használ. Ehhez bármikor megadhatja az alkalmazás igényeinek hatóköreit úgy, hogy az `scope` új hatóköröket a paraméterbe is beírja egy hozzáférési jogkivonat kérésekor – anélkül, hogy előre meg kellene határoznia azokat az alkalmazás regisztrációs adataiközött. Ha a felhasználó még nem járult hozzá a kérelemhez hozzáadott új hatókörökhöz, a rendszer csak az új engedélyekhez való hozzájárulásra kéri őket. További információ: [engedélyek, jóváhagyások és hatókörök.](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

Ha lehetővé teszi, hogy egy `scope` alkalmazás dinamikusan kérjen engedélyeket a paraméteren keresztül, a fejlesztők teljes mértékben szabályozhatják a felhasználói élményt. A jóváhagyási élmény frontra is betölthető, és egy kezdeti engedélyezési kérelemben kérheti az összes engedélyt. Ha az alkalmazásnak nagy számú engedélyre van szüksége, ezeket az engedélyeket fokozatosan gyűjtheti össze a felhasználótól, amikor az alkalmazás bizonyos funkcióit idővel megpróbálják használni.

A szervezet nevében végzett rendszergazdai hozzájáruláshoz továbbra is szükség van az alkalmazáshoz regisztrált statikus engedélyekre, ezért be kell állítania ezeket az engedélyeket az alkalmazásokhoz az alkalmazásregisztrációs portálon, ha szüksége van egy rendszergazdára, hogy a teljes szervezet nevében beleegyezést adjon. Ez csökkenti a szervezet rendszergazdája által az alkalmazás beállításához szükséges ciklusokat.

## <a name="scopes-not-resources"></a>Hatókörök, nem erőforrások

A v1.0-s végpontot használó alkalmazások esetében az alkalmazások **erőforrásként**vagy jogkivonatok címzettjeként is viselkedhetnek. Egy erőforrás számos **hatókört** vagy **oAuth2Engedélyeket** határozhat meg, amelyeket megért, lehetővé téve az ügyfélalkalmazások számára, hogy jogkivonatokat kérjenek az adott erőforrásból a hatókörök bizonyos készletéhez. Tekintsük a Microsoft Graph API-t egy erőforrás példájaként:

* Erőforrás-azonosító, `AppID URI`vagy:`https://graph.microsoft.com/`
* Hatókörök, `oAuth2Permissions`vagy `Directory.Read` `Directory.Write`: , , és így tovább.

Ez igaz a Microsoft identity platform végpont. Egy alkalmazás továbbra is erőforrásként viselkedhet, hatóköröket definiálhat, és uri-val azonosíthatja őket. Az ügyfélalkalmazások továbbra is kérhetnek hozzáférést ezekhez a hatókörökhöz. Azonban az, ahogyan az ügyfél kéri ezeket az engedélyeket megváltozott.

A v1.0-s végpont esetében az OAuth 2.0-s engedélyezési kérelem az Azure AD-nek a következőkre nézhet:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Itt az **erőforrás-paraméter** jelezte, hogy az ügyfélalkalmazás melyik erőforrást kéri az engedélyezéshez. Az Azure AD kiszámította az alkalmazás által megkövetelt engedélyeket az Azure Portal statikus konfigurációja alapján, és ennek megfelelően kiadta a jogkivonatokat.

A Microsoft identity platform végpontját használó alkalmazások esetében ugyanaz az OAuth 2.0-s engedélyezési kérelem a következőképpen néz ki:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Itt a **hatókör** paraméter azt jelzi, hogy az alkalmazás melyik erőforrást és engedélyeket kéri az engedélyezéshez. A kívánt erőforrás továbbra is jelen van a kérelemben – a hatókör paraméter minden értékében szerepel. A hatókör paraméter ily módon lehetővé teszi, hogy a Microsoft identity platform végpont jobban megfelel az OAuth 2.0 specifikáció, és jobban igazodik a közös iparági gyakorlat. Azt is lehetővé teszi, hogy az alkalmazások [növekményes beleegyezést](#incremental-and-dynamic-consent) tegyenek – csak akkor kérnek engedélyeket, ha az alkalmazás megköveteli őket, nem pedig előre.

## <a name="well-known-scopes"></a>Jól ismert hatókörök

### <a name="offline-access"></a>Kapcsolat nélküli hozzáférés

A Microsoft identity platform végpontot használó alkalmazások új, jól ismert engedélyek `offline_access` használatát igényelhetik az alkalmazásokhoz – a hatókörhöz. Minden alkalmazásnak kérnie kell ezt az engedélyt, ha hosszabb ideig kell hozzáférnie a felhasználó nevében lévő erőforrásokhoz, még akkor is, ha a felhasználó nem használja aktívan az alkalmazást. A `offline_access` hatókör a jóváhagyási párbeszédekben a felhasználó számára **bármikor access your data**néven jelenik meg, amelybe a felhasználónak el kell fogadnia. Az engedély `offline_access` kérése lehetővé teszi, hogy a webalkalmazás oauth 2.0 refresh_tokens kapjon a Microsoft identitásplatform-végpontról. A frissítési jogkivonatok hosszú élettartamúak, és hosszabb ideig cserélhetők az új OAuth 2.0 hozzáférési jogkivonatokra.

Ha az alkalmazás nem `offline_access` kéri a hatókört, nem kap frissítési jogkivonatokat. Ez azt jelenti, hogy amikor bevált egy engedélyezési kódot az OAuth 2.0 engedélyezési `/token` kód folyamatában, csak egy hozzáférési jogkivonatot kap vissza a végpontról. Ez a hozzáférési jogkivonat rövid ideig (általában egy óráig) érvényes marad, de végül lejár. Ekkor az alkalmazásnak vissza kell irányítania a felhasználót `/authorize` a végpontra az új engedélyezési kód lekéréséhez. Az átirányítás során előfordulhat, hogy a felhasználónak az alkalmazás típusától függően újra meg kell adnia a hitelesítő adatait, vagy újra meg kell adnia az engedélyeket.

Ha többet szeretne megtudni az OAuth 2.0-ról, `refresh_tokens`és `access_tokens`tekintse meg a Microsoft [identity platform protokollhivatkozását.](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="openid-profile-and-email"></a>OpenID, profil és e-mail

A Microsoft identity platformmal való legalapvetőbb OpenID Connect bejelentkezési folyamat korábban sok információt szolgáltatna a felhasználóról az eredményül kapott *id_token.* A id_token a jogcímek tartalmazhatják a felhasználó nevét, elsődleges felhasználónevét, e-mail címét, objektumazonosítóját stb.

Az információ, `openid` amelyhez a hatókör hozzáférést biztosít az alkalmazásszámára, mostantól korlátozott. A `openid` hatókör csak azt teszi lehetővé, hogy az alkalmazás bejelentkezzen a felhasználóba, és megkapja a felhasználó alkalmazásspecifikus azonosítóját. Ha személyes adatokat szeretne kapni a felhasználóról az alkalmazásban, az alkalmazásnak további engedélyeket kell kérnie a felhasználótól. Két új `email` hatókör, `profile`és lehetővé teszi, hogy további engedélyeket kérjen.

* A `email` hatókör lehetővé teszi az alkalmazás számára a `email` hozzáférést a felhasználó elsődleges e-mail címéhez a id_token a jogcímen keresztül, feltéve, hogy a felhasználó rendelkezik címezhető e-mail címmel.
* A `profile` hatókör hozzáférést biztosít az alkalmazás számára a felhasználóval kapcsolatos összes többi alapvető információhoz, például a felhasználó nevéhez, az előnyben részesített felhasználónévhez, az objektumazonosítóhoz és így tovább, a id_token.

Ezek a hatókörök lehetővé teszik az alkalmazás minimális közzétételi módon történő kódolását, így csak az okat az információkat kérheti a felhasználótól, amelyre az alkalmazásnak szüksége van a feladatához. Ezekről a hatókörökről további információt [a Microsoft identity platform hatókörének hivatkozási útmutatójában talál.](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="token-claims"></a>Token jogcímek

A Microsoft identity platform végpontja alapértelmezés szerint kisebb jogcímeket ad ki a jogkivonatokban, hogy a hasznos adatkis maradjon. Ha olyan alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyek egy 1.0-s jogkivonat ban egy adott jogcímtől függenek, amely alapértelmezés szerint nem érhető el egy Microsoft-identitásplatform-jogkivonatban, fontolja meg a [választható jogcímek](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) szolgáltatás használatát a jogcím felvételéhez.

> [!IMPORTANT]
> az 1.0-s és a 2.0-s kulcsokat a v1.0 és a v2.0 végpontok is kiadhatják! id_tokens *mindig* egyezik a végpont, ahonnan kérték, és a hozzáférési jogkivonatok *mindig* megfelelnek a webes API által várt formátumnak, amelyet az ügyfél az adott jogkivonat használatával fog hívni.  Így ha az alkalmazás a v2.0-végpontot használja a Microsoft Graph hívásához, amely a 1.0-s formátumú hozzáférési jogkivonatokat várja, az alkalmazás egy jogkivonatot kap a v1.0 formátumban.  

## <a name="limitations"></a>Korlátozások

A Microsoft identity platform használatakor néhány korlátozást figyelembe kell venni.

A Microsoft identity platformmal integrálható alkalmazások létrehozásakor el kell döntenie, hogy a Microsoft identity platform végpontja és a hitelesítési protokollok megfelelnek-e az igényeinek. A v1.0-s végpont és a platform továbbra is teljes mértékben támogatott, és bizonyos tekintetben több funkciógazdag, mint a Microsoft identitás platform. A Microsoft identity platform azonban [jelentős előnyöket biztosít](azure-ad-endpoint-comparison.md) a fejlesztők számára.

Az alábbiakban egy egyszerűsített javaslat a fejlesztők számára:

* Ha személyes Microsoft-fiókokat szeretne vagy szeretne támogatni az alkalmazásban, vagy új alkalmazást ír, használja a Microsoft identity platformot. De mielőtt megtenné, győződjön meg róla, hogy megértette a cikkben tárgyalt korlátozásokat.
* Ha saml-re támaszkodó alkalmazást telepít át vagy frissít, nem használhatja a Microsoft identity platformot. Ehelyett tekintse meg az [Azure AD 1.0-s megfelelő útmutatót.](v1-overview.md)

A Microsoft identity platform végpont jatttot, hogy megszüntesse az itt felsorolt korlátozásokat, így csak akkor kell használni a Microsoft identity platform végpont. Addig is használja ezt a cikket annak megállapítására, hogy a Microsoft identity platform végpont ja- d. Továbbra is frissítjük ezt a cikket, hogy tükrözze a Microsoft identity platform végpontjának aktuális állapotát. Látogasson vissza, hogy újraértékelje a követelményeket a Microsoft identity platform képességeivel szemben.

### <a name="restrictions-on-app-registrations"></a>Az alkalmazásregisztrációk korlátozása

Minden olyan alkalmazás, amely integrálni szeretne a Microsoft identity platform végpont, létrehozhat egy alkalmazás regisztrációaz új [ **alkalmazásregisztrációs** élményt](https://aka.ms/appregistrations) az Azure Portalon. A meglévő Microsoft-fiókalkalmazások nem kompatibilisek a portállal, de az összes Azure AD-alkalmazás, függetlenül attól, hogy hol és mikor regisztrálták őket.

A munkahelyi és iskolai fiókokat támogató alkalmazásregisztrációk, valamint a személyes fiókok a következő kikötésekkel rendelkeznek:

* Alkalmazásazonosítónként csak két alkalmazástitok engedélyezett.
* Egy olyan alkalmazás, amely nem regisztrált a bérlőben, csak a regisztrált fiókkal kezelhető. Nem osztható meg más fejlesztőkkel. Ez a helyzet a legtöbb olyan alkalmazás esetében, amelyet személyes Microsoft-fiókkal regisztráltak az Alkalmazásregisztrációs portálon. Ha több fejlesztővel szeretné megosztani az alkalmazásregisztrációt, regisztrálja az alkalmazást egy bérlőben az Azure Portal új **alkalmazásregisztrációk** szakaszával.
* Az átirányítási URL formátuma számos engedélyezett korlátozást. Az URL-cím átirányításáról a következő szakaszban talál további információt.

### <a name="restrictions-on-redirect-urls"></a>Az átirányítási URL-címekre vonatkozó korlátozások

A Microsoft identity platformra regisztrált alkalmazások csak korlátozott számú átirányítási URL-értékre vannak korlátozva. A webalkalmazások és -szolgáltatások átirányítási URL-címének a sémával `https`kell kezdődnie, és minden átirányítási URL-értéknek egyetlen DNS-tartományon kell megosztania.  A regisztrációs rendszer összehasonlítja a meglévő átirányítási URL teljes DNS-nevét a hozzáadni kívánt átirányítási URL DNS-nevével. `http://localhost`átirányítási URL-címként is támogatott.  

A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URL teljes DNS-neve nem egyezik meg a meglévő átirányítási URL DNS-nevével.
* Az új átirányítási URL teljes DNS-neve nem a meglévő átirányítási URL altartománya.

#### <a name="example-1"></a>1. példa

Ha az alkalmazás rendelkezik `https://login.contoso.com`a átirányítási URL-címmel, hozzáadhat egy átirányítási URL-címet, ahol a DNS-név pontosan megegyezik, ahogy az a következő példában látható:

`https://login.contoso.com/new`

Vagy hivatkozhat a login.contoso.com DNS-altartományára, ahogy az a következő példában látható:

`https://new.login.contoso.com`

#### <a name="example-2"></a>2. példa

Ha olyan alkalmazást szeretne, `login-east.contoso.com` amely `login-west.contoso.com` rendelkezik és átirányítási URL-címként rendelkezik, a következő sorrendben kell hozzáadnia ezeket az átirányítási URL-címeket:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi kettőt hozzáadhatja, mert ők az első átirányítási URL altartományai, contoso.com.

Egy adott alkalmazáshoz csak 20 válasz URL-cím lehet – ez a korlát a regisztráció által támogatott összes alkalmazástípusra (egyoldalas alkalmazásra (SPA), natív ügyfélre, webalkalmazásra és szolgáltatásra vonatkozik).  

Ha meg szeretné tudni, hogyan regisztrálhat egy alkalmazást a Microsoft identity platformmal való használatra, olvassa el [az Alkalmazás regisztrálása az új alkalmazásregisztrációk szolgáltatással című témakört.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="restrictions-on-libraries-and-sdks"></a>Könyvtárakra és SDK-kra vonatkozó korlátozások

Jelenleg a Microsoft identity platform végpont könyvtártámogatása korlátozott. Ha a Microsoft identity platform végpontját egy éles alkalmazásban szeretné használni, a következő lehetőségek közül választhat:

* Ha egy webalkalmazást hoz fel, biztonságosan használhatja az általánosan elérhető kiszolgálóoldali köztes szoftvert a bejelentkezéshez és a jogkivonat-érvényesítéshez. Ezek közé tartozik az OWIN OpenID Connect köztes ASP.NET és a Node.js Passport beépülő modul. A Microsoft middleware-t használó kódmintákat a [Microsoft identity platform első lépések](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) című szakaszában.
* Ha asztali számítógépet vagy mobilalkalmazást hoz unk, használhatjuk a Microsoft hitelesítési kódtárak (MSAL) egyikét. Ezek a tárak általában elérhetők, vagy éles környezetben támogatott előzetes verzióban érhetők el, így biztonságosan használhatók éles alkalmazásokban. Az előzetes verzió feltételeiről és a rendelkezésre álló tárakról a [hitelesítési könyvtárakhivatkozásban olvashat bővebben.](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* A Microsoft-kódtárak ban nem szereplő platformok esetében a Microsoft identity platform végpontjával integrálható, ha közvetlenül küld és fogad protokollüzeneteket az alkalmazáskódban. Az OpenID Connect és az OAuth protokollok [kifejezetten dokumentálva vannak](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) az ilyen integráció érdekében.
* Végül a nyílt forráskódú OpenID Connect és Az OAuth kódtárak segítségével integrálható a Microsoft identity platform végpontjával. A Microsoft identity platform végpontjának módosítás nélkül kompatibilisnek kell lennie számos nyílt forráskódú protokolltárral. Az ilyen típusú könyvtárak elérhetősége nyelvtől és platformtól függően változik. Az [OpenID Connect](https://openid.net/connect/) és [az OAuth 2.0](https://oauth.net/2/) weboldalak listát vezetnek a népszerű implementációkról. További információ: [Microsoft identity platform és authentication libraries](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json), valamint a microsoftidentity platform végpontjával tesztelt nyílt forráskódú ügyfélkódtárak és minták listája.
* A microsoftos `.well-known` identitásplatform közös végpontjának végpontja. `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` Cserélje `common` le a bérlőazonosítót, hogy a bérlőre vonatkozó adatokat kapjon.  

### <a name="protocol-changes"></a>Protokollváltozások

A Microsoft identity platform végpontja nem támogatja az SAML vagy a WS-Federation; ez egyetlen támogat Nyit Összeköt és OAuth 2.0.  Az OAuth 2.0 protokollok 1.0-s végpontjának jelentős módosításai a következők: 

* A `email` jogcím akkor kerül visszaadásra, ha egy választható jogcím van **konfigurálva, vagy** scope=email van megadva a kérelemben. 
* A `scope` paraméter most már támogatott `resource` a paraméter helyett.  
* Számos válasz módosult, hogy azok jobban megfeleljenek az OAuth 2.0 `expires_in` specifikációnak, például helyesen adják vissza int-ként a karakterlánc helyett.  

A Microsoft identity platform végpontjában támogatott protokollfunkciók hatókörének jobb megértéséhez olvassa el az [OpenID Connect és az OAuth 2.0 protokollhivatkozás című témakört.](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

#### <a name="saml-restrictions"></a>SAML korlátozások

Ha már használta az Active Directory hitelesítési könyvtárat (ADAL) a Windows-alkalmazásokban, akkor lehet, hogy kihasználta a Windows integrált hitelesítést, amely a Security Assertion Markup Language (SAML) feltételérvényesítési támogatást használja. Ezzel a támogatással az összevont Azure AD-bérlők felhasználói csendben hitelesíthetik magukat a helyszíni Active Directory-példányukkal hitelesítő adatok megadása nélkül. Az SAML-alapkiállítások megadása nem támogatott a Microsoft identity platform végpontján.
