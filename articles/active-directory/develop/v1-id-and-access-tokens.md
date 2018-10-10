---
title: Ismerje meg a másik tokent, és az Azure AD által támogatott jogcímtípusok |} A Microsoft Docs
description: Ismertetése és értékelése a szerint az Azure Active Directory (AAD) kiállított SAML 2.0 és a JSON Web tokenek (JWT) tokenek jogcímeit készült útmutató
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
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a8d4f2894a188ce83939180def65c5f5b058d215
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902212"
---
# <a name="azure-ad-token-reference"></a>Az Azure AD jogkivonat-referencia
Az Azure Active Directory (Azure AD) számos különböző típusú feldolgozása minden hitelesítési folyamat során a biztonsági jogkivonatokat bocsát ki. Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú jogkivonat tartalma. 

## <a name="types-of-tokens"></a>Jogkivonatok típusai
Az Azure AD támogatja a [OAuth 2.0 engedélyezési protokollt](v1-protocols-oauth-code.md), amely él access_tokens és refresh_tokens is. Azt is támogatja a hitelesítést és a bejelentkezési keresztül [OpenID Connect](v1-protocols-openid-connect-code.md), amely vezet be a jogkivonatot, a id_token egy harmadik típusú. Mindegyik token "tulajdonosi jogkivonattal" értéke jelöli.

