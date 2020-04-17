---
title: Az Azure Active Directory-ban szereplő módosítás-megszakító hivatkozások
description: Ismerje meg az Azure AD protokollok, amelyek hatással lehetnek az alkalmazás módosításai.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a60b927f7239818b582ffcd85ddb4b7d69594482
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535961"
---
# <a name="whats-new-for-authentication"></a>A hitelesítés újdonságai

>Értesítést kaphat a lap frissítéseiről. Csak add ezt [az URL-t](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS feed olvasó.

A hitelesítési rendszer folyamatosan módosítja és hozzáadja a funkciókat a biztonság és a szabványok megfelelőségének javítása érdekében. Ahhoz, hogy naprakész legyen a legújabb fejleményekkel, ez a cikk a következő részletekkel kapcsolatos információkat tartalmazza:

- A legújabb funkciók
- Ismert problémák
- Protokollváltozások
- Elavult funkciók

> [!TIP]
> Ez az oldal rendszeresen frissül, ezért látogasson el gyakran. Eltérő rendelkezés hiányában ezeket a változtatásokat csak az újonnan nyilvántartásba vett kérelmek esetében vezették be.

## <a name="upcoming-changes"></a>Közelgő változások

Jelenleg nincs beütemezve.  Kérjük, olvassa el alább a termelésben lévő vagy megjelenő módosításokat.

## <a name="march-2020"></a>2020. március

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>A felhasználói jelszavak 256 karakterre korlátozódnak.

**Hatályba lépés dátuma**: 2020.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Protokoll érintett:** Minden felhasználói folyamat.

A 256 karakternél hosszabb jelszóval rendelkező felhasználók, akik közvetlenül bejelentkeznek az Azure AD-be (szemben az összevont IDP-vel, például az ADFS-szel), nem tudnak bejelentkezni 2020.  A rendszergazdák kéréseket kaphatnak a felhasználók jelszavának visszaállításához.

A regisztrációs naplókban a hiba a AADSTS 50052 lesz: InvalidPasswordExceedsMaxLength

Üzenetet:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Kármentesítés:

A felhasználó nem tud bejelentkezni, mert a jelszava meghaladja a megengedett maximális hosszt. A jelszó visszaállításához forduljon a rendszergazdához. Ha az SSPR engedélyezve van a bérlőszámára, az "Elfelejtette a jelszó" hivatkozást követve alaphelyzetbe állíthatja a jelszavát.



## <a name="february-2020"></a>2020. február

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Üres töredékek lesz hozzáfűzve minden HTTP-átirányítás a bejelentkezési végpontról.

**Hatályba lépés dátuma**: 2020.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**A protokoll érintett:** OAuth és OIDC folyamatok, amelyek response_type=query - ez magában foglalja az [engedélyezési kód áramlását](v2-oauth2-auth-code-flow.md) bizonyos esetekben, és az implicit [folyamat](v2-oauth2-implicit-grant-flow.md).

Amikor egy hitelesítési választ küld login.microsoftonline.com egy alkalmazáshttp-átirányításon keresztül, a szolgáltatás hozzáfűzi egy üres töredék a válasz URL-cím.  Ez megakadályozza az átirányítási támadások egy osztályát, mivel biztosítja, hogy a böngésző törölje a hitelesítési kérelemben lévő töredékeket.  Egyetlen alkalmazásnak sem kell függenie ettől a viselkedéstől.


## <a name="august-2019"></a>2019. augusztus

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST formában szemantika lesz érvényesítve szigorúbban - terek és idézetek figyelmen kívül hagyják

**Hatályba lépés dátuma**: 2019.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Érintett protokoll**: Bárhol POST van használva[(ügyfél hitelesítő adatok,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [engedélyezési kód visszaváltása,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow), és [a frissítés token beváltás)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

A 9/2 héttől kezdve a POST metódust használó hitelesítési kérelmek et szigorúbb HTTP-szabványokkal érvényesíti a rendszer.  Pontosabban a szóközök és a dupla idézőjelek (") a továbbiakban nem lesznek eltávolítva a kéreleműrlap-értékekből. Ezek a módosítások várhatóan nem szakítják meg a meglévő ügyfeleket, és biztosítják, hogy az Azure AD-be küldött kérelmeket minden alkalommal megbízhatóan kezelje. A jövőben (lásd fent) azt tervezzük, hogy emellett elutasítja az ismétlődő paramétereket, és figyelmen kívül hagyja az anyagjegyzéket a kérelmeken belül.

Példa:

Ma, `?e=    "f"&g=h` elemzik azonos `?e=f&g=h` - `e`  ==  `f`így .  Ezzel a módosítással most elemzésre kerül, hogy `e`  ==  `    "f"` - ez nem valószínű, hogy érvényes érv lenne, és a kérés most sikertelen lenne.


## <a name="july-2019"></a>2019. július

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Csak egybérlős alkalmazások csak alkalmazásjogkivonatokat bocsát ki a rendszer, ha az ügyfélalkalmazás létezik az erőforrás-bérlőben

**Hatályba lépés dátuma**: 2019.

**Érintett végpontok**: Mind [az 1.0-s,](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) mind [a 2.0-s](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Érintett protokoll**: [Ügyfélhitelesítő adatok (csak alkalmazásjogkivonatokkal)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Egy biztonsági módosítás július 26-án lépett le, amely módosítja a csak alkalmazásjogkivonatok kiadásának módját (az ügyfél hitelesítő adatok megadásával). Korábban az alkalmazások számára lehetővé tették, hogy jogkivonatokat kapjon bármely más alkalmazás hívásához, függetlenül a bérlőben való jelenléttől vagy az adott alkalmazáshoz jóváhagyott szerepköröktől.  Ez a viselkedés frissült, így az erőforrások (más néven webes API-k) beállítása egybérlős (az alapértelmezett), az ügyfélalkalmazás léteznie kell az erőforrás-bérlőn belül.  Vegye figyelembe, hogy az ügyfél és az API közötti meglévő hozzájárulás továbbra sem szükséges, `roles` és az alkalmazásoktovábbra is saját engedélyezési ellenőrzéseket kell végezniük annak érdekében, hogy a jogcím jelen van, és tartalmazza az API várható értékét.

A hibaüzenet ebben a forgatókönyvben jelenleg a következőket mondja:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

A probléma megoldásához használja a rendszergazdai hozzájárulás i felületet az ügyfélalkalmazás egyszerű szolgáltatás a bérlőben, vagy manuálisan hozza létre.  Ez a követelmény biztosítja, hogy a bérlő engedélyt adott az alkalmazásnak a bérlőn belüli működésre.

#### <a name="example-request"></a>Példakérelem

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Ebben a példában az erőforrás-bérlő (hatóság) contoso.com, az erőforrás-alkalmazás egy egybérlős alkalmazás a Contoso-bérlő számára, `gateway.contoso.com/api` és az ügyfélalkalmazás. `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`  Ha az ügyfélalkalmazás Contoso.com belül rendelkezik egy egyszerű szolgáltatással, ez a kérés folytatódhat.  Ha azonban nem, akkor a kérelem sikertelen lesz a fenti hibával.

Ha a Contoso átjáró alkalmazás egy több-bérlős alkalmazás, azonban a kérelem továbbra is függetlenül attól, hogy az ügyfélalkalmazás, amelynek egyszerű szolgáltatás a Contoso.com belül.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Az átirányítási URI-k mostantól tartalmazhatnak lekérdezési karakterlánc-paramétereket

**Hatályba lépés dátuma**: 2019.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Érintett protokoll**: Minden folyamat

[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)szerint az Azure AD-alkalmazások most már regisztrálhatnak és használhatnak `https://contoso.com/oauth2?idp=microsoft`átirányítási (válasz) URI-kat statikus lekérdezési paraméterekkel (például ) az OAuth 2.0-s kérelmekhez.  A dinamikus átirányítási URI-k továbbra is tiltottak, mivel biztonsági kockázatot jelentenek, és ez `state` nem használható az állapotadatok megőrzésére egy hitelesítési kérelemben - ehhez használja a paramétert.

A statikus lekérdezési paraméter az átirányítási URI-k karakterlánc-egyeztetésének hatálya alá tartozik, mint az átirányítási URI bármely más része – ha nincs olyan karakterlánc regisztrálva, amely megfelel az URI-dekódolt redirect_uri, akkor a kérelem elutasításra kerül.  Ha az URI található az alkalmazás regisztrációjában, majd a teljes karakterlánc lesz a felhasználó átirányítására, beleértve a statikus lekérdezési paraméter.

Vegye figyelembe, hogy jelenleg (2019. július vége) az alkalmazás regisztrációs ux az Azure Portalon továbbra is blokkolja a lekérdezési paramétereket.  Az alkalmazásjegyzéket azonban manuálisan is szerkesztheti a lekérdezési paraméterek hozzáadásához és az alkalmazásban való teszteléséhez.


## <a name="march-2019"></a>2019. március

### <a name="looping-clients-will-be-interrupted"></a>Az ügyfelek hurkolása megszakad

**Hatályba lépés dátuma**: 2019. március 25.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Érintett protokoll**: Minden folyamat

Az ügyfélalkalmazások néha rosszul viselkednek, és rövid idő alatt több száz azonos bejelentkezési kérelmet bocsátanak ki.  Ezek a kérések lehetnek sikeresek, de mind hozzájárulnak a gyenge felhasználói élményhez és az IDP fokozott munkaterheléséhez, növelve a késést az összes felhasználó számára, és csökkentve az IDP rendelkezésre állását.  Ezek az alkalmazások a normál használat határain kívül működnek, és frissíteni kell őket, hogy megfelelően viselkedjenek.

Az ismétlődő kéréseket többször kibocsátó `invalid_grant` ügyfelek `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`a következő hibaüzenetet kapják: .

A legtöbb ügyfélnek nem kell módosítania a viselkedést a hiba elkerülése érdekében.  Ez a hiba csak a helytelenül konfigurált ügyfeleket érinti (azokat, amelyek nem rendelkeznek jogkivonat-gyorsítótárazásnélkül, vagy amelyek már rendelkeznek gyorshurkokat).  Az ügyfeleket eseti alapon (cookie-n keresztül) követik nyomon a következő tényezők alapján:

* Felhasználói tipp, ha van ilyen

* A kért hatókörök vagy erőforrások

* Ügyfél-azonosító

* Átirányítási URI

* Válasz típusa és módja

A rövid idő alatt (5 perc) több kérést küldő `invalid_grant` alkalmazások (15+) hibaüzenetet kapnak, amely elmagyarázza, hogy hurkolnak.  A kért jogkivonatok megfelelően hosszú élettartamú (10 perc minimum, 60 perc alapértelmezés szerint), így az ismételt kérelmek ebben az időszakban szükségtelen.

Minden alkalmazásnak `invalid_grant` egy interaktív üzenet megjelenítésével kell kezelnie, ahelyett, hogy csendben kérne egy jogkivonatot.  A hiba elkerülése érdekében az ügyfeleknek biztosítaniuk kell, hogy megfelelően gyorsítótárazzák a kapott jogkivonatokat.


## <a name="october-2018"></a>2018. október

### <a name="authorization-codes-can-no-longer-be-reused"></a>Az engedélyezési kódok már nem használhatók fel újra

**Hatályba lépés dátuma**: 2018.

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Érintett protokoll**: [Kódáramlás](v2-oauth2-auth-code-flow.md)

2018. november 15-től kezdődően az Azure AD leállítja az alkalmazások korábban használt hitelesítési kódjainak fogadását. Ez a biztonsági változás segít abban, hogy az Azure AD összhangba hozza az OAuth specifikációt, és a v1 és a v2 végpontokon is érvénybe lép.

Ha az alkalmazás újrahasználja az engedélyezési kódokat több erőforrás jogkivonatának lekéréséhez, azt javasoljuk, hogy a kód használatával kapjon egy frissítési jogkivonatot, majd használja ezt a frissítési jogkivonatot további jogkivonatok beszerzéséhez más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési jogkivonatok többször is használhatók több erőforrásközött. Minden olyan új alkalmazás, amely az OAuth-kódfolyamat során megpróbálja újra felhasználni a hitelesítési kódot, invalid_grant hibaüzenetet kap.

A frissítési jogkivonatokról [a Hozzáférési jogkivonatok frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token)című témakörben talál további információt.  Ha ADAL vagy MSAL, ez kezeli az Ön számára a könyvtár - cserélje ki a második példányát "AcquireTokenByAuthorizationCodeAsync" a "AcquireTokenSilentAsync".

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Az id tokenek nem használhatók az OBO-folyamathoz

**Dátum**: Május 1, 2018

**Érintett végpontok**: Mind az 1.0-s, mind a 2.0-s

**Érintett protokollok**: Implicit áramlás és [az áramlás nevében](v2-oauth2-on-behalf-of-flow.md)

2018. május 1-je után id_tokens nem használható az új alkalmazások obo-folyamatában az állításként. Hozzáférési jogkivonatokat kell használni, hanem az API-k védelméhez, még egy ügyfél és egy alkalmazás középső rétege között is. a 201 id_tokens8. ez a minta azonban nem tekinthető legjobb gyakorlatnak.

A módosítás kerülő megoldásához tegye a következőket:

1. Hozzon létre egy webes API-t az alkalmazáshoz egy vagy több hatókörrel. Ez az explicit belépési pont lehetővé teszi a finomabb szemcsés ellenőrzést és biztonságot.
1. Az alkalmazás jegyzékfájljában, az [Azure Portalon](https://portal.azure.com) vagy az [alkalmazás regisztrációs portálján](https://apps.dev.microsoft.com)győződjön meg arról, hogy az alkalmazás számára engedélyezett a hozzáférési jogkivonatok kiadása az implicit folyamaton keresztül. Ez a `oauth2AllowImplicitFlow` kulcson keresztül van irányítva.
1. Amikor az ügyfélalkalmazás id_token `response_type=id_token`kér keresztül, hozzáférési`response_type=token`jogkivonatot ( ) is kér a fent létrehozott webes API-hoz. Így a v2.0 végpont használatakor a `scope` paraméternek a . `api://GUID/SCOPE` A v1.0-végponton `resource` a paraméternek a webes API alkalmazás URI-jának kell lennie.
1. Adja át ezt a hozzáférési jogkivonatot a középső rétegnek a id_token helyett.
