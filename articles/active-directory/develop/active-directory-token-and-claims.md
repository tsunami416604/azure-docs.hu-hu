---
title: További tudnivalók a különböző jogkivonatot, és az Azure AD által támogatott jogcímtípusok |} Microsoft Docs
description: Ismertetése és értékelése a jogcím szerepel a SAML 2.0 és a JSON Web Tokens (JWT) tokenek által Azure Active Directory (AAD) kiadott egy útmutató
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 95ce83a3f1288d1b731aeeb8dcc32e58bcaefe21
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-token-reference"></a>Az Azure AD-jogkivonatok referenciájából
Azure Active Directory (Azure AD) bocsát ki biztonsági jogkivonatainak feldolgozása minden hitelesítési folyamat során számos különböző. Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú lexikális elem tartalmát. 

## <a name="types-of-tokens"></a>A jogkivonatok típusok
Az Azure AD által támogatott a [OAuth 2.0 protokoll](active-directory-protocols-oauth-code.md), amely él access_tokens és refresh_tokens is. Azt is támogatja a hitelesítést és -bejelentkezés keresztül [OpenID Connect](active-directory-protocols-openid-connect-code.md), amely bevezet egy harmadik típusú jogkivonatot, a id_token. Ezeket a jogkivonatokat mindegyikének "tulajdonosi jogkivonattal" értéke jelöli.

Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a "tulajdonos". Abban az értelemben a "tulajdonos", amely a token is jelenthet félre. Bár az Azure AD hitelesítési szükség van egy tulajdonosi jogkivonatot fogadásához, gondoskodni kell biztonságossá tételéhez a jogkivonatot, egy nem kívánt félnek hozzáférés megelőzése érdekében. Tulajdonosi jogkivonatok nem rendelkezik beépített eljárást, amely megakadályozhatja, hogy a nem hitelesített felek a őket, mert azok kell szállítani, például a transport layer security (HTTPS) biztonságos csatornát. Ha egy tulajdonosi jogkivonatot szövegként, egy man-a a középső támadás segítségével jogkivonat és a védett erőforrásokhoz való jogosulatlan hozzáférésre. Az azonos biztonsági elveket alkalmazza, ha a tárolást, vagy a gyorsítótárazás tulajdonosi jogkivonatok későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságos módon tárolja a tulajdonosi jogkivonatokhoz. További biztonsági szempontok a tulajdonosi jogkivonatok, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).

