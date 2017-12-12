---
title: Az Azure Active Directory v2.0 jogkivonatok referencia |} Microsoft Docs
description: "A jogkivonatok és jogcímek különböző típusairól a Azure AD v2.0-végpontra által kibocsátott"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 01994e067bd7ce0343f12ec3334a91bd062251a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Az Azure Active Directory v2.0 jogkivonatok referenciái
Az Azure Active Directory (Azure AD) v2.0-végponttól számos különböző típusú minden biztonsági jogkivonatokat bocsát ki [hitelesítési folyamat](active-directory-v2-flows.md). Ezt a hivatkozást a formátuma, a biztonsági jellemzőkkel és a különböző típusú lexikális elem tartalmát ismerteti.

> [!NOTE]
> A v2.0-végpontra nem támogatja az összes Azure Active Directory forgatókönyvek és funkciók. Annak megállapításához, hogy a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>A jogkivonatok típusok
A v2.0-végpontra támogatja a [OAuth 2.0 protokoll](active-directory-v2-protocols.md), használó tokenek hozzáférési és frissítési jogkivonatok. A v2.0-végpontra is támogatja a hitelesítést és -bejelentkezés keresztül [OpenID Connect](active-directory-v2-protocols.md). Egy harmadik típusú jogkivonatot, a Azonosítót jogkivonatban OpenID Connect vezet be. Ezeket a jogkivonatokat mindegyikének egy jelenik meg egy *tulajdonosi* token.

Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a tulajdonosi. A tulajdonosi, amely a token is jelenthet félre. Egy entitás fogadására a tulajdonosi jogkivonattal, Azure AD-val kell hitelesítenie, ha nem szükséges a jogkivonat biztonságos átvitel és a tárolás során, habár hozzá, és egy nem kívánt félnek használják. Néhány biztonsági jogkivonatokat rendelkezik beépített eljárást, amely megakadályozhatja, hogy a nem hitelesített felek a őket, de tulajdonosi jogkivonatok azonban nem. Tulajdonosi jogkivonatok kell szállítani, például a transport layer security (HTTPS) biztonságos csatornát. Ha egy tulajdonosi jogkivonatot az ilyen típusú biztonsági nélkül, egy rosszindulatú entitás használhatja a "-átjárójának támadás" jogkivonat, és a védett erőforrásokhoz való jogosulatlan hozzáférést használni. Az azonos biztonsági elveket alkalmazza, ha a tárolást, vagy a gyorsítótárazás tulajdonosi jogkivonatok későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás biztonságos továbbítja a tulajdonosi jogkivonatok tárolja. Tulajdonosi jogkivonatok további biztonsági szempontjait, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).

