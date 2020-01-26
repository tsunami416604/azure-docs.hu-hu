---
title: Azure Active Directory feltörési változások referenciája | Microsoft Docs
description: Ismerje meg az Azure AD protokollok változásait, amelyek hatással lehetnek az alkalmazásra.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 15293f6cf5ceafda2dd5727ad85804b432bae54a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758750"
---
# <a name="whats-new-for-authentication"></a>A hitelesítés újdonságai 

>Értesítést kaphat az oldal frissítéseiről. Csak adja hozzá [ezt az URL-címet](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS-hírcsatorna-olvasóhoz.

A hitelesítési rendszer folyamatosan módosítja és hozzáadja a szolgáltatásokat a biztonság és a szabványok megfelelőségének javítása érdekében. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő részletekkel kapcsolatos információkat tartalmazza:

- A legújabb funkciók
- Ismert problémák
- Protokoll módosításai
- Elavult funkciók

> [!TIP] 
> Ez az oldal rendszeresen frissül, ezért gyakran látogatnak. Hacsak másként nincs jelezve, ezek a módosítások csak az újonnan regisztrált alkalmazások esetében kerülnek érvénybe.  

## <a name="upcoming-changes"></a>Közelgő változások

Jelenleg nincs ütemezve.  Tekintse meg az alábbi, a vagy az éles környezetben futó módosításokat. 

## <a name="february-2020"></a>Február 2020: 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>A rendszer az összes HTTP-átirányításhoz hozzáfűzi az üres töredékeket a bejelentkezési végpontról. 

Hatálybalépés **dátuma**: február 8., 2020

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: response_type = lekérdezést használó OAuth-és OIDC-folyamatok, amelyek bizonyos esetekben az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) , valamint az [implicit folyamatot](v2-oauth2-implicit-grant-flow.md)fedik le. 

Ha a login.microsoftonline.com egy alkalmazáshoz HTTP-átirányítás útján küldenek hitelesítési választ, a szolgáltatás hozzáfűz egy üres töredéket a válasz URL-címéhez.  Ezzel megakadályozhatja, hogy a böngésző kitörölje a hitelesítési kérelemben szereplő meglévő töredékeket.  Ehhez a viselkedéshez egyetlen alkalmazásnak sincs függősége. 


## <a name="august-2019"></a>2019. augusztus

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>A POST Form szemantikai érvényesítése szigorúbb lesz, és a rendszer figyelmen kívül hagyja az idézőjeleket

