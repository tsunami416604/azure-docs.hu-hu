---
title: Az Azure CDN használata cors-szal | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Content Delivery Network (CDN) a több származási erőforrás-megosztás (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278114"
---
# <a name="using-azure-cdn-with-cors"></a>Az Azure CDN használata cors-szal
## <a name="what-is-cors"></a>Mi az a CORS?
A CORS (Cross Origin Resource Sharing) egy HTTP-szolgáltatás, amely lehetővé teszi, hogy az egyik tartományban futó webalkalmazások hozzáférjenek egy másik tartomány erőforrásaihoz. Annak érdekében, hogy csökkentsék a lehetőségét a webhelyek közötti scripting támadások, minden modern böngészők végre egy biztonsági korlátozás néven [ismert azonos eredetű politika](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Ez megakadályozza, hogy egy weblap api-kat hívjon egy másik tartományban.  A CORS biztonságos módot biztosít arra, hogy az egyik forrás (a forrástartomány) egy másik eredetű API-kat hívjon meg.

## <a name="how-it-works"></a>Működés
Kétféle CORS-kérelem, *egyszerű kérelmek* és *összetett kérelmek.*

### <a name="for-simple-requests"></a>Egyszerű kérések esetén:

1. A böngésző egy további **Origin** HTTP-kérelemfejléccel küldi el a CORS-kérelmet. Ennek a fejlécnek az értéke az az eredet, amely a szülőlapot szolgálta, amely *protokoll,* *tartomány* és port kombinációjaként van *definiálva.*  Amikor egy https//www.contoso.com\:oldal megpróbál hozzáférni egy felhasználó adataihoz a fabrikam.com a forrásban, a rendszer a következő kérelemfejlécet küldi fabrikam.com:

   `Origin: https://www.contoso.com`

2. A kiszolgáló az alábbi válaszokkal válaszolhat:

   * **Access-Control-Allow-Origin** fejléc a válaszában, amely jelzi, hogy melyik származási hely engedélyezett. Példa:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * HTTP-hibakód, például 403, ha a kiszolgáló nem engedélyezi a kereszt-eredetű kérelmet az Origin fejléc ének ellenőrzése után

   * **Access-Control-Allow-Origin** fejléc helyettesítő karakterrel, amely lehetővé teszi az összes eredetet:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Összetett kérések esetén:

Egy összetett kérelem egy CORS-kérelem, ahol a böngésző nek el kell küldenie egy *elővizsgálati kérelmet* (azaz egy előzetes mintavételt) a tényleges CORS-kérelem elküldése előtt. Az elővizsgálati kérelem engedélyt kér a kiszolgálótól, ha `OPTIONS` az eredeti CORS-kérelem folytatható, és ugyanarra az URL-címre irányuló kérés.

> [!TIP]
> A CORS-folyamatokról és a gyakori buktatókról további információt a [REST API-k CORS-útmutatója](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)című részében talál.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Helyettesítő karaktervagy egyeredetű forgatókönyvek
Az Azure CDN CORS automatikusan fog működni további konfiguráció nélkül, ha az **Access-Control-Allow-Origin** fejléc helyettesítő (*) vagy egyetlen eredetre van állítva.  A CDN gyorsítótárazza az első választ, és az azt követő kérések ugyanazt a fejlécet fogják használni.

Ha a CDN-re már megérkezett kérelmek, mielőtt a CORS az eredetre van állítva, a végponttartalom tartalmát meg kell tisztítania a tartalom újratöltéséhez az **Access-Control-Allow-Origin** fejléccel.

## <a name="multiple-origin-scenarios"></a>Több forrásforgatókönyv
Ha engedélyeznie kell egy adott származási listát a CORS számára, a dolgok egy kicsit bonyolultabbak lesznek. A probléma akkor jelentkezik, amikor a CDN gyorsítótárazza az **Access-Control-Allow-Origin** fejlécet az első CORS-eredethez.  Ha egy másik CORS-eredet egy későbbi kérelmet küld, a CDN a gyorsítótárazott **Access-Control-Allow-Origin** fejlécet fogja kiszolgálni, amely nem egyezik.  Ezt többféleképpen is kijavíthatja.

### <a name="azure-cdn-standard-profiles"></a>Azure CDN szabványos profilok
A Microsoft Azure CDN Standard szolgáltatásában létrehozhat egy szabályt a [Standard rules motorban](cdn-standard-rules-engine-reference.md) az **Origin** fejlécének ellenőrzéséhez a kérelemben. Ha érvényes eredetű, a szabály a kívánt értékkel állítja be az **Access-Control-Allow-Origin** fejlécet. Ebben az esetben a fájl eredeti kiszolgálójáról származó **Access-Control-Allow-Origin** fejlécet a rendszer figyelmen kívül hagyja, és a CDN szabálymotorja teljes mértékben kezeli az engedélyezett CORS-eredeteket.

![Szabályok példa szabványos szabálymotorral](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> További válaszfejlécek, például **hozzáférés-vezérlés-engedélyezési módszerek**módosításához további műveleteket adhat a szabályhoz.
> 

Az **Azure CDN Standard a akamai**, az egyetlen mechanizmus, amely lehetővé teszi a több eredetű használata nélkül a helyettesítő karakter eredet használata [lekérdezési karakterlánc gyorsítótárazás](cdn-query-string.md). Engedélyezze a CDN-végpont lekérdezési karakterlánc-beállítását, majd használjon egyedi lekérdezési karakterláncot az egyes engedélyezett tartományokból érkező kérelmekhez. Ha így tesz, a CDN minden egyes egyedi lekérdezési karakterlánchoz külön objektumot gyorsítótáraz. Ez a megközelítés azonban nem ideális, mivel ugyanannak a fájlnak több példányát fogja eredményezni a CDN-en gyorsítótárazva.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium a Verizontól
A Verizon Premium szabálymotor használatával létre kell [hoznod egy szabályt,](cdn-rules-engine.md) hogy ellenőrizd az **Origin** fejlécét a kérésen.  Ha érvényes eredetű, a szabály a **hozzáférés-vezérlés-engedélyezés-engedélyezés-eredet** fejlécet állítja be a kérelemben megadott eredettel.  Ha az **Origin** fejlécben megadott origó nem engedélyezett, a szabálynak ki kell hagynia az **Access-Control-Allow-Origin** fejlécet, ami miatt a böngésző elutasítja a kérést. 

Ezt kétféleképpen is megteheti a Prémium szabályok motorjával. Mindkét esetben a fájl eredeti kiszolgálójáról származó **Access-Control-Allow-Origin** fejlécet a rendszer figyelmen kívül hagyja, és a CDN szabálymotorja teljes mértékben kezeli az engedélyezett CORS-eredeteket.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Egy reguláris kifejezés az összes érvényes eredettel
Ebben az esetben olyan reguláris kifejezést hoz létre, amely tartalmazza az összes engedélyezni kívánt eredetet: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **A Verizon Azure CDN Premium** [szolgáltatása perl-kompatibilis reguláris kifejezéseket](https://pcre.org/) használ a reguláris kifejezések motorjaként.  A reguláris kifejezés érvényesítéséhez használhatja a [Reguláris kifejezések 101-et](https://regex101.com/) hasonló eszközökkel.  Ne feledje, hogy a "/" karakter reguláris kifejezésekben érvényes, és nem kell elkerülni, azonban a karakter elől való menekülés ajánlott eljárásnak minősül, és néhány regex validator elvárja.
> 
> 

Ha a reguláris kifejezés megegyezik, a szabály lecseréli az **originális hozzáférés-vezérlés-engedélyezés-engedélyezés-eredet** fejlécét a kérelmet küldő eredetre.  További CORS-fejléceket is hozzáadhat, például **access-control-allow-methods .You**can also add additional CORS headers, such as Access-Control-Allow-Methods .

![Példa szabályok reguláris kifejezésre](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Kérelem fejlécszabály minden egyes forráshoz.
A reguláris kifejezések helyett létrehozhat egy külön szabályt minden egyes forráshoz, amelyet engedélyezni szeretne a **Fejléc kérelem helyettesítő karakterének** [feltételével.](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) A reguláris kifejezési módszerhez, a szabálymotor önmagában is beállítja a CORS-fejléceket. 

![Szabálypélda reguláris kifejezés nélkül](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> A fenti példában a helyettesítő karakter használata * azt mondja a szabálymotornak, hogy megfeleljen a HTTP és a HTTPS egyezésének.
> 
> 



