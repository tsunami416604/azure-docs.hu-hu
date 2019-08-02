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
ms.topic: article
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c0bc7f5d4890ae494c6c6616b42eddc2445b159
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666486"
---
# <a name="whats-new-for-authentication"></a>A hitelesítés újdonságai 

>Értesítést kaphat az oldal frissítéseiről. Csak adja hozzá [ezt az URL-címet](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS-hírcsatorna-olvasóhoz.

A hitelesítési rendszer folyamatosan módosítja és hozzáadja a szolgáltatásokat a biztonság és a szabványok megfelelőségének javítása érdekében. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő részletekkel kapcsolatos információkat tartalmazza:

- Legújabb funkciók
- Ismert problémák
- Protokoll módosításai
- Elavult funkciók

> [!TIP] 
> Ez az oldal rendszeresen frissül, ezért gyakran látogatnak. Hacsak másként nincs jelezve, ezek a módosítások csak az újonnan regisztrált alkalmazások esetében kerülnek érvénybe.  

## <a name="upcoming-changes"></a>Közelgő változások

Augusztus 2019: Az URL-elemzési szabályok alapján kényszerítheti a szemantikai hibákat – az ismétlődő paraméterek hibát jeleznek, a paraméterek közötti idézőjelek a továbbiakban nem lesznek figyelmen kívül hagyva, és az [Anyagjegyzék](https://www.w3.org/International/questions/qa-byte-order-mark) figyelmen kívül lesz hagyva.

## <a name="july-2019"></a>Július 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Csak az egybérlős alkalmazásokhoz tartozó jogkivonatok jelennek meg, ha az ügyfélalkalmazás létezik az erőforrás-bérlőben

Hatálybalépés **dátuma**: 2019. július 26.

Érintett végpontok: [V 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) és [v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

Érintett **protokoll**: [Ügyfél hitelesítő adatai (csak az alkalmazáshoz tartozó jogkivonatok)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

A biztonsági változások élőben július 26-án mentek át, és az alkalmazáshoz tartozó jogkivonatok (az ügyfél hitelesítő adatainak megadása révén) megváltoznak. Korábban az alkalmazások jogkivonatokat kérhetnek a más alkalmazások meghívására, függetlenül attól, hogy az adott alkalmazáshoz hozzájárultak-e a bérlő vagy a szerepkörök.  Ez a viselkedés úgy frissült, hogy az erőforrások (más néven webes API-k) egybérlős (alapértelmezett) legyenek, az ügyfélalkalmazás léteznie kell az erőforrás-bérlőn belül.  Vegye figyelembe, hogy az ügyfél és az API közötti meglévő hozzájárulás még nem szükséges, és az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük annak biztosításához, hogy `roles` a jogcímek jelen legyenek, és tartalmazzák az API várt értékét.

A forgatókönyvhöz tartozó hibaüzenet jelenleg a következő állapotokat jelzi: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

A probléma megoldásához a rendszergazdai engedélyekkel hozza létre az ügyfélalkalmazás szolgáltatásnevet a bérlőben, vagy hozza létre manuálisan.  Ez a követelmény biztosítja, hogy a bérlő a bérlőn belüli működésre engedélyt kapjon az alkalmazás számára.  

#### <a name="example-request"></a>Példakérelem

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Ebben a példában az erőforrás-bérlő (szolgáltató) a contoso.com, az erőforrás-alkalmazás egy, a contoso-bérlőhöz nevű `gateway.contoso.com/api` egybérlős alkalmazás, és az ügyfélalkalmazás. `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`  Ha az ügyfélalkalmazás a Contoso.com-n belül rendelkezik egyszerű szolgáltatással, akkor a kérelem folytatódhat.  Ha azonban ez nem így van, a kérelem sikertelen lesz a fenti hibával.  

Ha a contoso-átjáró alkalmazás több-bérlős alkalmazás volt, akkor a kérést a rendszer a Contoso.com-on belüli egyszerű szolgáltatástól függetlenül folytatja.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Az átirányítási URI-k mostantól tartalmazhatnak lekérdezési karakterlánc-paramétereket

Hatálybalépés **dátuma**: 2019. július 22.

Érintett végpontok: V 1.0 és v 2.0

Érintett **protokoll**: Minden folyamat

Az [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)-ben az Azure ad-alkalmazások mostantól regisztrálhatják és használhatják az átirányítási (válasz) URI- https://contoso.com/oauth2?idp=microsoft) ket statikus lekérdezési paraméterekkel (például OAuth 2,0-kérelmek esetén).  A dinamikus átirányítási URI-k továbbra is tiltottak, mivel biztonsági kockázatot jelentenek, és ez nem használható az állapotadatok megtartására a hitelesítési kérések között `state` – Ehhez használja a paramétert.

A statikus lekérdezési paraméter az átirányítási URI-k (például az átirányítási URI bármely más része) karakterlánc-egyeztetésére vonatkozik – ha nincs regisztrálva karakterlánc, amely megfelel az URI-dekódolású redirect_uri, a rendszer elutasítja a kérelmet.  Ha az URI megtalálható az alkalmazás regisztrációjában, a rendszer a teljes karakterláncot használja a felhasználó átirányításához, beleértve a statikus lekérdezési paramétert is. 

Vegye figyelembe, hogy jelenleg (2019. július végén) az alkalmazás regisztrációja UX Azure Portal továbbra is letiltják a lekérdezési paramétereket.  Az alkalmazás-jegyzékfájlt azonban manuálisan is szerkesztheti a lekérdezési paraméterek hozzáadásához és a teszteléshez az alkalmazásban.  


## <a name="march-2019"></a>Március 2019

### <a name="looping-clients-will-be-interrupted"></a>A hurok-ügyfelek megszakadnak

Hatálybalépés **dátuma**: Március 25., 2019

Érintett végpontok: V 1.0 és v 2.0

Érintett **protokoll**: Minden folyamat

Előfordulhat, hogy az ügyfélalkalmazások időnként nem működnek, és több száz ugyanazzal a bejelentkezési kérelemmel rendelkeznek.  Előfordulhat, hogy ezek a kérések nem sikeresek, de mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.  Ezek az alkalmazások a normál használat határain kívül működnek, és a megfelelő működés érdekében frissíteni kell őket.  

A duplikált kérelmeket `invalid_grant` többször kibocsátó ügyfelek hibaüzenetet kapnak: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A hiba elkerüléséhez a legtöbb ügyfélnek nem kell módosítania a viselkedést.  Ez a hiba csak a helytelenül konfigurált ügyfelek (a jogkivonat-gyorsítótárazás nélküli, illetve a hozzájuk tartozó prompt-hurkok nélkül) hatással lesz rá.  Az ügyfeleket az alábbi tényezők alapján, helyileg (cookie-n keresztül) követik nyomon.

* Felhasználói célzás, ha van ilyen

* Igényelt hatókörök vagy erőforrások

* Ügyfél-azonosító

* Átirányítási URI

* Válasz típusa és módja

A több kérést (15 +) használó alkalmazások rövid időn belül (5 perc) `invalid_grant` hibaüzenetet kapnak arról, hogy a rendszer hurkokat küld.  A kért jogkivonatok kellően hosszú élettartammal rendelkeznek (alapértelmezés szerint 10 perc, 60 perc), ezért az adott időszakra vonatkozó ismétlődő kérelmek szükségtelenek.  

A tokenek csendes `invalid_grant` kérelmezése helyett minden alkalmazásnak egy interaktív kérést kell kezelnie.  A hiba elkerüléséhez az ügyfeleknek biztosítaniuk kell, hogy megfelelően gyorsítótárazzák a kapott jogkivonatokat.


## <a name="october-2018"></a>2018. október

### <a name="authorization-codes-can-no-longer-be-reused"></a>Az engedélyezési kódokat már nem lehet újra felhasználni

Hatálybalépés **dátuma**: November 15., 2018

Érintett végpontok: V 1.0 és v 2.0

Érintett **protokoll**: [Kód folyamatábrája](v2-oauth2-auth-code-flow.md)

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Minden olyan új alkalmazás, amely a OAuth-kód folyamata során hitelesítési kódot próbál használni, invalid_grant hibaüzenetet kap.

További információ a frissítési tokenekről: [a hozzáférési tokenek frissítése](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  A ADAL vagy a MSAL használata esetén ezt a függvénytár kezeli – a "AcquireTokenByAuthorizationCodeAsync" második példányát cserélje le a "AcquireTokenSilentAsync" értékre. 

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Az OBO-folyamathoz nem használhatók azonosító jogkivonatok

**Dátum**: 2018. május 1.

Érintett végpontok: V 1.0 és v 2.0

Érintett **protokollok**: Implicit folyamat és [OBO-folyamat](v1-oauth2-on-behalf-of-flow.md)

2018. május 1-től a id_tokens nem használható az új alkalmazások esetében az OBO-flow-ban. A hozzáférési jogkivonatokat inkább az API-k védelmére kell használni, még az ügyfél és a középső rétegek között is. Az 2018. május 1. előtt regisztrált alkalmazások továbbra is működőképesek lesznek, és a hozzáférési jogkivonatok id_tokens is képesek lesznek cserélni; Ez a minta azonban nem tekinthető az ajánlott eljárásnak.

A módosítás megkerüléséhez a következőket teheti:

1. Hozzon létre egy webes API-t az alkalmazáshoz egy vagy több hatókörrel. Ez a explicit belépési pont lehetővé teszi a finomabb, részletes szabályozást és biztonságot.
1. Az alkalmazás jegyzékfájljában a [Azure Portal](https://portal.azure.com) vagy az [alkalmazás regisztrációs portálján](https://apps.dev.microsoft.com)ellenőrizze, hogy az alkalmazás jogosult-e hozzáférési tokeneket kiadni az implicit folyamaton keresztül. Ezt a `oauth2AllowImplicitFlow` kulcs vezérli.
1. Amikor az ügyfélalkalmazás egy id_token-t kér `response_type=id_token`a-n keresztül, a fent`response_type=token`létrehozott webes API-hoz is kér hozzáférési tokent (). Így ha a v 2.0-s végpontot `scope` használja, a paraméternek `api://GUID/SCOPE`a következőhöz hasonlóan kell kinéznie:. A v 1.0 végponton a `resource` paraméternek a webes API alkalmazás-URI-ja kell lennie.
1. Adja át ezt a hozzáférési jogkivonatot a középső szintjére a id_token helyett.  
