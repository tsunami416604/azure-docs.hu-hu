---
title: Az Azure Active Directory 2.0-s verziójú jogkivonatok hivatkozás |} A Microsoft Docs
description: A jogkivonatok és jogcímek, az Azure AD v2.0-végpont által kibocsátott típusú
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1b0db9ad6d72268ff6074ab2b21026cc04758fbd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504192"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Az Azure Active Directory 2.0-s verziójú jogkivonatok leírása
Az Azure Active Directory (Azure AD) v2.0-végpont bocsát ki biztonsági jogkivonatokat az egyes számos különböző típusú [hitelesítési folyamat](active-directory-v2-flows.md). Ez a hivatkozás formátuma, biztonsági jellemzőkkel és különböző típusú jogkivonat tartalma ismerteti.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Jogkivonatok típusai
A v2.0-végpont támogatja a [OAuth 2.0 engedélyezési protokollt](active-directory-v2-protocols.md), mely használt hozzáférési jogkivonatokat és frissítési jogkivonatok. A v2.0-végpontra is támogatja a hitelesítést és a bejelentkezési keresztül [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect vezet be a jogkivonatot, az azonosító jogkivonat egy harmadik típusú. Mindegyik token jelenik meg egy *tulajdonosi* token.

A tulajdonosi jogkivonatot, amely hozzáférést biztosít a tulajdonosi védett erőforrásokhoz való könnyű biztonsági jogkivonat. A tulajdonosi minden olyan entitás, amely a token is jelenthet. Egy entitás fogadni a tulajdonosi jogkivonattal, Azure AD-vel kell hitelesítenie, ha a lépéseket a rendszer nem hajtja végre a jogkivonat biztonságos átvitelét és tárolását során, bár hozzá, és egy nem kívánt entitás használják. Néhány biztonsági jogkivonatokat rendelkezik egy beépített mechanizmus, amellyel megakadályozza, hogy illetéktelen fél használja őket, de ne tulajdonosi jogkivonatokat. Egy biztonságos csatornát, például a transport layer security (HTTPS) kell szállítani, tulajdonosi jogkivonatokat. Egy tulajdonosi jogkivonat nélkül az ilyen típusú biztonsági átvitt, ha egy rosszindulatú fél használhat egy "man-in-the-middle támadás" beszerezni a jogkivonatot, és azt a védett erőforrásokhoz való jogosulatlan hozzáférést. A biztonsági alapelveket alkalmazható, ha a tárolásával, sem a gyorsítótárazás a tulajdonosi jogkivonatokat későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja a tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](http://tools.ietf.org/html/rfc6750).

Számos, a v2.0-végpont által kiállított jogkivonatokat használják, mint a JSON webes jogkivonatainak (JWTs). A JWT módja a tömör, URL-cím környezetben is biztonságos két fél közötti adatátvitelhez. Az adatokat a jwt-t hívja a *jogcím*. Egy helyességi feltétel a tulajdonosi és a jogkivonat tárgyában. A jogcímeket a jwt-t olyan JavaScript Object Notation (JSON) objektumok, kódolt és szerializálni számára az átvitelhez. A v2.0-végpont által kiállított JWTs jelentkezett, de nincs titkosítva, mert könnyen vizsgálhatja meg a jwt-t tartalmát hibakeresési célra. JWTs kapcsolatos további információkért lásd: a [JWT-specifikáció](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Azonosító-jogkivonatokat
Egy azonosító jogkivonat egy biztonsági jogkivonatot, amely az alkalmazás fogad a használatával végzett hitelesítés formája [OpenID Connect](active-directory-v2-protocols.md). Azonosító-jogkivonatokat jelentésekként jelennek meg [JWTs](#types-of-tokens), és használhatja a bejelentkezni a felhasználó az alkalmazás jogcímeket tartalmaznak. Egy azonosító jogkivonat többféle módon is használhatja a jogcímeket. Általában segítségével a rendszergazdák azonosító-jogkivonatokat fiók információk megjelenítéséhez vagy egy alkalmazásban a döntést hozhasson a hozzáférésről. A v2.0-végpont problémák azonosító jogkivonat, amely rendelkezik a konzisztens jogcímeket, függetlenül attól, hogy a bejelentkezett felhasználó típusa csak egyféle típusú. A formátum és a tartalom azonosító-jogkivonatokat ugyanazok a személyes Microsoft-fiókok felhasználóinak és a munkahelyi vagy iskolai fiókok esetében.

Jelenleg azonosító-jogkivonatokat jelentkezett, de nem titkosított. Amikor az alkalmazás megkapja egy azonosító jogkivonat, kell [érvényesíteni az aláírást](#validating-tokens) a token hitelességének igazolásához, és ellenőrizni annak érvényességét igazolásához néhány jogcímeket. A jogcímek, az alkalmazás által ellenőrzött forgatókönyv követelményeitől függően eltérőek lehetnek, de az alkalmazás kell végeznie néhány [közös jogcím ellenőrzések](#validating-tokens) minden forgatókönyvben.

Is biztosítunk a jogcímekre vonatkozó részletes információk az azonosító-jogkivonatokat egy mintául szolgáló azonosító jogkivonat mellett a következő szakaszokban. Vegye figyelembe, hogy az azonosító-jogkivonatokat a jogcím nem meghatározott sorrendben adja vissza. Emellett új jogcímeket is léptethetők be azonosító-jogkivonatokat bármikor. Az alkalmazás nem kell szünet, amikor új jogcímeket jelennek meg. Az alábbi lista tartalmazza a jogcímeket, amelyeket az alkalmazás jelenleg is megbízhatóan értelmezni. További részleteket talál a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Minta azonosító jogkivonat
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> A gyakorlatban vizsgálhatja meg a minta azonosító jogkivonat jogcímeiben, illessze be a a mintául szolgáló azonosító jogkivonatot [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Azonosító-jogkivonatokat a jogcím
| Name (Név) | Jogcím | Példaérték | Leírás |
| --- | --- | --- | --- |
| Célközönség |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |A jogkivonat az illetékes címzett azonosítja. Az azonosító tokenekhez a célközönségét az alkalmazás Alkalmazásazonosító, az alkalmazás a Microsoft alkalmazás regisztrációs portálon rendelt. Az alkalmazás kell érvényesíteni ezt az értéket, és elutasítja a tokent, ha az érték nem egyezik. |
| Kiállító |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot, és az Azure AD-bérlővel, amelyben a felhasználó hitelesítési adja vissza. Az alkalmazás ellenőrizni kell a kibocsátó jogcím győződjön meg arról, hogy a jogkivonat érkezett a v2.0-végpontra. Is kell használnia a jogcím GUID részének korlátozhatja azon bérlők számára, hogy az alkalmazás jelentkezhetnek be. A GUID, amely azt jelzi, hogy a felhasználó Microsoft-fiók fogyasztói felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Tulajdonos: |`iat` |`1452285331` |Az idő, amikor a jogkivonat lett kiállítva, jelöli, alapidőpont szerint. |
| Lejárati idő |`exp` |`1452289231` |Az idő, amikor a jogkivonat érvénytelen, válik jelöli, alapidőpont szerint. Az alkalmazás használjon ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| Nem előtt |`nbf` |`1452285331` |Az idő, amikor a jogkivonat érvényes, válik jelöli, alapidőpont szerint. Ez általában a ugyanaz, mint a kiadási idő. Az alkalmazás használjon ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| verzió: |`ver` |`2.0` |Az azonosító jogkivonat, az Azure AD által meghatározott verziója. A v2.0-végpont értéke `2.0`. |
| bérlőazonosító |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Egy GUID jelöli, hogy a felhasználó Azure AD-bérlővel. Munkahelyi és iskolai fiókok esetében a GUID azonosító a szervezet, amely a felhasználó tartozik Bérlőazonosítója nem módosítható. Személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| Kód kivonata |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |A kód kivonat csak akkor, ha az azonosító jogkivonat, az OAuth 2.0 engedélyezési kóddal kiadott azonosító-jogkivonatokat szerepel. Az engedélyezési kódot hitelességének ellenőrzéséhez használható. További ellenőrzés elvégzésével kapcsolatos információkért lásd: a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hozzáférési jogkivonat kivonata |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Jogkivonat kivonata azonosító szerepel a hozzáférési jogkivonatok, csak ha az azonosító jogkivonat kiadott egy OAuth 2.0 hozzáférési jogkivonatban. Hozzáférési jogkivonat hitelességének ellenőrzéséhez használható. További ellenőrzés elvégzésével kapcsolatos információkért lásd: a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html). |
| egyszeri |`nonce` |`12345` |Az egyszeri problémák enyhítését célzó támadások ismétlésének stratégiát is. Az alkalmazás is megadhat egy egyszeri egy engedélyezési kérésben a `nonce` lekérdezési paraméter. Az értéknek a kérésben bocsásson ki az azonosító jogkivonat `nonce` jogcím, kívánja módosítani. Az alkalmazás ellenőrizheti az érték azt a kérést, amely az alkalmazás munkamenet az egy adott azonosító jogkivonat társítja a megadott értékkel. Az alkalmazás végre kell hajtania az ellenőrzés az azonosító jogkivonat érvényesítése során. |
| név |`name` |`Babe Ruth` |A jogcím nevét itt emberek számára olvasható érték, amely azonosítja a jogkivonat tárgyában. Az érték nem garantált, hogy egyedi legyen, ezekre kapott válaszokon, és úgy tervezték, hogy csak megjelenítési célokra használható. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| e-mailben |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Ha van ilyen, a felhasználói fiók társított elsődleges e-mail címét. Az érték mutable és idővel változhatnak. A `email` hatókör szükséges, ezt a kérelmet kap. |
| előnyben részesített felhasználónév |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Az elsődleges felhasználónév, amely a felhasználót a v2.0-végpont jelöli. Ez lehet egy e-mail-címet, telefonszámot vagy egy általános felhasználónév nélkül egy megadott formátumban. Az érték mutable és idővel változhatnak. Mivel ezekre kapott válaszokon, ez az érték nem használható az engedélyezési döntésekhez. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| Tulajdonos |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például a felhasználó az alkalmazás. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Ez az engedélyezési ellenőrzéséhez biztonságosan, például amikor a jogkivonat-erőforrás eléréséhez használható, és adatbázistáblák kulcsként használhatók. Mivel a tulajdonos mindig szerepel a jogkivonatokat, hogy az Azure AD-problémák, azt javasoljuk, ez az érték egy általános célú engedélyezési rendszerben. Az a tulajdonos, azonban a páros azonosító – egyedi legyen egy adott alkalmazás azonosítója. Ezért ha egy felhasználó bejelentkezik a két különböző ügyfél-azonosító használatával két különböző alkalmazásokat, ezeket az alkalmazásokat két különböző értékeket, a tulajdonos jogcím fog kapni. Ez előfordulhat, hogy, vagy előfordulhat, hogy nem megfelelő az architektúra és adatvédelmi követelményeitől függően. |
| Objektumazonosító: |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | A Microsoft identity rendszeren, ebben az esetben egy felhasználói fiókot egy objektum nem módosítható azonosítója. Is használható hitelesítési ellenőrzések elvégzéséhez biztonságosan és a egy kulcsként adatbázistáblák. Alkalmazások közötti egyedileg azonosítja a felhasználó ezt az Azonosítót – ugyanaz a felhasználó bejelentkezik, két különböző alkalmazás ugyanazt az értéket a fog kapni a `oid` jogcím. Ez azt jelenti, hogy használható a Microsoft online szolgáltatásaihoz, például a Microsoft Graph lekérdezések létrehozásakor. A Microsoft Graph ezt az Azonosítót ad vissza a `id` tulajdonság egy adott felhasználói fiókhoz. Mivel a `oid` korrelációját, ha a felhasználók, több alkalmazás lehetővé teszi a `profile` hatókör szükséges, ezt a kérelmet kap. Vegye figyelembe, hogy ha egy felhasználó több bérlő már létezik, a felhasználó fogja tartalmazni az egyes bérlők egy másik objektum azonosítója – a különböző fiókok, annak ellenére, hogy a felhasználó bejelentkezik az egyes ugyanazokkal a hitelesítő adatokkal rendelkező fiók minősülnek. |

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

A v2.0-végpont lehetővé teszi, hogy a harmadik féltől származó alkalmazások, amelyek az Azure ad-vel védett erőforrások, például webes API-k számára a hozzáférési jogkivonatok kiállításához. Egy alkalmazás hozzáférési jogkivonatok kiállításához beállításával kapcsolatos további információkért tekintse meg [alkalmazás regisztrálása a v2.0-végponttal](quickstart-v2-register-an-app.md). Az alkalmazás a v2.0-végpontra való regisztráláskor a fejlesztő adhatja meg nevű, hozzáférési szintet **hatókörök**, amelyhez hozzáférési jogkivonatok bocsáthat. Ha például a **calendars.read** a Microsoft Graph API-ban definiált hatókörhöz, amely engedélyezi a felhasználó naptár olvasásához szükséges engedély. Ha az alkalmazás egy hozzáférési jogkivonatot kap a v2.0-végpont, ellenőrizni kell a jogkivonat aláírása, kibocsátó, célközönség, lejárati ideje és bármely más jogcímet a forgatókönyvtől függően. 

Ha egy hozzáférési jogkivonatot kér a v2.0-végpont, a v2.0-végpontra, az is metaadatait, az alkalmazások a hozzáférési jogkivonatát adja vissza. Ezen információk közé tartozik a hozzáférési jogkivonatot, és a hatóköröket, amelyek esetében érvényes lejárati idejét. Az alkalmazás ezeket a metaadatokat használja intelligens gyorsítótárazás hozzáférési jogkivonatok, nem kell elemezni, nyissa meg a hozzáférési jogkivonat magát.

### <a name="refresh-tokens"></a>Frissítési jogkivonatok
Frissítési jogkivonatok olyan biztonsági jogkivonatok, amelyek az alkalmazás használatával új hozzáférési jogkivonatok lépjen be az OAuth 2.0 folyamat. Az alkalmazás frissítési biztonsági jogkivonat segítségével anélkül, hogy a felhasználói hosszú távú egy felhasználó nevében erőforrásokhoz való hozzáférés érdekében.

Frissítési biztonsági jogkivonat több erőforrást is. A frissítési jogkivonatot kapott egy erőforráshoz jogkivonatkérések közben váltható hozzáférési jogkivonatok egy teljesen más erőforráshoz.

Kell igényelnie az alkalmazás egy frissítési jogkivonat választ kap, és biztosítani a `offline_access` hatókör. További információkat talál a `offline_access` hatókörét, tekintse meg a [beleegyezése és hatókörök](active-directory-v2-scopes.md) cikk.

Frissítési jogkivonatok, és mindig, teljesen átlátszatlan, az alkalmazáshoz. Azok az Azure AD v2.0-végpont által kiállított, és csak kell megvizsgálni és értelmezi a v2.0-végpontra. Hosszú élettartamú, azonban az alkalmazás várható, hogy a frissítési jogkivonatok minden időszak, amíg a rendszer utolsó nem kell írni. Lehet, hogy a frissítési biztonsági jogkivonat érvénytelenített bármely pillanatban különböző okok miatt – további információkért lásd: [jogkivonat-visszavonási](active-directory-token-and-claims.md#token-revocation). Az egyetlen módszer az alkalmazás tudja, hogy ha egy frissítési jogkivonat érvényes, hogy egy jogkivonat kérése a v2.0-végpont, így a beváltáshoz kísérlet.

Amikor egy új hozzáférési jogkivonatot a frissítési jogkivonat beváltása (és ha megadták az alkalmazás a `offline_access` hatókör), egy új frissítési jogkivonatot a jogkivonat választ kap. Mentse az újonnan kiállított frissítési jogkivonat, amelyet a kérés helyett. Ez garantálja, hogy a frissítési biztonsági jogkivonat érvényes, amíg maradnak.

## <a name="validating-tokens"></a>Token ellenőrzése
Jelenleg a csak jogkivonat érvényesítésére, az alkalmazások végrehajtásához szükséges azonosító-jogkivonatokat érvényesíti. Egy azonosító jogkivonat érvényesítéséhez, az alkalmazás az azonosító jogkivonat aláírása és az azonosító jogkivonat jogcímeiben is ellenőrizni kell.

<!-- TODO: Link --> A Microsoft biztosít, kódtárak és kódmintákkal, amelyek bemutatják, hogyan könnyedén megfelelhet a jogkivonat érvényesítésére. A következő szakaszban ismertetünk, az alapul szolgáló folyamat. Számos harmadik fél nyílt forráskódú könyvtáraink is érhetők el JWT-ellenőrzés céljából. Szinte minden platform és nyelv legalább egy tárat lehetőség van.

### <a name="validate-the-signature"></a>Az aláírás ellenőrzése
Jwt-t tartalmaz három szegmensek, amelyeket a `.` karakter. Az első szegmens más néven a *fejléc*, a második szegmens a *törzs*, és a harmadik szegmens pedig az a *aláírás*. Az aláírás-szegmens az azonosító jogkivonat hitelességének ellenőrzéséhez, hogy az alkalmazás által is megbízhatónak használható.

Azonosító-jogkivonatokat az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például RSA 256 jelentkezett. Az azonosító jogkivonat fejlécében a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmaz. Példa:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

A `alg` jogcím azt jelzi, hogy a jogkivonat aláírásához használt algoritmust. A `kid` jogcím azt jelzi, hogy a jogkivonat aláírásához használt nyilvános kulcsot.

A v2.0-végpont azonosítója és a hozzáférési jogkivonatokban nyilvános-titkos kulcspárok egy meghatározott készletének egyik használatával jelentkezik. A v2.0-végpont rendszeres időközönként forog kulcsokat, a lehetséges készletét, így az alkalmazás automatikusan kezeli a fő módosításokat kell írni. A v2.0-végpont által használt nyilvános kulcsok a frissítések kereséséhez ésszerű gyakoriságot van 24 óránként.

Az aláíró kulcs adatokat kell érvényesíteni az aláírást, az OpenID Connect metaadat-dokumentumban található használatával szerezheti be:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki az URL-CÍMÉT egy böngészőben!

Ez a dokumentum metaadatait a JSON-objektum, amely rendelkezik a számos hasznos információt tartalmaz, például az OpenID Connect-hitelesítéshez szükséges különböző végpontok helyét. A dokumentum is tartalmaz egy *jwks_uri*, helyét, a jogkivonatok aláírásához használt nyilvános kulcsok készlete révén. A jwks_uri helyén található a JSON-dokumentum összes a nyilvánoskulcs-adatait, amely jelenleg használatban van. Az alkalmazás használhatja a `kid` jogcím a JWT-fejlécben válassza ki, melyik nyilvános kulcs ebben a dokumentumban használt jogkivonat aláírása. Majd aláírás-ellenőrzése és segítségével hajtja végre a megfelelő nyilvános kulcsot a jelzett algoritmus.

> [!NOTE]
> A `x5t` jogcím a v2.0-végpont már elavult. Javasoljuk, hogy használja a `kid` jogcím a jogkivonat érvényesítéséhez.

Aláírás-ellenőrzés végrehajtása ebben a dokumentumban hatókörén kívül esik. Számos nyílt forráskódú kódtár segítségével a érhetők el.

### <a name="validate-the-claims"></a>A jogcímek ellenőrzése
Ha az alkalmazás-azonosító jogkivonat felhasználói bejelentkezés után kap, azt az azonosító jogkivonat is végre kell hajtania néhány ellenőrzi a jogcímek ellen. Ezek közé tartozik, de nem korlátozódnak:

* **célközönség** jogcímet, győződjön meg arról, hogy az azonosító jogkivonat kell fordítani az alkalmazás szándéka
* **nem előtt** és **lejárati idő** jogcímekre, győződjön meg arról, hogy az azonosító jogkivonat nem járt le
* **kiállító** jogcímet, győződjön meg arról, hogy a jogkivonat bocsátotta az alkalmazáshoz a v2.0-végpont
* **egyszeri**, jogkivonatként visszajátszani elleni támadások kockázatait

Jogcím-ellenőrzések, végre kell hajtania, az alkalmazás teljes listáját lásd: a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Ezeket a jogcímeket várt értéket részletek szerepelnek a [azonosító-jogkivonatokat](# ID tokens) szakaszban.

## <a name="token-lifetimes"></a>A jogkivonatok élettartama
Az információk csak a következő jogkivonatok élettartamának biztosítunk. Az információk segíthetnek fejlesztése és hibakeresése alkalmazásokat. Az alkalmazások nem kell írni állandó maradjon ezen élettartam bármelyikét várható. Token a jogkivonatok élettartamának is, és bármikor változik.

| Jogkivonat | Élettartam | Leírás |
| --- | --- | --- |
| Azonosító-jogkivonatokat (munkahelyi vagy iskolai fiókok esetében) |1 óra |ID jogkivonatok általában olyan érvényes 1 órán keresztül. A webalkalmazás Ez ugyanaz az élettartam használatával saját munkamenetben fenntartható, mivel a felhasználó (ajánlott), vagy választhat egy teljesen más munkamenet élettartamára. Ha az alkalmazás egy új azonosító jogkivonat beszerzése, kell ellenőriznie az új bejelentkezési kérelem a 2.0-s verziójú authorize végponton. Ha a felhasználó rendelkezik egy érvényes böngésző-munkamenetet a v2.0-végponttal, a felhasználó előfordulhat, hogy nem szükséges újra adja meg a hitelesítő adataikat. |
| ID token (személyes fiókok esetében) |24 óra |ID jogkivonatok személyes fiókok általában olyan érvényes 24 órán keresztül. A webalkalmazás Ez ugyanaz az élettartam használatával saját munkamenetben fenntartható, mivel a felhasználó (ajánlott), vagy választhat egy teljesen más munkamenet élettartamára. Ha az alkalmazás egy új azonosító jogkivonat beszerzése, kell ellenőriznie az új bejelentkezési kérelem a 2.0-s verziójú authorize végponton. Ha a felhasználó rendelkezik egy érvényes böngésző-munkamenetet a v2.0-végponttal, a felhasználó előfordulhat, hogy nem szükséges újra adja meg a hitelesítő adataikat. |
| Hozzáférési jogkivonatok (munkahelyi vagy iskolai fiókok esetében) |1 óra |A token metaadatainak részeként jogkivonatválaszok feltüntetve. |
| Hozzáférési jogkivonatok (személyes fiókok esetében) |1 óra |A token metaadatainak részeként jogkivonatválaszok feltüntetve. Hozzáférési jogkivonatok, amelyek a személyes fiókok nevében kibocsátott konfigurálható egy másik élettartamának, de 1 órára jellemző. |
| Frissítési jogkivonatok (munkahelyi vagy iskolai fiók) |Akár 14 nap |A egyetlen frissítési jogkivonatok esetében legfeljebb 14 napig érvényes. Azonban a frissítési jogkivonat érvénytelenné válhatnak különböző okok miatt bármikor, az alkalmazás továbbra is próbálja használni a frissítési jogkivonatok, amíg nem sikerül, vagy az alkalmazás egy új frissítési jogkivonat lecseréli azt. A frissítési jogkivonatok is lesz érvénytelen, ha 90 napig volt, mert a felhasználó megadott hitelesítő adataikat. |
| Frissítési jogkivonatok (személyes fiókok esetében) |Legfeljebb 1 év |Egy egyetlen frissítési token legfeljebb 1 évig érvényes. Azonban a frissítési jogkivonat érvénytelenné válhatnak különböző okok miatt bármikor, az alkalmazás továbbra is próbálja használni a frissítési jogkivonatok, amíg nem sikerül. |
| Hitelesítési kódok (munkahelyi vagy iskolai fiókok esetében) |10 perc |Engedélyezési kódokat szándékosan rövid életű, és érdemes azonnal beváltani a hozzáférési és frissítési biztonsági jogkivonat a jogkivonatok fogadásakor. |
| Hitelesítési kódok (személyes fiókok esetében) |5 perc |Engedélyezési kódokat szándékosan rövid életű, és érdemes azonnal beváltani a hozzáférési és frissítési biztonsági jogkivonat a jogkivonatok fogadásakor. Engedélyezési kódokat kiállított személyes fiókok nevében egyszeri használatra szolgálnak. |