Hatálybalépés **dátuma**: szeptember 2., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: bárhol a post használatban van ([ügyfél-hitelesítő adatok](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [engedélyezési kód beváltása](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)és [frissítési jogkivonat beváltása](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

A 9/2-as hét elindításával a POST metódust használó hitelesítési kérelmek szigorúbb HTTP-szabványokkal lesznek érvényesítve.  A szóközök és a kettős idézőjelek (") nem lesznek eltávolítva a kérelmek űrlapjának értékeiből. Ezeknek a változásoknak nem kell megszüntetniük a meglévő ügyfeleket, és biztosítaniuk kell, hogy az Azure AD-nek küldött kérelmeket minden alkalommal megbízhatóan kezelje a rendszer. A jövőben (lásd fent) Azt tervezzük, hogy az ismétlődő paramétereket is elutasítjuk, és figyelmen kívül hagyják az ANYAGJEGYZÉKet a kérések között. 

Példa:

Napjainkban a `?e=    "f"&g=h` azonos módon van elemezve `?e=f&g=h`-so `e` == `f`.  Ezzel a változással elemezni fogjuk, hogy `e` == `    "f"` – ez nem valószínű, hogy érvényes argumentum, és a kérés meghiúsul. 


## <a name="july-2019"></a>2019. július

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Csak az egybérlős alkalmazásokhoz tartozó jogkivonatok jelennek meg, ha az ügyfélalkalmazás létezik az erőforrás-bérlőben

**Hatálybalépés dátuma**: július 26., 2019

Érintett **végpontok**: [v 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) és [v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

Érintett **protokoll**: [ügyfél-hitelesítő adatok (csak alkalmazásbeli tokenek)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

A biztonsági változások élőben július 26-án mentek át, és az alkalmazáshoz tartozó jogkivonatok (az ügyfél hitelesítő adatainak megadása révén) megváltoznak. Korábban az alkalmazások jogkivonatokat kérhetnek a más alkalmazások meghívására, függetlenül attól, hogy az adott alkalmazáshoz hozzájárultak-e a bérlő vagy a szerepkörök.  Ez a viselkedés úgy frissült, hogy az erőforrások (más néven webes API-k) egybérlős (alapértelmezett) legyenek, az ügyfélalkalmazás léteznie kell az erőforrás-bérlőn belül.  Vegye figyelembe, hogy az ügyfél és az API közötti meglévő beleegyezés még nem szükséges, és az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük annak biztosításához, hogy egy `roles` jogcím megtalálható legyen, és tartalmazza az API várt értékét.

A forgatókönyvhöz tartozó hibaüzenet jelenleg a következő állapotokat jelzi: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

A probléma megoldásához a rendszergazdai engedélyekkel hozza létre az ügyfélalkalmazás szolgáltatásnevet a bérlőben, vagy hozza létre manuálisan.  Ez a követelmény biztosítja, hogy a bérlő a bérlőn belüli működésre engedélyt kapjon az alkalmazás számára.  

#### <a name="example-request"></a>Példakérelem

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` ebben a példában az erőforrás-bérlő (szolgáltató) contoso.com, az erőforrás-alkalmazás egy, a contoso-bérlő számára `gateway.contoso.com/api` nevű egybérlős alkalmazás, az ügyfélalkalmazás pedig `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Ha az ügyfélalkalmazás a Contoso.com-n belül rendelkezik egyszerű szolgáltatással, akkor a kérelem folytatódhat.  Ha azonban ez nem így van, a kérelem sikertelen lesz a fenti hibával.  

Ha a contoso-átjáró alkalmazás több-bérlős alkalmazás volt, akkor a kérést a rendszer a Contoso.com-on belüli egyszerű szolgáltatástól függetlenül folytatja.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Az átirányítási URI-k mostantól tartalmazhatnak lekérdezési karakterlánc-paramétereket

**Hatálybalépés dátuma**: július 22., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: minden folyamat

Az [RFC 6749-es](https://tools.ietf.org/html/rfc6749#section-3.1.2)verzióban az Azure ad-alkalmazások mostantól regisztrálhatnak és használhatnak átirányítási (válasz) URI-ket statikus lekérdezési paraméterekkel (például https://contoso.com/oauth2?idp=microsoft) OAuth 2,0-kérelmek esetén).  A dinamikus átirányítási URI-k továbbra is tiltottak, mivel biztonsági kockázatot jelentenek, és ez nem használható az állapotadatok megtartására a hitelesítési kérések között – Ehhez használja a `state` paramétert.

A statikus lekérdezési paraméter az átirányítási URI-k (például az átirányítási URI bármely más része) karakterlánc-megfeleltetésének felel meg – ha nincs regisztrálva karakterlánc, amely megfelel az URI-dekódolású redirect_urinak, a rendszer elutasítja a kérelmet.  Ha az URI megtalálható az alkalmazás regisztrációjában, a rendszer a teljes karakterláncot használja a felhasználó átirányításához, beleértve a statikus lekérdezési paramétert is. 

Vegye figyelembe, hogy jelenleg (2019. július végén) az alkalmazás regisztrációja UX Azure Portal továbbra is letiltják a lekérdezési paramétereket.  Az alkalmazás-jegyzékfájlt azonban manuálisan is szerkesztheti a lekérdezési paraméterek hozzáadásához és a teszteléshez az alkalmazásban.  


## <a name="march-2019"></a>2019. március

### <a name="looping-clients-will-be-interrupted"></a>A hurok-ügyfelek megszakadnak

**Hatálybalépés dátuma**: március 25., 2019

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: minden folyamat

Előfordulhat, hogy az ügyfélalkalmazások időnként nem működnek, és több száz ugyanazzal a bejelentkezési kérelemmel rendelkeznek.  Előfordulhat, hogy ezek a kérések nem sikeresek, de mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.  Ezek az alkalmazások a normál használat határain kívül működnek, és a megfelelő működés érdekében frissíteni kell őket.  

A duplikált kérelmeket többször kiállító ügyfelek `invalid_grant` hibaüzenetet küldenek: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A hiba elkerüléséhez a legtöbb ügyfélnek nem kell módosítania a viselkedést.  Ez a hiba csak a helytelenül konfigurált ügyfelek (a jogkivonat-gyorsítótárazás nélküli, illetve a hozzájuk tartozó prompt-hurkok nélkül) hatással lesz rá.  Az ügyfeleket az alábbi tényezők alapján, helyileg (cookie-n keresztül) követik nyomon.

* Felhasználói célzás, ha van ilyen

* Igényelt hatókörök vagy erőforrások

* Ügyfél-azonosító

* Átirányítási URI

* Válasz típusa és módja

A több kérést (15 +) használó alkalmazások rövid idő alatt (5 perc) egy `invalid_grant` hibaüzenetet kapnak, amely elmagyarázza, hogy hurkokat használ.  A kért jogkivonatok kellően hosszú élettartammal rendelkeznek (alapértelmezés szerint 10 perc, 60 perc), ezért az adott időszakra vonatkozó ismétlődő kérelmek szükségtelenek.  

Az összes alkalmazásnak `invalid_grant` kell kezelnie egy interaktív üzenet megjelenítésével, nem pedig a tokenek csendes kérelmezése helyett.  A hiba elkerüléséhez az ügyfeleknek biztosítaniuk kell, hogy megfelelően gyorsítótárazzák a kapott jogkivonatokat.


## <a name="october-2018"></a>2018. október

### <a name="authorization-codes-can-no-longer-be-reused"></a>Az engedélyezési kódokat már nem lehet újra felhasználni

Hatálybalépés **dátuma**: november 15., 2018

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokoll**: [kód flow](v2-oauth2-auth-code-flow.md)

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Minden olyan új alkalmazás, amely a OAuth-kód folyamata során a hitelesítési kódot próbálja újra felhasználni, invalid_grant hibát fog kapni.

További információ a frissítési tokenekről: [a hozzáférési tokenek frissítése](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  A ADAL vagy a MSAL használata esetén ezt a függvénytár kezeli – a "AcquireTokenByAuthorizationCodeAsync" második példányát cserélje le a "AcquireTokenSilentAsync" értékre. 

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Az OBO-folyamathoz nem használhatók azonosító jogkivonatok

**Dátum**: 2018. május 1.

Érintett **végpontok**: v 1.0 és v 2.0

Érintett **protokollok**: implicit flow és [OBO flow](v1-oauth2-on-behalf-of-flow.md)

2018. május 1-től a id_tokens nem használható az új alkalmazások esetében az OBO-flow-ban. A hozzáférési jogkivonatokat inkább az API-k védelmére kell használni, még az ügyfél és a középső rétegek között is. Az 2018. május 1. előtt regisztrált alkalmazások továbbra is működőképesek lesznek, és a hozzáférési tokenek id_tokens Exchange-re lesznek képesek. Ez a minta azonban nem tekinthető az ajánlott eljárásnak.

A módosítás megkerüléséhez a következőket teheti:

1. Hozzon létre egy webes API-t az alkalmazáshoz egy vagy több hatókörrel. Ez a explicit belépési pont lehetővé teszi a finomabb, részletes szabályozást és biztonságot.
1. Az alkalmazás jegyzékfájljában a [Azure Portal](https://portal.azure.com) vagy az [alkalmazás regisztrációs portálján](https://apps.dev.microsoft.com)ellenőrizze, hogy az alkalmazás jogosult-e hozzáférési tokeneket kiadni az implicit folyamaton keresztül. Ezt a `oauth2AllowImplicitFlow` kulcs vezérli.
1. Ha az ügyfélalkalmazás `response_type=id_token`-on keresztül kér id_token, a fent létrehozott webes API-hoz is igényel hozzáférési jogkivonatot (`response_type=token`). Így ha a v 2.0-s végpontot használja, a `scope` paraméternek a `api://GUID/SCOPE`hoz hasonlóan kell kinéznie. A v 1.0 végponton a `resource` paraméternek a webes API alkalmazás-URI azonosítójának kell lennie.
1. Adja át ezt a hozzáférési jogkivonatot a középső szintjére a id_token helyett.  