A tulajdonosi jogkivonatot, amely hozzáférést biztosít a "tulajdonos" védett erőforrásokhoz való könnyű biztonsági jogkivonat. Ebben az értelemben a "tulajdonos" minden olyan entitás, amely a token is jelenthet. Bár az Azure AD-hitelesítés szükséges tulajdonosi jogkivonattal fogadásához, gondoskodni kell a jogkivonatot, hogy egy nem kívánt fél hozzáférés biztonságossá tételéhez. Tulajdonosi jogkivonatokat nem rendelkeznek olyan beépített mechanizmus megakadályozza, hogy illetéktelen fél használja őket, mert egy biztonságos csatornán, például a transport layer security (HTTPS) kell szállítani. A titkosítatlan továbbított tulajdonosi jogkivonattal, ha egy man-a a középső támadás is használható a token beszerzéséhez és a egy védett erőforrás jogosulatlan elérésére. A biztonsági alapelveket alkalmazható, ha a tárolásával, sem a gyorsítótárazás a tulajdonosi jogkivonatokat későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](http://tools.ietf.org/html/rfc6750).

Számos, az Azure AD által kiállított jogkivonatokat használják, mint a JSON webes jogkivonatainak vagy JWTs. Jwt-t a tömör, URL-cím környezetben is biztonságos módszerrel információk átvitele a két fél között. JWTs szereplő információk nevezik "jogcímek" vagy a helyességi feltételek információk a tulajdonosi és a jogkivonat tárgyában. JWTs jogcímeket kódolva, és továbbítására szerializált JSON-objektumok. Mivel az Azure AD által kiállított JWTs jelentkezett, de nincs titkosítva, egyszerűen vizsgálhatja meg a jwt-t tartalmát hibakeresési célra. Nincsenek elérhető végrehajtására, például több eszközt [jwt.ms](https://jwt.ms/). A JWTs további információkért olvassa el a [JWT-specifikáció](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens a biztonsági jogkivonat, amely az alkalmazás fogad a hitelesítés végrehajtásához egy formája [OpenID Connect](v1-protocols-openid-connect-code.md). Jelentésekként jelennek meg [JWTs](#types-of-tokens), és tartalmazza a jogcímek az alkalmazásba a felhasználó az aláíráshoz használható. A jogcímek használhatja id_token, igény szerint – a gyakran használt azokat az fiók adatainak megjelenítéséhez, vagy hogy döntést hozhasson a hozzáférésről egy alkalmazásban.

Id_tokens jelentkezett, de jelenleg nincs titkosítva. Amikor az alkalmazás megkapja egy id_token, kell [érvényesíteni az aláírást](#validating-tokens) a token hitelességének igazolásához, és ellenőrizni annak érvényességét igazolásához néhány jogcímeket. A jogcímek, az alkalmazás által ellenőrzött forgatókönyv követelményeitől függően eltérőek lehetnek, de van néhány [közös jogcím ellenőrzések](#validating-tokens) , amely az alkalmazás minden esetben kell elvégezni.

Id_tokens jogcímeket, valamint egy minta id_token tekintse meg a következő szakaszban talál. Vegye figyelembe, hogy a jogcímek, az id_tokens nem bármely adott sorrendben adja vissza. Emellett új jogcímeket is lehet bevezetni bármikor id_tokens időben – az alkalmazás tilos megszüntetnie, új jogcímeket jelennek meg. Az alábbi lista tartalmazza az alkalmazás megbízható tudja értelmezni a írásának időpontjában a jogcímeket. Ha szükséges, további részletek találhatók a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Minta id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Eljárás, próbálja meg illessze be azt a mintát id_token jogcímeket vizsgálatával [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>A id_tokens jogcímek
> [!div class="mx-codeBreakAll"]
| JWT-jogcím | Name (Név) | Leírás |
| --- | --- | --- |
| `aud` |Célközönség |A jogkivonat az illetékes címzett. Az alkalmazás, amely megkapja a token ellenőriznie kell, hogy a célközönség érték helyességéről, valamint egy másik közönség számára készült jogkivonatokat elutasítása. <br><br> **Példaérték SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Példaérték JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Alkalmazáshitelesítési környezet osztályhivatkozása |Azt jelzi, hogy az ügyfél hitelesítése. Nyilvános-ügyfél értéke 0. Ügyfél-Azonosítóját és ügyfélkulcsát használnak, ha az érték az 1. Ügyféltanúsítvány a hitelesítéshez használt, ha az értéke 2. <br><br> **Példaérték JWT**: <br> `"appidacr": "0"` |
| `acr` |Hitelesítési környezethez tartozó Osztályhivatkozása |Azt jelzi, hogy hogyan tárgya volt hitelesíti, szemben az ügyfél az alkalmazás hitelesítési környezethez tartozó Osztályhivatkozása jogcím. "0" érték azt jelzi, hogy a végfelhasználói hitelesítés nem felelt meg az ISO/IEC 29115 követelményeinek. <br><br> **Példaérték JWT**: <br> `"acr": "0"` |
| Hitelesítési időpont |A dátum és idő, mikor történt a hitelesítés rögzíti. <br><br> **Példaérték SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Hitelesítési módszer |Azonosítja a jogkivonat tárgyában hitelesítésének módját. <br><br> **Példaérték SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Példaérték JWT**: `“amr”: ["pwd"]` |
| `given_name` |Utónév |Itt az első vagy az "adott" a felhasználó neve az Azure AD-felhasználói objektum vannak megadva. <br><br> **Példaérték SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"given_name": "Frank"` |
| `groups` |Csoportok |A tulajdonos csoporttagságok képviselő objektumazonosítók biztosít. Ezek az értékek egyedi (lásd az Objektumazonosító) és biztonságosan használható például az erőforrás hozzáférési kényszerítése a hozzáférés kezelésében. A csoportok jogcím szerepel a csoportok alkalmazásonkénti alapon, az alkalmazásjegyzékben "groupMembershipClaims" tulajdonságát keresztül vannak konfigurálva. NULL értékű kizárja az összes csoport, csak az Active Directory biztonsági csoport tagságát, és a egy értéket "All" lesz tartalmazzák a biztonsági csoportok és az Office 365 terjesztési listák "SecurityGroup" értéket tartalmazza. <br><br> **Megjegyzések**: <br> Tekintse meg a `hasgroups` használata részletesen alábbi jogcím a `groups` az implicit engedélyezés jogcím. <br> Más folyamatok Ha a felhasználó szerepel csoportok száma (150 SAML, 200 JWT számára) korlátozza keresztül haladnak, majd egy szint kerettúllépése jogcím bekerül a jogcím-adatforrások mutat, amely tartalmazza azokat a csoportokat a felhasználó Graph-végpont. (a. <br><br> **Példaérték SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Példaérték JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | JWT Implicit folyamat csoportok kerettúllépési kijelző| Ha van ilyen, mindig `true`, jelzi, hogy a felhasználó szerepel-e legalább egy csoporthoz. Helyén használt a `groups` JWTs jogcím típusú implicit engedélyezés folyamatokban, ha a teljes csoportok jogcím lenne kiterjesztése a URI töredék kívül eső URL-cím hossza (jelenleg a 6 vagy a további csoportokat). Azt jelzi, hogy az ügyfélnek használnia kell a grafikon, a felhasználói csoportok meghatározására (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Kereten túli kijelző csoportok | A jogkivonat-kérelmeket, amelyek nem korlátozott hossza (lásd: `hasgroups` fent), de továbbra is túl nagy a jogkivonatot, a felhasználó a teljes csoportok listáját mutató hivatkozást tartalmaz. Az elosztott jogcímként, a SAML helyén új jogcímként JWTs a `groups` jogcím. <br><br> **Példaérték SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Identitásszolgáltató |A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállító jogcím értékét, kivéve, ha a felhasználói fiók, mint a kibocsátó egy másik bérlőben található. <br><br> **Példaérték SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Az idő, amikor a jogkivonat kiállított tárolja. Gyakran token frissessége mérésére szolgál. <br><br> **Példaérték SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Példaérték JWT**: <br> `"iat": 1390234181` |
| `iss` |Kiállító |Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatát adja vissza. A jogkivonatokat, amely az Azure AD ad vissza a kibocsátó sts.windows.net. A globálisan egyedi Azonosítót a kibocsátó jogcím értékét a rendszer a bérlő Azonosítóját az Azure AD-címtár. A bérlő azonosítója nem módosítható és megbízható azonosítója, amelyet a könyvtárban. <br><br> **Példaérték SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Példaérték JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Vezetéknév |Az utolsó neve, Vezetéknév vagy felhasználó család neve biztosít az Azure AD-felhasználói objektum. <br><br> **Példaérték SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Name (Név) |A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált egy bérlőn belül egyedinek kell lennie, és célja, hogy csak megjelenítési célokra használhatók. <br><br> **Példaérték SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Objektumazonosító |Tartalmazza az objektum egyedi azonosítója az Azure ad-ben. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Az Objektumazonosító segítségével azonosíthatja a lekérdezésekben az Azure AD-objektum. <br><br> **Példaérték SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Szerepkörök |Az összes alkalmazás-szerepkörök, amelyek a tárgy megkapta-e közvetlen és közvetett csoporttagság, és a szerepköralapú hozzáférés-vezérlés kikényszerítésére használható jelöli. Alkalmazás-szerepkörök keresztül definiált alkalmazás alapon, az `appRoles` az alkalmazásjegyzékben tulajdonságát. A `value` egyes alkalmazás-szerepkörökhöz tulajdonság értékét, amely a szerepkör jogcím jelenik meg. <br><br> **Példaérték SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Példaérték JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Hatókör |Azt jelzi, hogy az ügyfélalkalmazás rendelt megszemélyesítési engedélyeket. Az alapértelmezett engedély `user_impersonation`. A védett erőforrás tulajdonosa további értékek regisztrálhat az Azure ad-ben. <br><br> **Példaérték JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Tárgy |Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például a felhasználó az alkalmazás azonosítja. Ez az érték nem módosítható és nem lehet hozzárendelni, vagy újra felhasználhatók, tehát használat biztonságosan hitelesítési ellenőrzések elvégzéséhez. A tulajdonos mindig szerepel a jogkivonatokat az Azure AD-problémák, mert javasoljuk ennek az értéknek egy általános célú engedélyezési rendszerben. <br> `SubjectConfirmation` jogcím nem áll. Leírja, hogyan ellenőrzése a jogkivonat tárgyában. `Bearer` azt jelzi, hogy a jogkivonat birtokában megerősíti a tulajdonos. <br><br> **Példaérték SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Példaérték JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Bérlőazonosító |Nem módosítható, egyszer használatos azonosító, amely azonosítja a directory-bérlővel, amely kiállította a jogkivonatot. Ez az érték használatával egy több-bérlős alkalmazásban a bérlő-specifikus directory erőforrásainak eléréséhez. Ez az érték segítségével például a bérlő a Graph API-hívással azonosíthatja. <br><br> **Példaérték SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Példaérték JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Jogkivonat élettartama |A jogkivonat érvényességi idejét határozza meg. A szolgáltatás, amely érvényesíti a jogkivonatot kell győződjön meg arról, hogy az aktuális dátumot a jogkivonat élettartamát, más azt kell utasítania a token belül van-e. A szolgáltatás lehetővé teheti a akár öt percet a jogkivonat élettartama tartományon kívül áthidalhatók az idő ("idő döntés") a különbségeket az Azure AD között és a szolgáltatás. <br><br> **Példaérték SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Példaérték JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Felhasználó egyszerű neve |Tárolja a felhasználó egyszerű felhasználóneve.<br><br> **Példaérték JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Verzió |A jogkivonat verziószáma tárolja. <br><br> **Példaérték JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Hozzáférési jogkivonatok
Sikeres hitelesítést követően az Azure AD hozzáférési tokent, védett erőforrások eléréséhez használt adja vissza. A hozzáférési token egy base 64 kódolású JSON webes jogkivonat (JWT), és annak tartalmát a dekóder keresztül futtatásával is megvizsgálni.

Ha az alkalmazás csak *használja* eléréséhez hozzáférési jogkivonatok az API-khoz, lehetséges (és javasolt) gyökérkönyvtárral hozzáférési jogkivonatok teljesen átlátszatlan – csak a karakterláncokhoz, amelyek az alkalmazás továbbíthat a HTTP-kérések erőforrások.

Egy hozzáférési jogkivonatot kér, amikor az Azure AD, az is kínál az alkalmazás a hozzáférési jogkivonat bizonyos metaadatait adja vissza. Ezen információk közé tartozik a hozzáférési jogkivonatot, és a hatóköröket, amelyek esetében érvényes lejárati idejét. Ez lehetővé teszi az alkalmazás intelligens gyorsítótárazás hozzáférési jogkivonatok, nem kell elemezni, nyissa meg a hozzáférési jogkivonat magát.

Ha az alkalmazás egy API-t, amely a HTTP-kérések hozzáférési jogkivonatok vár Azure AD-vel védett, majd végrehajtandó érvényesítési és ellenőrzési token kap. Az alkalmazás végre kell hajtania a hozzáférési jogkivonat érvényesítése és erőforrások eléréséhez. Érvényesítési további információkért tekintse meg [érvényesítése jogkivonatok](#validating-tokens). Ehhez a .NET-tel kapcsolatos részletekért lásd: [védelmet egy webes API-t az Azure AD-ből a tulajdonosi jogkivonatokat](quickstart-v1-dotnet-webapi.md).

## <a name="refresh-tokens"></a>Frissítési jogkivonatok

Frissítési jogkivonatok olyan biztonsági jogkivonatok, amelyek az alkalmazás az OAuth 2.0-folyamat az új hozzáférési jogkivonatok beszerzésére használható. Lehetővé teszi az alkalmazás hosszú távú egy felhasználó nevében erőforrásokhoz való hozzáférés érdekében anélkül, hogy a felhasználó általi alkalmazása közben.

Frissítési biztonsági jogkivonat több erőforrást is. Tehát, hogy a frissítési jogkivonatot kapott egy erőforráshoz jogkivonatkérések közben váltható hozzáférési jogkivonatok egy teljesen más erőforráshoz. Ehhez állítsa a `resource` paramétert a kérelmet a célzott erőforráson.

Frissítési jogkivonatok olyan teljesen átlátszatlan, az alkalmazáshoz. Hosszú élettartamú, azonban az alkalmazás várható, hogy a frissítési jogkivonatok minden időszak, amíg a rendszer utolsó nem kell írni. Lehet, hogy a frissítési biztonsági jogkivonat érvénytelenített jelennek meg a számos okból - bármikor [visszavonni a tokeneket](#token-revocation) ebből kifolyólag. Az egyetlen módszer az alkalmazás tudja, hogy ha egy frissítési jogkivonat érvényes, hogy azáltal, hogy a jogkivonat kérése az Azure AD-jogkivonat végpontra a beváltáshoz kísérlet.

Amikor egy új hozzáférési jogkivonatot a frissítési jogkivonat beváltása, kapni fog egy új frissítési jogkivonatot a jogkivonat válaszban. Mentse az újonnan kiállított frissítési jogkivonatot, és cserélje le azt a kérésben használt. Ez garantálja, hogy a frissítési biztonsági jogkivonat érvényes, amíg maradnak.

## <a name="validating-tokens"></a>Token ellenőrzése

Annak érdekében, hogy id_token és a egy access_token ellenőrzéséhez az alkalmazás ellenőrizni kell a jogkivonat-aláírási és a jogcímeket. Annak érdekében, hogy hozzáférési jogkivonatainak érvényesítéséhez, az alkalmazás is ellenőrizni kell a kibocsátó, a célközönség és az aláíró jogkivonatokat. Ezek kell érvényesíteni az OpenID felderítési dokumentum értékekkel. Például a bérlő független a dokumentum verziója található: [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Az Azure AD-közbenső hozzáférési jogkivonatok érvényességének a beépített funkciókkal rendelkezik, és akkor is böngészhet a [minták](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) található egy Ön által választott nyelven. Explicit módon ellenőrzése a JWT jogkivonat további információkért tekintse meg a [JWT-ellenőrzés manuális minta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Kódtárak és kódmintákkal, amelyek bemutatják, hogyan könnyedén megfelelhet a jogkivonat érvényesítésére - biztosítunk az alábbi információkat egyszerűen megadott azok számára, akik szeretné tudni, hogy az alapul szolgáló folyamat. Érhetők el is számos harmadik féltől származó nyílt forráskódú függvénytárak JWT-ellenőrzés – legalább egy lehetőség a szinte minden platformon és nyelven is elérhető. Az Azure AD hitelesítési kódtárai és Kódminták kapcsolatos további információkért tekintse meg [az Azure AD hitelesítési kódtárai](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Az aláírás ellenőrzése

Jwt-t tartalmaz három szegmensek, amelyeket a `.` karakter. Az első szegmens más néven a **fejléc**, mint a második a **törzs**, és a harmadik, mint a **aláírás**. Az aláírás szegmens a token hitelességének ellenőrzéséhez, hogy az alkalmazás által is megbízhatónak használható.

Az Azure AD által kiállított jogkivonatokban van bejelentkezve az iparági szabványos aszimmetrikus titkosítási algoritmusok, például RSA-256 használatával. A JWT fejlécében a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A `alg` jogcím azt jelzi, hogy a jogkivonat, ideje aláírásához használt algoritmust az `x5t` jogcím azt jelzi, hogy a jogkivonat aláírásához használt adott nyilvános kulcs.

Lekérdezhet az idő az Azure AD egy meghatározott készlete nyilvános-titkos kulcspárok használatával id_token előfordulhat, hogy alá. Az Azure AD elforgatása a kulcsok rendszeres időközönként, lehetséges készletét, így az alkalmazás automatikusan kezeli a fő módosításokat kell írni. Az Azure AD által használt nyilvános kulcsok frissítései ésszerű gyakoriságot van 24 óránként.

Az aláíró kulcs adatokat kell érvényesíteni az aláírást, az OpenID Connect metaadat-dokumentumban található használatával szerezheti be:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki az URL-címet egy böngészőben!

Ez a dokumentum metaadatai egy több hasznos információt tartalmaz, például az OpenID Connect hitelesítést végrehajtásához szükséges különböző végpontok helyét tartalmazó JSON-objektumot. 

Ezenkívül tartalmazza a `jwks_uri`, helyét, a jogkivonatok aláírásához használt nyilvános kulcsok készlete révén. A JSON-dokumentum helyén található a `jwks_uri` tartalmazza az összes nyilvános kulcsadatokat idő adott pillanatban használja. Az alkalmazás használhatja a `kid` jogcím a JWT fejlécben, válassza ki, melyik nyilvános kulcs ebben a dokumentumban használt egy adott jogkivonat aláírása. Majd műveleteket hajthat végre a megfelelő nyilvános kulcsot és a jelzett algoritmus használatával aláírás-ellenőrzése.

> [!NOTE]
> Az 1.0-s verziójú végpont adja vissza, mind a `x5t` és `kid` jogcímeket. A `x5t` jogcím hiányzik a v2.0-jogkivonatokat. A v2.0-végpont fűzi hozzá a `kid` jogcím. Továbbítja, javasoljuk, hogy használja a `kid` jogcím a jogkivonat érvényesítéséhez.

Aláírás-ellenőrzés végrehajtása terjed ki a dokumentum - érhetők el számos nyílt forráskódú kódtár segít, ha szükséges.

#### <a name="validating-the-claims"></a>A jogcímek ellenőrzése

Amikor az alkalmazás megkapja egy token (id_tokent a felhasználói bejelentkezés után, vagy egy hozzáférési jogkivonatot a HTTP-kérelemben szereplő tulajdonosi jogkivonattal,), a jogkivonat is végre kell hajtania néhány ellenőrzi a jogcímek ellen. Ezek közé tartozik, de nem korlátozódnak:

* A **célközönség** jogcímet – győződjön meg arról, hogy a jogkivonat szánták, az alkalmazásnak meg kell adni.
* A **nem előtt** és **lejárati idő** jogcímek – ellenőrizze, hogy a jogkivonat nem járt le.
* A **kibocsátó** jogcímet – győződjön meg arról, hogy a jogkivonat valóban adta, az alkalmazás Azure ad-ben.
* A **egyszeri** – a hitelesítési támadások elhárításához.
* és egyebek...

Az alkalmazás végre kell hajtania, azonosító-jogkivonatokat a jogcím ellenőrzések teljes listájáért tekintse meg a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Ezeket a jogcímeket várt értéket részletek szerepelnek az előző [id_token](#id-tokens) szakaszban.

## <a name="token-revocation"></a>A tokenek visszavonása

Frissítési jogkivonatok érvénytelenítve, vagy bármikor, számos okból visszavonva. Ezek két fő kategóriába sorolhatók: időtúllépések és által okozott hibával találkozzanak. 

**Token időtúllépések**

* MaxInactiveTime: Ha a frissítési jogkivonat nem használták a MaxInactiveTime által időn belül, a frissítési jogkivonat már nem érvényes lesz. 
* MaxSessionAge: Ha valami nem az alapértelmezett (csak visszavont) MaxAgeSessionMultiFactor vagy MaxAgeSessionSingleFactor állított be, majd újbóli hitelesítés lesz szükség a beállítása a MaxAgeSession * az idő elteltével. 
* Példák:
  * A bérlő rendelkezik egy 5 napos MaxInactiveTime szabadságon heti hiba történt a felhasználó, és így AAD rendelkezik nem látható a felhasználó új jogkivonat kérést 7 nap. A következő alkalommal, amikor a felhasználó egy új jogkivonatot kér fognak találni a frissítési Token vissza lett vonva, és újra azokat kell adnia a hitelesítő adatait. 
  * A bizalmas alkalmazásokhoz rendelkezik egy MaxAgeSessionSingleFactor 1 nap. Ha egy felhasználó bejelentkezik, hétfőn, és a "frissítő kedd" (miután 25 óra telt el), akkor újra hitelesíteni kell. 

**Visszavont tanúsítványok**

|   | Jelszóalapú cookie-k | Jelszóalapú jogkivonat | A nem jelszó-alapú cookie-k | Nem-jelszó alapján jogkivonat | Bizalmas ügyfél jogkivonat| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
|Jelszó lejárata| Aktív marad|Aktív marad|Aktív marad|Aktív marad|Aktív marad|
|Felhasználó jelszavát| Visszavonva | Visszavonva | Aktív marad|Aktív marad|Aktív marad|
|Felhasználó végrehajtja az SSPR|Visszavonva | Visszavonva | Aktív marad|Aktív marad|Aktív marad|
|Rendszergazdai jelszó alaphelyzetbe állítása|Visszavonva | Visszavonva | Aktív marad|Aktív marad|Aktív marad|
|Felhasználó visszavonja a frissítési biztonsági jogkivonat [PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Visszavonva | Visszavonva |Visszavonva | Visszavonva |Visszavonva | Visszavonva |
|Rendszergazdai a bérlőhöz tartozó összes frissítési biztonsági jogkivonat visszahívja azokat [PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Visszavonva | Visszavonva |Visszavonva | Visszavonva |Visszavonva | Visszavonva |
|[Egyszeri bejelentkezés ki](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code#single-sign-out) a weben | Visszavonva | Aktív marad |Visszavonva | Aktív marad |Aktív marad |Aktív marad |

> [!NOTE]
> Egy "jelszó nem-alapú" Bejelentkezés az egyik, a felhasználó nem írja be a jelszót, hogy álljon a.  A face például használja a Windows Hello, egy FIDO-kulcs vagy egy PIN-kódot. 
>
> A Windows elsődleges frissítési jogkivonat létezik egy ismert probléma.  Ha lekérte a PRT keresztül egy jelszót, és ezután a felhasználó bejelentkezik Hello használatával, azzal nem módosítja az eredeti, a PRT, és azt a rendszer visszavonja, ha a felhasználó megváltoztatja a jelszavát. 

## <a name="sample-tokens"></a>Minta jogkivonatok

Ebben a szakaszban jeleníti meg a mintákat, SAML és a JWT-jogkivonatokat, amely az Azure AD adja vissza. Ezek a minták segítségével láthatja, a jogcímeket a környezetben.

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
Ez a minta elemeit egy tipikus JSON webes jogkivonat (JWT) szerepel az engedélyezési kód engedélyezési folyamatával.
Mellett a jogcímek, az a jogkivonatot tartalmaz egy verziószámot a **ver** és **appidacr**, a hitelesítési környezethez tartozó osztályhivatkozása, ami azt jelzi, hogy az ügyfél hitelesítése. Nyilvános-ügyfél értéke 0. Ha egy ügyfél-azonosító vagy a titkos Ügyfélkód lett megadva, az érték az 1.

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
* Tekintse meg az Azure AD Graph [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) és a [házirend entitás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), tudhat meg többet az Azure AD Graph API-n keresztül jogkivonat élettartama házirend kezelése.
* További információ és példák többek között a mintákat, PowerShell-parancsmagok használatával házirendek kezelése a [konfigurálható jogkivonatok élettartamának, az Azure ad-ben](active-directory-configurable-token-lifetimes.md). 
* Adjon hozzá [egyéni és az opcionális jogcímek](active-directory-optional-claims.md) való az alkalmazás a jogkivonatokat. 
