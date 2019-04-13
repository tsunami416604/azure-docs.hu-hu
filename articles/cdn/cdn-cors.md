---
title: Cors-támogatással rendelkező Azure CDN szolgáltatás használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Content Delivery Network (CDN), az eltérő eredetű erőforrások megosztása (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3dbf0aea50f382a0b325bf068a200cde42098733
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547591"
---
# <a name="using-azure-cdn-with-cors"></a>Cors-támogatással rendelkező Azure CDN szolgáltatás használata
## <a name="what-is-cors"></a>Mit jelent a CORS?
A CORS (Adatbázisközi eredetű erőforrások megosztása) egy HTTP-funkció, amely lehetővé teszi egy adott tartományban futó webes alkalmazás egy másik tartományban lévő erőforrások eléréséhez. Annak érdekében, hogy az esélye, webhelyek közötti parancsfájlok futtatására, minden modern böngészők biztonsági korlátozással akadályozzák meg más néven [azonoseredet-](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Ez megakadályozza, hogy egy másik tartományban lévő API-k származó weblap.  A CORS biztonságos megoldást nyújt, hogy a forrás API-k hívásához egy forrás (a forrástartomány).

## <a name="how-it-works"></a>Működés
CORS-kérések két típusa van *egyszerű kérelmek* és *összetett kérelmeket.*

### <a name="for-simple-requests"></a>Egyszerű kérések:

1. A böngésző elküldi a CORS-kérést és a egy további **forrás** HTTP-kérelem fejléce. Ez a fejléc értéke a forrás, amely a szülő oldal, amely kombinációja típusúként van definiálva kiszolgált *protokoll* *tartomány,* és *port.*  Ha egy lapot https://www.contoso.com megpróbál hozzáférni egy felhasználói adatokat a fabrikam.com forrás, a következő kérés fejlécében a fabrikam.com küldi el:

   `Origin: https:\//www.contoso.com`

2. A kiszolgáló a következő jelenhetnek meg:

   * Egy **Access-Control-Allow-Origin** fejléc jelzi, hogy melyik forráswebhelynek engedélyezett válaszában. Példa:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Például ha a kiszolgáló nem engedélyezi az eltérő eredetű kérelem a Origin fejléc ellenőrzése után 403-as HTTP-hibakódot

   * Egy **Access-Control-Allow-Origin** fejlécet, amely lehetővé teszi az összes forrás helyettesítő karakterrel:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>A komplex kéréseket:

Egy összetett kérelme, mert a CORS-kéréshez ahol a böngésző szükséges küldése egy *ellenőrzési kérés* (azaz egy előzetes mintavételt) a tényleges CORS-kérés elküldése előtt. Az ellenőrzési kérés a kiszolgáló engedélyt kér az eredeti CORS felkérheti lépne, és egy `OPTIONS` kérelem az azonos URL-CÍMRE.

> [!TIP]
> A CORS-folyamatok és a közös alkalmazásmegoldásokra további részletekért tekintse meg a [útmutató, amellyel a REST API-kat CORS](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Helyettesítő karakter, vagy egyetlen forrás forgatókönyvek
Az Azure CDN CORS automatikusan fog működni további konfiguráció nélkül Ha a **Access-Control-Allow-Origin** fejléc értéke helyettesítő karakter (*) vagy egy egyetlen forrás.  A CDN gyorsítótárazzák az első válasz és későbbi kérelmeket használja ugyanazt a fejlécet.

Kérelmek már végzett és a CDN CORS a forrás beállítása előtt, ha szüksége lesz a végpont tartalmat töltse be újra a tartalmat a tartalom kiürítése a **Access-Control-Allow-Origin** fejléc.

## <a name="multiple-origin-scenarios"></a>Több forrás forgatókönyv
Ha egy adott lista azokat az eredeteket tartalmazza a CORS engedélyezendő engedélyeznie kell, dolgot kicsit összetettebb beolvasása. A probléma akkor fordul elő, amikor a CDN gyorsítótárazza a **Access-Control-Allow-Origin** az első CORS-origin fejléc.  Amikor egy másik CORS-forrás egy későbbi kérelmet, a CDN-t szolgálja ki a gyorsítótárazott **Access-Control-Allow-Origin** fejlécet, amely nem egyeznek.  Többféleképpen is lehet a probléma megoldása.

### <a name="azure-cdn-premium-from-verizon"></a>Prémium szintű Azure CDN a Verizontól
Ennek engedélyezéséhez a legjobb módja **verizon Azure CDN Premium**, amely közzéteszi néhány speciális funkciókkal. 

Kell [hozzon létre egy szabályt](cdn-rules-engine.md) ellenőrizheti a **forrás** a kérelem fejléce.  Ha egy érvényes forrás, a szabály fogja beállítani a **Access-Control-Allow-Origin** a forrás a kérésben megadott fejlécet.  Ha a forrás megadva az a **forrás** fejléc nem engedélyezett, a szabály kell kihagyja a **Access-Control-Allow-Origin** fejléc, ami a böngészőben, hogy elutasítja a kérelmet. 

Ha szeretné elvégezni a rules engine két módszer van. Mindkét esetben a **Access-Control-Allow-Origin** fejlécet a forráskiszolgáló a fájlt a rendszer figyelmen kívül hagyja, és a CDN szabálymotorral teljes körű kezelése a CORS engedélyezett eredetek.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Az összes érvényes forrás egy reguláris kifejezés
Ebben az esetben hozunk létre, amely tartalmazza az engedélyezni kívánt források összes reguláris kifejezést: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Az Azure CDN Premiumhoz a Verizontól** használ [Perl kompatibilis reguláris kifejezések](https://pcre.org/) reguláris kifejezések a motorként.  Egy hasonló eszközzel [reguláris kifejezések 101](https://regex101.com/) a reguláris kifejezés érvényesítése.  Fontos megjegyezni, hogy a "/" karaktert érvényes reguláris kifejezésekben, és nem kell kell megadni, azonban a karakter escape-karaktersorozat ajánlott eljárás, és néhány regex érvényesítők által várt.
> 
> 

Ha megfelel a reguláris kifejezés, a szabály felülírja az **Access-Control-Allow-Origin** fejléc (ha van ilyen) és a forrás, amely a kérelmet küldött a forrásból.  Például is hozzáadhat további CORS fejlécek, **hozzáférés-vezérlési-engedélyezése – módszerek**.

![A reguláris kifejezéssel szabályok példa](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Kérelem fejléce szabály minden forrás.
Ahelyett, hogy a reguláris kifejezések, ehelyett létrehozhat egy külön szabályt minden egyes forrás lehetővé teszi a kívánt a **kérelem fejléce helyettesítő** [feltételnek megfelelő](/previous-versions/azure/mt757336(v=azure.100)#Anchor_1). A reguláris kifejezés metódus az önálló szabálymotorral beállítja a CORS fejlécek. 

![Szabályok példa nélküli reguláris kifejezés](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> A helyettesítő karakter használatát a fenti példában a * arra utasítja a rules engine, hogy megfeleljen a HTTP és HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Az Azure standard szintű CDN-profilok
A standard szintű Azure CDN-profilok (**Azure CDN Standard a Microsoft**, **Azure CDN Akamai Standard**, és **Azure CDN Standard verizon**), a csak mechanizmus a helyettesítő karaktert tartalmazó forrás használata használata nélkül teszi lehetővé több adatforrás [lekérdezési karakterláncok gyorsítótárazása](cdn-query-string.md). A lekérdezési sztring beállítása a CDN-végpont engedélyezéséhez, majd egyedi lekérdezési karakterlánc minden olyan engedélyezett tartományhoz érkező kéréseket. Ez azt eredményezi, hogy a CDN-gyorsítótárazás minden egyedi lekérdezési karakterláncot egy külön objektumot. Ez a módszer nem ideális megoldás, azonban, több példányát ugyanahhoz a fájlhoz a CDN gyorsítótárazza eredményez.  

