---
title: Az Azure-beli bejárati architektúra architektúrájának és terminológiájának szabályozása
description: Ez a cikk áttekintést nyújt az Azure bejárati szabályainak motor szolgáltatásáról.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 1332f4b21f971d23055c158c2aebdd3316f1bd39
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512214"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Mi a szabályok motorja az Azure bejárati kapujában? 

A Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását, és a webalkalmazás viselkedésének nagyobb mértékű felügyeletét biztosítja. Az Azure-beli bejárati ajtóhoz tartozó szabályok a legfontosabb funkciókból állnak, többek között:

- A HTTPS betartatása, gondoskodjon róla, hogy a végfelhasználók a tartalmat biztonságos kapcsolaton keresztül használják.
- A biztonsági fejlécek alkalmazásával megakadályozhatja a böngészőalapú biztonsági réseket, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy, az X-frame-Options, valamint a hozzáférés-vezérlés-engedélyezési-Origin fejléceket a különböző eredetű erőforrás-megosztási (CORS) forgatókönyvekhez. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.
- Az alkalmazás mobil vagy asztali verziójára irányuló kérelmek átirányítása a kérések fejlécei, cookie-k vagy lekérdezési karakterláncok tartalmának mintái alapján.
- Az átirányítási képességekkel 301, 302, 307 és 308 értéket adhat vissza az ügyfélnek, hogy átirányítsa az új állomásnévre, elérési utakra vagy protokollokra.
- Dinamikusan módosítja az útvonal gyorsítótárazási konfigurációját a bejövő kérelmek alapján.
- Írja át újra a kérelem URL-címét, és továbbítsa a kérést a megfelelő háttérbe a konfigurált háttérrendszer-készletben.

## <a name="architecture"></a>Architektúra 

A szabályok motorja kezeli a kérelmeket az Edge-ben. A szabályok motor konfigurálása után, amikor egy kérelem eléri a bejárati ajtó végpontját, a rendszer először a WAF hajtja végre, majd ezt követi a előtér/tartományhoz társított szabályok motor konfigurációja. A szabályok motorjának konfigurálásakor az azt jelenti, hogy a szülő-útválasztási szabály már egyezik. Annak megadása, hogy az egyes szabályokban szereplő összes művelet végre van-e hajtva, a szabályon belül az összes egyezési feltétel teljesül. Ha egy kérelem nem felel meg a szabály-végrehajtó konfigurációjának egyik feltételének sem, akkor az alapértelmezett útválasztási szabály lesz végrehajtva. 

Az alábbi konfigurációban például a szabályok motorja úgy van konfigurálva, hogy hozzáfűzje a válasz fejlécét, amely megváltoztatja a gyorsítótár-vezérlő maximális életkorát, ha az egyeztetési feltétel teljesül. 

![Válasz fejlécének művelete](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Egy másik példában látható, hogy a szabályok motorja úgy van konfigurálva, hogy egy felhasználót a hely mobil verziójára küldjön, ha az egyeztetési feltétel, az eszköz típusa igaz. 

![útvonal konfigurációjának felülbírálása](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Mindkét példa esetében, ha egyetlen egyezési feltétel sem teljesül, a megadott útválasztási szabály az, amit végrehajt. 

## <a name="terminology"></a>Terminológia 

A AFD Rules Engine használatával több szabálykészlet-konfigurációt hozhat létre, amelyek mindegyike szabályokból áll. Az alábbiakban néhány hasznos terminológiát ismertetünk, amely a szabályok motorjának konfigurálásakor fog megjelenni. 

- *Rules Engine-konfiguráció*: egyetlen útvonalra vonatkozó szabályra alkalmazott szabályok halmaza. Mindegyik konfiguráció 25 szabályra van korlátozva. Akár 10 konfigurációt is létrehozhat. 
- *Rules Engine szabály*: egy legfeljebb 10 egyeztetési feltételből és 5 műveletből álló szabály.
- *Egyeztetési feltétel*: a beérkező kérések elemzéséhez számos egyeztetési feltétel használható. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltételek a **és** a operátorral vannak kiértékelve. Az egyeztetési feltételek teljes listája [itt](front-door-rules-engine-match-conditions.md)található. 
- *Művelet*: a műveletek azt írják elő, hogy mi történik a bejövő kérelmekkel – a kérés/válasz fejléc műveletei, a továbbítás, az átirányítások és az újraírások még ma elérhetők. Egy szabály legfeljebb 5 műveletet tartalmazhat. egy szabály azonban csak 1 útvonal-konfiguráció felülbírálását tartalmazhatja.  A műveletek teljes listája [itt](front-door-rules-engine-actions.md)található.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan állíthatja be az első [szabályok motorjának konfigurációját](front-door-tutorial-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