Számos olyan Azure AD által kiállított jogkivonatokat használják, mint a JSON webes jogkivonatainak vagy JWTs. Jwt-t egy kompakt biztonságos URL-címet a eszközöket információk átvitele a két fél között. JWTs szereplő információk a tulajdonosi és a token tárgya "jogcímek" vagy a helyességi feltételek információk néven ismert. A rendszer kódolni és továbbítására szerializált JSON-objektumok a JWTs a jogcímeket. Mivel az Azure AD által kibocsátott JWTs aláírt, de nincs titkosítva, könnyen vizsgálhatja meg a jwt-t tartalmát hibakeresési célra. Nincsenek elérhető például a módon valósíthatja, több eszközt [jwt.ms](https://jwt.ms/). A JWTs további információkért olvassa el a [JWT specification](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens a bejelentkezési biztonsági jogkivonatot, amely az alkalmazás kapja meg, amikor a hitelesítés végrehajtásához egy formája [OpenID Connect](active-directory-protocols-openid-connect-code.md). Jelentésekként jelennek meg [JWTs](#types-of-tokens), és tartalmaznak jogcímeket a felhasználó bejelentkezik az alkalmazás használhat. A jogcímek használata az egy id_token, tetszés szerint – általában arra szolgálnak, fiók adatainak megjelenítéséhez, vagy a hozzáférés-vezérlési döntéseket és alkalmazáson belüli.

Id_tokens aláírt, de jelenleg nincs titkosítva. Ha az alkalmazás egy id_token kap, kell [az aláírás érvényesítése](#validating-tokens) a token hitelességének igazolásához és ellenőrizhesse néhány a token érvényességének igazolásához. A jogcímek, az alkalmazás által érvényesített forgatókönyv követelményeitől függően eltérőek, de van azonban néhány [közös jogcím érvényesítést](#validating-tokens) , amely az alkalmazás minden esetben kell elvégezni.

Lásd a következő információk id_tokens jogcímeket, valamint egy minta id_token. Figyelje meg, hogy bármely adott sorrendben nem lehet megjeleníteni a id_tokens jogcímeket. Emellett új jogcímeket is lehet bevezetni bármikor id_tokens időben, mert az alkalmazás nem törhetik belépéskor új jogcímeket. Az alábbi lista tartalmazza az alkalmazás megbízhatóan tudja értelmezni a írásának időpontjában a jogcímeket. Ha szükséges, akár további részletek találhatók a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>A minta id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Eljárás, próbálja ki a jogcím szerepel a minta id_token ellenőrzése illeszti be [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>A id_tokens jogcímek
> [!div class="mx-codeBreakAll"]
| JWT jogcím | Név | Leírás |
| --- | --- | --- |
| `aud` |Célközönség |A jogkivonat az illetékes címzett. Az alkalmazást, amely megkapja a jogkivonatot kell győződjön meg arról, hogy a célközönség értéke megfelelő-e, és utasítsa el a szükséges jogkivonatokhoz, egy másik célközönség számára készült. <br><br> **Példa SAML érték**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Példaérték JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Alkalmazás hitelesítési környezeti osztályait ismertető dokumentációban |Azt jelzi, hogy az ügyfelek hitelesítésének módját. Egy nyilvános ügyfél értéke 0. Ügyfél-azonosító és a titkos ügyfélkódot használata esetén a értéke 1. <br><br> **Példaérték JWT**: <br> `"appidacr": "0"` |
| `acr` |Hitelesítési környezeti osztályait ismertető dokumentációban |Azt jelzi, hogyan a tulajdonos hitelesített, az ügyfél számára az alkalmazás hitelesítési környezeti osztályait ismertető dokumentációban jogcím szemben. A "0" érték azt jelzi, hogy a végfelhasználói hitelesítési nem felelt meg az ISO/IEC 29115 követelményeinek. <br><br> **Példaérték JWT**: <br> `"acr": "0"` |
| Azonnali hitelesítés |A dátum és idő, amikor hitelesítést történt rögzíti. <br><br> **Példa SAML érték**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Hitelesítési módszer |A token tárgya hitelesítésének módját azonosítása <br><br> **Példa SAML érték**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Példaérték JWT**: `“amr”: ["pwd"]` |
| `given_name` |Utónév |Az első biztosít vagy az "adott" a felhasználó nevét, az Azure AD-felhasználói objektum vannak megadva. <br><br> **Példa SAML érték**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"given_name": "Frank"` |
| `groups` |Csoportok |Az objektumazonosító, amelyek megfelelnek a tulajdonos csoporttagságok biztosít. Ezek az értékek egyedi (lásd: objektumazonosító:) és a hozzáférés, például az erőforrások eléréséhez engedélyezési kényszerítése biztonságosan használható. A csoportok a csoportok jogcím szerepel-alkalmazásonként, az alkalmazás jegyzékében "groupMembershipClaims" tulajdonságának használatával kell beállítani. Null értékű kizárja az összes csoport, csak az Active Directory biztonsági csoport tagságát, és a "All" lesz tartalmazzák a biztonsági csoportok és Office 365 terjesztési listák "a(z)"biztonsági csoporthoz értéket tartalmazza. <br><br> **Megjegyzések**: <br> Lásd: a `hasgroups` használatával alatt jogcím a `groups` jogcím implicit megadását. <br> Az egyéb folyamatok Ha a felhasználó csoportok száma meghaladja a (150 SAML, 200 a jwt-t) kerül, majd egy túlhasználati jogcím megkapja a jogcím-adatforrások irányul-e a Graph-végpont a felhasználói csoportok listáját tartalmazó. (a. <br><br> **Példa SAML érték**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Példaérték JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | JWT implicit engedélyezési folyamat csoportok keretét kijelző| Ha van ilyen, mindig `true`, azt jelöli, a felhasználó van legalább egy csoportot. Helyett használja a `groups` implicit grant flow, ha a teljes csoportok jogcím JWTs a jogcím eleve az URI-töredéket beállításban megadott URL-cím hossza korlát (jelenleg a 6-os vagy további csoportokat). Azt jelzi, hogy az ügyfélnek használnia kell a diagramon, a felhasználói csoport meghatározásához (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Csoportok keretét kijelző | A jogkivonat-kérelmeket, amelyek nem korlátozott hossza (lásd: `hasgroups` fent), de továbbra is túl nagy a jogkivonatot, a felhasználó a teljes listájának mutató hivatkozást is fog szerepelni. Az elosztott jogcímként, a SAML helyett egy új jogcímként JWTs a `groups` jogcímek. <br><br> **Példa SAML érték**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Identitásszolgáltató |Az identitásszolgáltató, amely a token tárgya hitelesített rögzíti. Ez az érték megegyezik a a kibocsátó jogcím értékét, kivéve, ha a felhasználói fiók pedig egy másik bérlői, mint a kibocsátó. <br><br> **Példa SAML érték**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Az idő, amelynél a jogkivonat ki tárolja. Gyakran használt token frissesség mérését. <br><br> **Példa SAML érték**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Példaérték JWT**: <br> `"iat": 1390234181` |
| `iss` |Kiállító |Azonosítja a biztonságijogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot ad vissza. A jogkivonatok az Azure AD eredményül a kibocsátó sts.windows.net. A globálisan egyedi Azonosítót a kibocsátó jogcím értékét a rendszer a bérlő azonosítója az Azure AD-címtár. A bérlő azonosítója nem módosítható és megbízható azonosítója, amelyet a könyvtárban. <br><br> **Példa SAML érték**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Példaérték JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Vezetéknév |Az utolsó nevét, Vezetéknév vagy családnév felhasználó biztosít az Azure AD-felhasználói objektum. <br><br> **Példa SAML érték**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Név |Emberi olvasható érték, amely azonosítja a token tárgya biztosít. Ez az érték nem garantált a bérlő belül egyedinek kell lennie, és célja, hogy csak a megjelenítésre használható. <br><br> **Példa SAML érték**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Objektumazonosító |Egy objektum egyedi azonosítóját tartalmazza az Azure ad-ben. Ez az érték nem módosítható és nem lehet újbóli hozzárendelése és nem használja fel újra. Az Objektumazonosító segítségével azonosíthatja a lekérdezésekben az Azure AD-objektum. <br><br> **Példa SAML érték**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Szerepkörök |Minden alkalmazás szerepkör, amely a tárgy rendelkezik közvetlen és közvetett csoporttagság, és a szerepköralapú hozzáférés-vezérlést használható jelöli. Alkalmazási szerepköröknek meghatározott alkalmazásonkénti bontásban keresztül a `appRoles` tulajdonsága az alkalmazás jegyzékében. A `value` minden egyes alkalmazás-szerepkör tulajdonsága, amely akkor jelenik meg, a szerepkör jogcím értékét. <br><br> **Példa SAML érték**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Példaérték JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Hatókör |Azt jelzi, hogy az ügyfélalkalmazás számára megadott megszemélyesítési engedélyeket. Az alapértelmezett engedély `user_impersonation`. A védett erőforrás tulajdonosa további értékeket regisztrálhatja az Azure AD-ben. <br><br> **Példaérték JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Tárgy |Azonosítja a rendszerbiztonsági tag, amelyekről a token állításokat információkat, például egy alkalmazás felhasználója. Ez az érték nem módosítható és nem társítható újra, vagy használja fel újra, így használható biztonságosan hitelesítési ellenőrzések elvégzéséhez. A tulajdonos mindig megtalálható-e a jogkivonatok az Azure AD-problémák, mert azt javasoljuk, használja ezt az értéket egy általános célú engedélyezési rendszerben. <br> `SubjectConfirmation` Nincs olyan jogcímet. Leírja, hogyan ellenőrzése a jogkivonat tárgyát. `Bearer` azt jelzi, hogy a tulajdonos megerősíti a token birtokában. <br><br> **Példa SAML érték**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Példaérték JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Bérlő azonosítója |Nem módosítható, egyszer használatos azonosító, amely azonosítja a directory-bérlő a jogkivonatot kibocsátó. Egy több-bérlős alkalmazás bérlői-specifikus könyvtár erőforrásainak eléréséhez használhatja ezt az értéket. Például ez az érték segítségével azonosíthatja a bérlő által a Graph API hívása. <br><br> **Példa SAML érték**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |A jogkivonatok élettartama |Meghatározza a időtartam alatt, amelyen belül a lexikális elem érvénytelen. A szolgáltatás, amely érvényesíti a jogkivonatot győződjön meg arról, hogy az aktuális dátumot a jogkivonat élettartamát, más azt kell utasítania a token belül van-e. A szolgáltatás előfordulhat, hogy hagyjon akár öt percet a jogkivonatok élettartama tartományon kívül az eltérések idő ("idő döntés") a fiókot az Azure AD között és a szolgáltatás. <br><br> **Példa SAML érték**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Példaérték JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Egyszerű felhasználónév |Tárolja a felhasználó egyszerű felhasználónevét.<br><br> **Példaérték JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Verzió |A token a verziószámát tárolja. <br><br> **Példaérték JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Hozzáférési jogkivonatok
Sikeres hitelesítés után az Azure AD hozzáférési tokent, amely védett erőforrások eléréséhez használható adja vissza. A hozzáférési token base 64 kódolású JSON webes jogkivonat (JWT), és annak tartalmát a dekóder keresztül futtatásával ellenőrizni kell.

Ha az alkalmazás csak *használ* hozzáférési jogkivonatok is elérheti az API-k, is (és kell) tekinti hozzáférési jogkivonatok nem teljesen átlátszó – csak karakterláncok, amelyek az alkalmazás képes továbbadni a HTTP-kérelmek erőforrások.

Amikor kér le a hozzáférési tokent, akkor az Azure AD is az néhány a hozzáférés-jogkivonattá az alkalmazás metaadatainak adja vissza. Ezen információk közé tartozik a lejárat időpontjának a hozzáférési jogkivonat és a hatókörök, amelyek esetében érvényes. Ez lehetővé teszi az alkalmazás hajtsa végre az intelligens gyorsítótárazás hozzáférési jogkivonatok nem kell elemezni, nyissa meg a hozzáférési jogkivonat magát.

Ha az alkalmazás egy API-t, hogy a hozzáférési jogkivonatok vár a HTTP-kérelmek Azure AD-val védett, majd végre kell hajtania érvényesítési és a jogkivonatok kap vizsgálata. Az alkalmazás érvényesítése és a hozzáférési jogkivonat erőforrások eléréséhez annak használata előtt végre kell hajtania. Az érvényesítési további információkért lásd: [ellenőrzése jogkivonatok](#validating-tokens). További tudnivalók erről a .NET [egy Azure ad-tulajdonosi jogkivonatok segítségével webes API védelme](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Frissítési jogkivonatok

Frissítési jogkivonatok olyan biztonsági jogkivonatok, amelyek az alkalmazás segítségével szerezzen be új hozzáférési jogkivonatok az OAuth 2.0 folyamatból. Ez lehetővé teszi, hogy az alkalmazás által a felhasználói beavatkozás nélkül hosszú távú hozzáférést egy felhasználó nevében erőforrások eléréséhez.

Frissítési jogkivonatok több erőforrás. Vagyis, hogy a frissítési token során egy erőforrást a kérés érkezett is váltható a hozzáférési jogkivonatok egy teljesen más erőforráshoz. Ehhez az szükséges, állítsa be a `resource` paramétert a célként megadott erőforrás a kérelem.

Frissítési jogkivonatok nem teljesen átlátszó, az alkalmazásban. Hosszú élettartamú, de az alkalmazás nem írható várható, hogy egy frissítési jogkivonat bármely ideig tart. Lehet, hogy a frissítési jogkivonatokat érvénytelenített időben számos okból - bármikor lásd [Token visszavonási](#token-revocation) ezen okok miatt. Csak az alkalmazás tudja, hogy a frissítési jogkivonat érvényes, beváltani azt az Azure AD-jogkivonat végpontjához kérelmek, így sikertelen bejelentkezési kísérletet.

Amikor új tokenre egy frissítési token beváltásához kapni fog egy új frissítési jogkivonat a biztonságijogkivonat-válaszban. Mentse az újonnan kiadott frissítési jogkivonat helyett a egy, a kérésben használt. Ez garantálja, hogy a frissítési jogkivonatokat maradnak, amíg érvényes.

## <a name="validating-tokens"></a>Jogkivonatok ellenőrzése

Ellenőrizni fogja az id_token vagy egy access_token, az alkalmazás érdemes ellenőrizni a jogkivonat aláírása és a jogcímeket. Ellenőrizni fogja az hozzáférési jogkivonatok, az alkalmazás is ellenőrizni kell a kibocsátó, a célközönség és az aláírási jogkivonatokat. Ezek kell érvényesíteni az értékekkel, amelyet a OpenID felderítési dokumentum. Például a bérlő független a dokumentum verziója található [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Az Azure AD köztes ellenőrzéséhez a hozzáférési jogkivonatok beépített funkciókkal rendelkezik, és akkor böngészhet a [minták](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) az Ön által választott nyelven kereséséhez. Explicit módon ellenőrizze a JWT jogkivonat kapcsolatos további információkért lásd: a [JWT-ellenőrző manuális minta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Könyvtárak és kódpéldák, amelyek bemutatják, hogyan könnyen kezelni a jogkivonatok érvényesség-ellenőrzése - nyújtunk az alábbi információkat egyszerűen megadott rendelkező szeretné tudni, hogy az alapul szolgáló folyamat. Nincsenek is több harmadik féltől származó nyílt forráskódú szalagtárak JWT érvényesítési,-szinte minden platform és a nyelv ott legalább egy beállítást. Az Azure AD hitelesítési könyvtárak és kódpéldák kapcsolatos további információkért lásd: [az Azure AD-hitelesítési kódtárakkal](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Az aláírás érvényesítése

A jwt-t tartalmazza az három, az elemek elválasztására pedig a `.` karakter. Az első szegmensnek is ismert, a **fejléc**, a második a **törzs**, és a harmadik, mint a **aláírás**. Az aláírás szegmens segítségével hitelességének a jogkivonatot, hogy az alkalmazás által megbízhatóak.

Az Azure AD által kiállított jogkivonatokat aláírt iparági szabványos aszimmetrikus titkosítási algoritmusok, például RSA 256 használatával. A JWT fejlécében a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

A `alg` jogcím jelzi a token, igénybe aláírásához használt algoritmust a `x5t` jogcím jelzi az adott a token aláírásához használt nyilvános kulcsot.

Az idő álljon az Azure AD aláírhatja egy id_token közül legalább egy bizonyos készlet nyilvános-titkos kulcspárok használatával. Az Azure AD forog kulcsok rendszeres időközönként, a lehetséges készletét, így az alkalmazás kezelni azokat automatikusan kell írni. Az Azure AD által használt nyilvános kulcsok frissítések kereséséhez ésszerű gyakoriságát 24 óránként van.

Az aláírási kulcs adatok ellenőrizni az aláírást az OpenID Connect metaadat-dokumentum található használatával szerezheti be:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Próbálja meg egy böngészőben az URL-címet!
> 
> 

A metaadat-dokumentum egy JSON-objektum tartalmazó számos hasznos információt tartalmaz, például a különböző végpontok OpenID Connect hitelesítési végrehajtásához szükséges hely. 

Ezenkívül tartalmazza a `jwks_uri`, ennek révén a jogkivonatok aláírásához használt nyilvános kulcsok készlete helyét. A JSON-dokumentum található a `jwks_uri` tartalmazza az összes idő az adott pillanatban használja a nyilvános kulcs adatait. Az alkalmazás használhatja a `kid` jogcím válassza ki, melyik nyilvános kulcs ebben a dokumentumban már használta egy adott jogkivonat aláírása a JWT-fejlécben. Végezhet el az aláírás érvényesítése a megfelelő nyilvános kulccsal és a megadott algoritmus használatával.

Aláírás-ellenőrzés végrehajtása a jelen dokumentum nem terjed – nincsenek számos nyílt forráskódú szalagtárak segít, ha szükséges.

#### <a name="validating-the-claims"></a>A jogcímek ellenőrzése

Ha az alkalmazás (egy id_token felhasználói bejelentkezés során, vagy olyan hozzáférési jogkivonatot, mint a HTTP-kérelmek tulajdonosi jogkivonattal) jogkivonatot kap, a jogkivonat is végre kell hajtania néhány ellenőrzések szemben. Ezek közé tartozik, de nincsenek korlátozva:

* A **célközönség** jogcím - győződjön meg arról, hogy a jogkivonat kell fordítani az alkalmazás szándéka.
* A **nem előtt** és **lejárati idő** jogcímeket - győződjön meg arról, hogy a jogkivonat nem járt le.
* A **kibocsátó** jogcím - győződjön meg arról, hogy a jogkivonat valóban ki az alkalmazás által az Azure AD.
* A **Nonce** – a hitelesítési karakterláncok ismétlésének támadás elhárítása érdekében.
* és egyéb...

Az alkalmazás végre kell hajtania, azonosító-jogkivonatokat a jogcím érvényesítést teljes listájáért tekintse meg a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Ezeket a jogcímeket a várt értékek részletek szerepelnek az előző [id_token](#id-tokens) szakasz.

## <a name="token-revocation"></a>Jogkivonat visszavonása

Frissítési jogkivonatok érvénytelenítve, vagy bármikor, számos okból visszavonva. Ezek két fő kategóriába sorolhatók: időtúllépések és hibával találkoznak. 
* Token időtúllépések
  * MaxInactiveTime: Ha a frissítési jogkivonat még egyszer sem használták a MaxInactiveTime meghatározni időn belül, a frissítési Token már nem érvényes lesz. 
  * MaxSessionAge: Ha nem az alapértelmezett (amíg visszavont) MaxAgeSessionMultiFactor vagy MaxAgeSessionSingleFactor állított be, majd ismételt hitelesítés lesz szükség a beállított a MaxAgeSession * idő elteltével. 
  * Példák:
    * A bérlő rendelkezik egy 5 napos MaxInactiveTime szabadságon egy hétig merült fel a felhasználó, és így aad-ben nem tapasztalt felhasználói új kérés 7 nap múlva. A következő alkalommal a felhasználó kéri egy új jogkivonatot, azok megkeresi a frissítési Token visszavonták, és újra azokat kell adnia a hitelesítő adataikat. 
    * A bizalmas alkalmazásokhoz rendelkezik egy MaxAgeSessionSingleFactor 1 nap. Ha egy felhasználó bejelentkezése hétfőjén és keddjén (miután 25 óra telt el), akkor le kell újra hitelesíteni. 
* Visszavonási
  * Önkéntes jelszómódosítás: Ha a felhasználó megváltoztatja a jelszavát, előfordulhat, hogy rendelkeznek az alkalmazásaikat, attól függően, hogy a jogkivonat volt elérni módja némelyike különböző újra hitelesíteni. Tekintse meg a kivételek az alábbi megjegyzésekben. 
  * Akaratlan jelszómódosítás: Ha a rendszergazda kényszeríti a felhasználót, hogy módosítania kell a jelszavát, vagy visszaállítja azt, majd a felhasználói jogkivonatokhoz érvénytelenné válnak ha volt elérni a használatával a jelszavát. Tekintse meg a kivételek az alábbi megjegyzésekben. 
  * Biztonság megsértése: (Pl. a jelszavak a helyi tárolójába megszegése) biztonsági problémák esetén a rendszergazdának vissza tudja vonni a frissítési jogkivonatokat jelenleg kiadott összes. Ezzel kikényszeríti a minden felhasználó számára újra hitelesíteni. 

> [!NOTE]
>Ha a hitelesítési módszer nem jelszó nem használható (a Windows Hello-, a hitelesítő alkalmazása, például egy oldallal vagy az ujjlenyomat biometria) a token eléréséhez, a jelszó módosítása nem kényszeríti a felhasználónak újra hitelesíteni (de ez arra kényszeríti a hitelesítő alkalmazás újra hitelesíteni). Ez azért, mert a kiválasztott hitelesítési bemeneti (a arc, pl.) nem változott, és ezért használható újra újra hitelesíteni.
>
> Jelszó módosítása által okozott hibával találkozzanak, bizalmas ügyfelek nem érinti. A jelszó módosítása továbbra is, hogy frissítési jogkivonat használatával további jogkivonatokhoz abl kiadott frissítési jogkivonatok bizalmas ügyfél. 

## <a name="sample-tokens"></a>A minta jogkivonatok

Ez a szakasz SAML és JWT-jogkivonatokat, amely visszaadja az Azure AD mintáit jeleníti meg. Ezeket a mintákat lehetővé teszik, hogy tekintse meg a jogcímeket a környezetben.

### <a name="saml-token"></a>SAML-jogkivonat

Ez a minta egy tipikus SAML-jogkivonat.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT jogkivonat - felhasználó megszemélyesítése
Ez a jellemző JSON webes jogkivonatot (JWT) szerepel egy engedélyezési kód grant flow mintáját.
Jogcímek, az mellett a token tartalmaz egy verziószámot a **ver** és **appidacr**, a hitelesítési környezeti osztályhivatkozást, amely azt jelzi, hogy az ügyfél hitelesítési. Egy nyilvános ügyfél értéke 0. Ha egy ügyfél-azonosító vagy a titkos ügyfélkulcs használt, értéke 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Kapcsolódó tartalom
* Tekintse meg az Azure AD Graph [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) és a [házirend entitás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), tudhat meg többet az Azure AD Graph API-n keresztül a jogkivonatok élettartama házirend kezelése.
* További információk és minták a PowerShell-parancsmagok, többek között a minták keresztül házirendek kezelése [konfigurálható jogkivonat élettartamát az Azure AD](../active-directory-configurable-token-lifetimes.md). 
* Adja hozzá [egyéni és az opcionális jogcímek](active-directory-optional-claims.md) a jogkivonatok az alkalmazás számára. 
