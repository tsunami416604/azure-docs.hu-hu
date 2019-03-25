---
title: Az Azure Active Directory használhatatlanná tévő változásai hivatkozás |} A Microsoft Docs
description: Ismerje meg, amely hatással lehet az alkalmazás az Azure AD-protokollok végzett módosítások.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401295"
---
# <a name="whats-new-for-authentication"></a>Újdonságok a hitelesítéshez? 

>Ez a lap frissítésével kapcsolatos értesítést kaphat. Adja hozzá a [az URL-cím](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS-hírcsatorna-olvasó.

A hitelesítési rendszere módosítja, és hozzáadja a szolgáltatások rendszeresen biztonsági és megfelelőségi szabványoknak. Vétele a legújabb fejlemények, ez a cikk nyújt tájékoztatást a következő adatokat:

- Legújabb funkciókról
- Ismert problémák
- Protokoll módosítások
- Elavult funkciók

> [!TIP] 
> Ez az oldal rendszeresen frissül, így látogat. Ha nincs másképp jelölve, a módosítások csak kerüljenek helyen az újonnan regisztrált alkalmazások.  

## <a name="upcoming-changes"></a>Közelgő változások

Nincs, jelenleg ütemezve. 

## <a name="march-2019"></a>2019. március

### <a name="looping-clients-will-be-interrupted"></a>Az ügyfelek hurkolás megszakad

**Hatályba lépés dátuma**: 2019. márciusi 25.

**Érintett végpontok**: 1.0-s verziója és a 2.0-s verzió

**Érintett protokoll**: Minden folyamat

Ügyfélalkalmazások is néha feladatelőzményeket, a bejelentkezési kérésben több száz kiállító rövid időn át.  Előfordulhat, hogy ezeket a kérelmeket, vagy nem lehet sikeres, de az Identitásszolgáltató, növelje a késés az összes felhasználó számára, és csökkenti az Identitásszolgáltató rendelkezésre állásának közreműködés mindannyian és a gyenge felhasználói élmény fokozása számítási feladatok.  Ezek az alkalmazások működnek a normál használati határain kívül, és frissíteni kell, hogy megfelelően viselkednek.  

Az ügyfelek ezt a problémát az ismétlődő kérelmek többszöri küld egy `invalid_grant` hiba: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A legtöbb ügyfelek nem kell a hiba elkerüléséhez viselkedés megváltoztatásához.  Ez a hiba által érintett csak helytelenül konfigurált ügyfelek (token-gyorsítótárazási nélküli vagy azok Rákérdezés hurkok már helymetrikák).  Az ügyfelek nyomon példány helyi (cookie-k) használatával a következő tényezőktől függ:

* Felhasználói mutatót, ha van ilyen

* Hatókörök vagy a kért erőforrás

* Ügyfél-azonosító

* Átirányítási URI

* Válasz típusa és mód

Így több kérés (15 +) egy rövid idő alatt (5 perc) alkalmazások fog kapni egy `invalid_grant` arról tájékoztat, hogy azok hurkolást.  A jogkivonatok kért elég hosszú élettartamú élettartama (10 perc legalább, alapértelmezés szerint 60 percenként), ezért a következő ismétlődő kérelmek ez idő alatt nem szükségesek.  

Kezelje a minden alkalmazás `invalid_grant` által egy interaktív kérdés megjelenítő ahelyett, hogy csendes kér egy token.  Annak érdekében, hogy ez a hiba, az ügyfelek biztosítják, hogy azok helyesen vannak gyorsítótárazás a kapott jogkivonatokat.


## <a name="october-2018"></a>2018. október

### <a name="authorization-codes-can-no-longer-be-reused"></a>Engedélyezési kód már nem használható fel újra

**Hatályba lépés dátuma**: 2018. november 15.

**Érintett végpontok**: 1.0-s verziója és a 2.0-s verzió

**Érintett protokoll**: [Hitelesítésikód-folyamata](v2-oauth2-auth-code-flow.md)

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Minden olyan új alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

További információ a frissítési biztonsági jogkivonat: [frissítése a hozzáférési jogkivonatok](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Ha adal-t vagy az MSAL használatával, ez kezeli az Ön számára a szalagtár - cserélje le az "AcquireTokenByAuthorizationCodeAsync" a "AcquireTokenSilentAsync" második példányát. 

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID jogkivonatok nem használható a OBO folyamat

**Dátum**: 2018. május 1.

**Érintett végpontok**: 1.0-s verziója és a 2.0-s verzió

**Érintett protokollok**: Implicit folyamat és [OBO folyamat](v1-oauth2-on-behalf-of-flow.md)

2018. május 1. után id_tokens nem használható a OBO folyamatban a helyességi feltétel alkalmazása új alkalmazások számára. Hozzáférési jogkivonatok helyette használandó API-kat, biztonságossá is között egy ügyfél és a középső réteg ugyanazzal az alkalmazással. Mielőtt 2018. május 1. továbbra is működik, és képes az exchange-hozzáférési jogkivonat helyeként; id_tokens regisztrált alkalmazás Ez a minta azonban nem tekinthető a legjobb.

Ez a változás megkerüléséhez a következőket teheti:

1. Hozzon létre egy webes API-t az alkalmazás egy vagy több hatókört. Ez explicit belépési pont lehetővé teszi az ennél a részletes ellenőrzés és biztonság.
1. Az alkalmazás-jegyzékfájl, az a a [az Azure portal](https://portal.azure.com) vagy a [alkalmazás regisztrációs portál](https://apps.dev.microsoft.com), győződjön meg arról, hogy az alkalmazás engedélyezett az implicit folyamatot keresztül hozzáférési jogkivonatokat kibocsátani. Ennek segítségével lehet szabályozni az `oauth2AllowImplicitFlow` kulcsot.
1. Amikor az ügyfélalkalmazást igényel a keresztül id_token `response_type=id_token`, is kérheti a hozzáférési jogkivonat (`response_type=token`) a fent létrehozott webes API-hoz. Így a v2.0-végpont használata esetén a `scope` paraméter hasonlóan kell kinéznie `api://GUID/SCOPE`. Az 1.0-s verziójú végpont a `resource` paraméternek kell lennie az alkalmazás URI-ját a webes API-t.
1. A középső réteg az id_token helyett adja meg a hozzáférési jogkivonatot.  