Nagy része a v2.0-végpontra által kiállított jogkivonatokat használják, mint a JSON Web Tokens (JWTs). A JWT módja a kompakt, a biztonságos URL-cím két fél közötti adatátvitelhez. A jwt-t információk nevezik egy *jogcím*. A tulajdonosi és a token tárgya egy helyességi feltétel is. A rendszer kódolása, és a továbbításra szerializált JavaScript Object Notation (JSON) objektumokat a jogcímeket a jwt-t. A kiállító a v2.0-végpontra JWTs aláírt, de nincs titkosítva, mert könnyen vizsgálhatja meg a jwt-t tartalmát hibakeresési célra. JWTs kapcsolatos további információkért tekintse meg a [JWT specification](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Azonosító-jogkivonatokat
Egy azonosító tokent a bejelentkezési biztonsági jogkivonatot, amely az alkalmazás kapja meg, amikor használatával végez hitelesítést egy formája, amely [OpenID Connect](active-directory-v2-protocols.md). Azonosító-jogkivonatokat helyettesítik [JWTs](#types-of-tokens), és jelentkezzen be a felhasználó az alkalmazás segítségével jogcímeket tartalmaznak. A jogcímek egy Azonosítót jogkivonatban különböző módokon használhatja. Általában a rendszergazdák azonosító-jogkivonatokat használják fiók információk megjelenítéséhez vagy egy alkalmazásban a hozzáférés-vezérlési döntéseket. A v2.0-végpontra azonosító jogkivonatot, amely rendelkezik a jogcímeket, függetlenül attól, milyen típusú, amely a bejelentkezett felhasználó konzisztens csak egyféle típusú problémákkal. A formátum és azonosító-jogkivonatokat tartalmát ugyanazok a személyes Microsoft-fiókot használó felhasználók és a munkahelyi vagy iskolai fiókok.

Jelenleg azonosító-jogkivonatokat írták alá, de nincs titkosítva. Ha az alkalmazás egy azonosító jogkivonatot kap, kell [az aláírás érvényesítése](#validating-tokens) a token hitelességének igazolásához és ellenőrizhesse néhány a token érvényességének igazolásához. A jogcímek, az alkalmazás által érvényesített forgatókönyv követelményeitől függően eltérőek, de az alkalmazás kell végeznie néhány [közös jogcím érvényesítést](#validating-tokens) minden forgatókönyvben.

Ad a jogcímek teljes körű részleteit a minta azonosító jogkivonat mellett a következő szakaszokban azonosító-jogkivonatokat. Vegye figyelembe, hogy a jogcímek az azonosító-jogkivonatokat nem vissza meghatározott sorrendben. Emellett új jogcímeket is lehet bevezetni azonosító-jogkivonatokat bármikor. Az alkalmazás nem kell töréspont, ha új, új jogcímeket. Az alábbi lista tartalmazza az alkalmazás jelenleg is megbízhatóan értelmezhetők jogcímeket. További részletek találhatók a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Minta azonosító tokent
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Az eljárás az, vizsgálja meg a minta azonosító jogkivonatában lévő jogcímeket, illessze be a minta azonosító jogkivonatot [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Azonosító-jogkivonatokat a jogcím
| Név | Jogcím | Példaérték | Leírás |
| --- | --- | --- | --- |
| Célközönség |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |A jogkivonat az illetékes címzett azonosítja. Az azonosító-jogkivonatokat a célközönségét az alkalmazás a Microsoft alkalmazás regisztrációs portálon rendelt az alkalmazás alkalmazás-azonosító. Az alkalmazás kell ellenőrizni az értékét, és utasítsa el a jogkivonatot, ha az érték nem egyezik. |
| Kiállító |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Azonosítja a biztonságijogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot, és az Azure AD-bérlőt, amelyben a felhasználó hitelesítési adja vissza. Az alkalmazás érdemes ellenőrizni a kibocsátó jogcím győződjön meg arról, hogy a jogkivonat származik-e a v2.0-végponttól. Azt is használjon a GUID része a jogcímszabályok bérlőinek, amelyeket az alkalmazás való bejelentkezés korlátozni. A GUID, amely azt jelzi, hogy a felhasználó Microsoft-fiók fogyasztói felhasználó `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Ki: |`iat` |`1452285331` |Az idő, amelynél a jogkivonat adta ki, epoch időt jelöli. |
| Lejárati idő |`exp` |`1452289231` |Az idő, ahol a token érvénytelen, válik epoch időt jelöli. Az alkalmazás által használandó ezt az igényt a jogkivonatok élettartama érvényességének ellenőrzésére. |
| Hatálybalépési idő |`nbf` |`1452285331` |Az idő, amelynél a jogkivonat hatályba lép, epoch időt jelöli. Ez megegyezik általában a kiállítási idő. Az alkalmazás által használandó ezt az igényt a jogkivonatok élettartama érvényességének ellenőrzésére. |
| verzió: |`ver` |`2.0` |Az azonosító jogkivonatot, az Azure AD által definiált verzióját. A v2.0-végpontra értéke `2.0`. |
| Bérlő azonosítója |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Az, hogy a felhasználó az Azure AD-bérlő képviselő GUID. A munkahelyi és iskolai fiókok a GUID-azonosító, amely a felhasználó tagja a szervezet nem módosítható bérlői azonosító. A személyes fiókok értéke `9188040d-6c67-4c5b-b112-36a304b66dad`. A `profile` hatókör ezt az igényt fogadásához szükséges. |
| Kód kivonata |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |A kód kivonat csak akkor, ha a Azonosítót jogkivonatban kiadott OAuth 2.0 hitelesítési kóddal azonosító-jogkivonatokat szerepel. Az engedélyezési kód hitelességének használható. Ezen ellenőrzés végrehajtásával kapcsolatos részletekért lásd: a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hozzáférési jogkivonat kivonata |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |A hozzáférési token kivonatoló azonosító szerepel jogkivonatokat, csak ha az azonosító token kiadott olyan OAuth 2.0 hozzáférési jogkivonatot. A hozzáférési token hitelességének használható. Ezen ellenőrzés végrehajtásával kapcsolatos részletekért lásd: a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html). |
| Nonce |`nonce` |`12345` |Az egyszeri üzenet hitelesítési karakterláncok ismétlésének támadások kiküszöböléséhez stratégiát. Az alkalmazás megadhat egy nonce engedélyezési kérelmet használatával a `nonce` lekérdezési paraméter. Megadja a kérelemben szereplő érték is ki lesz adva a Azonosítót jogkivonatban `nonce` jogcím változtatás nélkül. Az alkalmazás ellenőrizheti, hogy az érték azt a kérelmet, amely összerendeli az alkalmazás munkamenet egyedi azonosítója jogkivonatok megadott értékkel. Az alkalmazás végre kell hajtania az ellenőrzés az azonosító jogkivonatok érvényesség-ellenőrzése során. |
| név |`name` |`Babe Ruth` |A jogcím nevét emberek számára olvasható érték, amely azonosítja a token tárgya biztosít. Az érték nem garantált egyedinek kell lennie, változtatható, és úgy van kialakítva, csak megjelenítési célra használható. A `profile` hatókör ezt az igényt fogadásához szükséges. |
| e-mailben |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Ha létezik egy felhasználói fiókhoz tartozó elsődleges e-mail címe. Az érték változtatható és idővel változhatnak. A `email` hatókör ezt az igényt fogadásához szükséges. |
| előnyben részesített felhasználónév |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Elsődleges felhasználóneve, amely a felhasználót a v2.0-végpontra jelöli. Ez lehet egy e-mail címet, telefonszámot vagy egy általános felhasználónév nélkül a megadott formátumban. Az érték változtatható és idővel változhatnak. Mivel ez változtatható, ez az érték nem használható engedélyezéshez. A `profile` hatókör ezt az igényt fogadásához szükséges. |
| Tulajdonos |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Arról, hogy mely a token állításokat információkat, például egy alkalmazás a felhasználó rendszerbiztonsági tag. Ez az érték nem módosítható és nem lehet újbóli hozzárendelése és nem használja fel újra. Ellenőrzi a engedélyezési biztonságosan, például ha használja a tokent elért egy erőforrást használható, és egy adatbázistáblákban kulcs használható. Mivel a tulajdonos mindig szerepel a jogkivonatokat, hogy az Azure AD-problémák, azt javasoljuk, ez az érték egy általános célú engedélyezési rendszerben. A tulajdonos, azonban egy páros azonosító - egyedi legyen egy adott alkalmazásra.  Ezért ha egy felhasználó bejelentkezik a két különböző alkalmazások két különböző ügyfél-azonosító, az alkalmazások két eltérő értékek tartoznak a tulajdonos jogcím fog kapni.  Ez lehet, hogy vagy a architektúra és adatvédelmi követelményeitől függően nem is kívánatos. |
| objektum azonosítója |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | A Microsoft identity rendszer ebben az esetben egy felhasználói fiókot az objektum nem módosítható azonosítója.  Azt is segítségével biztonságos és kulcsként az adatbázistáblákban levő engedélyezési ellenőrzéseket hajtanak végre. Ezt az Azonosítót egyedileg azonosítja a felhasználó alkalmazásra – két különböző alkalmazások az azonos felhasználói bejelentkezés fog kapni a ugyanazt az értéket a `oid` jogcímek.  Ez azt jelenti, hogy használható Microsoft online szolgáltatások, például a Microsoft Graph lekérdezések létrehozásakor.  A Microsoft Graph ezt az Azonosítót ad vissza a `id` tulajdonság egy adott felhasználói fiók.  Mivel a `oid` lehetővé teszi, hogy a felhasználók, összefüggéseket több alkalmazást a `profile` hatókör ezt az igényt fogadásához szükséges. Vegye figyelembe, hogy ha egy felhasználó több bérlő, a felhasználó fogja tartalmazni az egyes bérlők különböző Objektumazonosító - nek minősíti azokat külön fiókot annak ellenére, hogy a felhasználó bejelentkezik az egyes fiókokhoz azokkal a hitelesítő adatokkal. |

### <a name="access-tokens"></a>Hozzáférési jogkivonatok
Jelenleg csak a Microsoft Services által igénybe vehető a v2.0-végpontra által kibocsátott jogkivonatot. Az alkalmazások végrehajtani az érvényesítés vagy a hozzáférési jogkivonatok vizsgálata a jelenleg támogatott esetek bármelyike nem szükséges. Hozzáférési jogkivonatok nem teljesen átlátszó, is kezelheti. Csak karakterláncok, amelyek az alkalmazás képes továbbadni a Microsoft a HTTP-kérelmek.

A v2.0-végpontra is bevezeti, a közeljövőben, az alkalmazás hozzáférési jogkivonatok az egyéb ügyfelektől fogadott képességét. Idő lejárta után a hivatkozás a témakörben található információk fog frissülni, hogy az alkalmazás hozzáférési jogkivonatok érvényesség-ellenőrzése és más hasonló feladatok elvégzéséhez szükséges információkkal.

A v2.0-végpontra a kér le a hozzáférési tokent, ha a v2.0-végpontra is visszaadja a hozzáférési jogkivonat az alkalmazás használatára vonatkozó metaadatok. Ezen információk közé tartozik a lejárat időpontjának a hozzáférési jogkivonat és a hatókörök, amelyek esetében érvényes. Az alkalmazás intelligens gyorsítótárazás hozzáférési jogkivonatok nem kell elemezni, nyissa meg a hozzáférési jogkivonat maga végrehajtásához ezeket a metaadatokat használja.

### <a name="refresh-tokens"></a>Frissítési jogkivonatok
Frissítési jogkivonatok olyan biztonsági jogkivonatok, amelyek az alkalmazás új hozzáférési jogkivonatok lekérni az OAuth 2.0 folyamatból. Az alkalmazás használhatja a frissítési jogkivonatokat anélkül, hogy a felhasználói hosszú távú hozzáférést egy felhasználó nevében erőforrások eléréséhez.

Frissítési jogkivonatok több erőforrás. A frissítési token során egy erőforrást a kérés érkezett is váltható a hozzáférési jogkivonatok egy teljesen más erőforráshoz.

A frissítési token választ kap, az alkalmazás kell igényelnie, és biztosítani a `offline_acesss` hatókör. További részletek a `offline_access` hatókörét, tekintse meg a [hozzájárulási és hatókörök](active-directory-v2-scopes.md) cikk.

Frissítési jogkivonatok, és mindig lesz, az alkalmazás teljesen átlátszó. Ezek az Azure AD v2.0-végponttól által kiállított és csak felügyelete és a v2.0-végpontra értelmezi. Hosszú élettartamú, de az alkalmazás nem írható várható, hogy egy frissítési jogkivonat bármely ideig tart. Frissítési jogkivonatokat különböző okokból bármikor érvénytelenített lehet. Csak az alkalmazás tudja, hogy a frissítési jogkivonat érvényes, beváltani azt kérelmek, így a v2.0-végpontra kísérletet.

Amikor új tokenre egy frissítési token beváltja (ha megadták az alkalmazás és a `offline_access` hatókör), kap egy új frissítési jogkivonat a biztonságijogkivonat-válaszban. Mentse az újonnan kiadott frissítési jogkivonat a kérésben használt helyett. Ez biztosítja, hogy, hogy a frissítési jogkivonatokat maradnak, amíg érvényes.

## <a name="validating-tokens"></a>Jogkivonatok ellenőrzése
Jelenleg az alkalmazások kell elvégezni a csak jogkivonat érvényesítésére érvényesítése azonosító-jogkivonatokat. Egy azonosító tokent ellenőrzéséhez az alkalmazás érdemes ellenőrizni a azonosító jogkivonat aláírása, mind a Azonosítót jogkivonatban a jogcím.

<!-- TODO: Link -->
A Microsoft biztosít a könyvtárak és kódpéldák, amelyek bemutatják a könnyen kezelni a jogkivonatok érvényesség-ellenőrzése. A következő szakaszok azt ismertetik az alapul szolgáló folyamat. Több külső nyílt forráskódú kódtárai is elérhetők a JWT-ellenőrzéshez. Legalább egy tárat szinte minden platform és nyelvi beállítás van.

### <a name="validate-the-signature"></a>Az aláírás érvényesítése
A jwt-t tartalmazza az három, az elemek elválasztására pedig a `.` karakter. Az első szegmensnek is ismert, a *fejléc*, a második szegmens pedig az a *törzs*, és a harmadik szegmens pedig az *aláírás*. Az aláírás szegmens segítségével hitelességének a Azonosítót jogkivonatban, hogy az alkalmazás által megbízhatóak.

Azonosító-jogkivonatokat aláírt szabványos aszimmetrikus titkosítási algoritmusok, például RSA 256. Az azonosító token fejléce rendelkezik a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat. Példa:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

A `alg` jogcím jelzi a token aláírásához használt algoritmust. A `kid` jogcím jelzi a token aláírásához használt nyilvános kulcsot.

Bármikor a v2.0-végpontra előfordulhat, hogy használatával írja alá az azonosító tokent egy meghatározott nyilvános-titkos kulcspárok valamelyikét. A v2.0-végpontra rendszeresen forog kulcsokat, a lehetséges készletét, így az alkalmazás kezelni azokat automatikusan kell írni. A v2.0-végpontra által használt nyilvános kulcsok frissítések kereséséhez ésszerű gyakoriság van 24 óránként.

Az aláíró fontos adatot, amelyekre szüksége van az aláírás érvényesítése az OpenID Connect metaadat-dokumentum található segítségével szerezheti be:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálkozzon a böngésző URL-címet!
>
>

A metaadat-dokumentum egy JSON-objektum, amely rendelkezik a számos hasznos információt tartalmaz, például a különböző végpontok az OpenID Connect hitelesítéshez szükséges hely.  A dokumentum is magában foglalja a *jwks_uri*, ennek révén a jogkivonatok aláírásához használt nyilvános kulcsok készlete helyét. A JSON-dokumentum található, a jwks_uri minden a nyilvánoskulcs-adatokat, amelyek jelenleg használatban van. Az alkalmazás használhatja a `kid` jogcím válassza ki, melyik nyilvános kulcs ebben a dokumentumban már használta a jogkivonat aláírása a JWT-fejlécben. Majd az aláírás érvényesítése és segítségével hajtja végre a megfelelő nyilvános kulcsot a jelzett algoritmus.

Aláírás-ellenőrzés végrehajtása van ez a dokumentum nem terjed. Számos nyílt forráskódú kódtárai ennek segítségével érhetők el.

### <a name="validate-the-claims"></a>A jogcímek ellenőrzése
Ha az alkalmazás felhasználói bejelentkezés után egy azonosító jogkivonatot kap, azt kell is ellenőrzi a néhány szemben a Azonosítót jogkivonatban. Ezek közé tartozik, de nincsenek korlátozva:

* **a célközönség** jogcím, győződjön meg arról, hogy az azonosító jogkivonatot kell fordítani az alkalmazás szándéka
* **hatálybalépési idő** és **lejárati idő** jogcímeket, győződjön meg arról, hogy az azonosító jogkivonat nem járt
* **kibocsátó** jogcím, győződjön meg arról, hogy a jogkivonat kiállító az alkalmazáshoz a v2.0-végpontra
* **NONCE**, jogkivonatként visszajátszásos támadások megoldás

Jogcím-érvényesítést, végre kell hajtania, az alkalmazás teljes listáját lásd: a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Ezeket a jogcímeket a várt értékek részletek szerepelnek a [azonosító-jogkivonatokat](# ID tokens) szakasz.

## <a name="token-lifetimes"></a>Token élettartama
A következő token élettartama csak tájékoztatási nyújtunk. Az információk segíthetnek fejlesztése és alkalmazások hibakeresését. Az alkalmazások történik, ezek állandó maradjon élettartama bármelyikét nem kell írni. A token élettartama is, és bármikor változik.

| Token | Élettartam | Leírás |
| --- | --- | --- |
| Azonosító-jogkivonatokat (munkahelyi vagy iskolai fiókok) |1 óra |Azonosító-jogkivonatokat általában érvényesek 1 óra. A webalkalmazás használhatja ez ugyanazt az élettartam (ajánlott) a felhasználó a saját munkamenet fenntartásához, vagy választhat egy teljesen más munkamenetek élettartamát. Ha az alkalmazás egy új ID-token beszerzése, kell új bejelentkezési kérés a v2.0 a végpont hitelesítéséhez. Ha a felhasználó egy érvényes böngésző-munkamenetet a v2.0-végponttal rendelkezik, a felhasználó előfordulhat, hogy nem kell írja be újra a hitelesítő adataikat. |
| Azonosító-jogkivonatokat (személyes fiókok) |24 óra |A személyes fiókok azonosító-jogkivonatokat általában érvényes 24 órán át. A webalkalmazás használhatja ez ugyanazt az élettartam (ajánlott) a felhasználó a saját munkamenet fenntartásához, vagy választhat egy teljesen más munkamenetek élettartamát. Ha az alkalmazás egy új ID-token beszerzése, kell új bejelentkezési kérés a v2.0 a végpont hitelesítéséhez. Ha a felhasználó egy érvényes böngésző-munkamenetet a v2.0-végponttal rendelkezik, a felhasználó előfordulhat, hogy nem kell írja be újra a hitelesítő adataikat. |
| Hozzáférési jogkivonatok (munkahelyi vagy iskolai fiókok) |1 óra |A token metaadatok részeként token válaszok jelzett. |
| Access tokens (személyes fiókok) |1 óra |A token metaadatok részeként token válaszok jelzett. Hozzáférés kiadott jogkivonatokat, személyes fiókok nevében konfigurálhatja a különböző élettartamot, de tipikus 1 óra. |
| Frissítési jogkivonatok (munkahelyi vagy iskolai fiókkal) |Legfeljebb 14 nap |Egyetlen frissítési jogkivonat esetén legfeljebb 14 napig érvényes. Azonban a frissítési jogkivonat érvénytelenné válhatnak különböző okokból bármikor, az alkalmazás továbbra is egy frissítési jogkivonat használatakor, amíg nem sikerül, vagy az alkalmazás egy új frissítési jogkivonat lecseréli azt. A frissítési jogkivonat is lesz érvénytelen, ha azt óta 90 nappal a felhasználó megadott hitelesítő adataikat. |
| Frissítési jogkivonatok (személyes fiókok) |Legfeljebb 1 év |Egyetlen frissítési jogkivonat esetén legfeljebb 1 évig érvényes. Azonban a frissítési jogkivonat érvénytelenné válhatnak különböző okokból bármikor, az alkalmazás továbbra is egy frissítési jogkivonat használatakor, amíg meg nem hiúsul. |
| Hitelesítési kódok (munkahelyi vagy iskolai fiókok) |10 perc |Hitelesítési kódok szándékosan rövid életű, és kell azonnal váltható a hozzáférési jogkivonatok és a frissítési jogkivonatokat a jogkivonatok fogadásakor. |
| Hitelesítési kódok (személyes fiókok) |5 perc |Hitelesítési kódok szándékosan rövid életű, és kell azonnal váltható a hozzáférési jogkivonatok és a frissítési jogkivonatokat a jogkivonatok fogadásakor. Hitelesítési kódok kiállított személyes fiókok nevében egyszeri használatra szolgálnak. |
