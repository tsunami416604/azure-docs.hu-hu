---
title: Azure bejárati ajtó | Microsoft Docs
description: Ez a cikk az Azure Front Doorról nyújt áttekintést. Megtudhatja, hogy a megfelelő választás-e a felhasználói forgalom terheléselosztásához az alkalmazáshoz.
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
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515551"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Mi a szabályok motorja az Azure bejárati kapujában? 

A Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását, és a webalkalmazás viselkedésének nagyobb mértékű felügyeletét biztosítja. Az Azure-beli bejárati ajtóhoz tartozó szabályok a legfontosabb funkciókból állnak, többek között:

- Fejléc-alapú útválasztás – a kérések fejléce, cookie-k és lekérdezési karakterláncok tartalmában található minták alapján továbbítja a kérelmeket.
- Paraméter-alapú útválasztás – kihasználhatja az egyeztetési feltételek egy sorozatát, beleértve az argumentumokat, a lekérdezési karakterláncokat, a cookie-kat és a kérelmeket, valamint a HTTP-kérések paramétereinek alapján történő átirányítását. 
- Útvonal-konfigurációk felülbírálásai: 
    - Az átirányítási képességekkel 301/302/307/308-es átirányítást adhat vissza az ügyfélnek az új állomásnévre, elérési utakra és protokollokra való átirányításhoz. 
    - A továbbítási funkciók használatával a kérelem URL-címének újraírhatónak kell lennie anélkül, hogy hagyományos átirányítást kellene végeznie, és továbbítania kell a kérést a konfigurált háttérrendszer megfelelő hátterébe 
    - Testreszabhatja a gyorsítótárazási konfigurációt, és dinamikusan módosíthatja az útvonalakat a gyorsítótárazástól a megfeleltetési feltételek alapján. 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektúra 

A szabályok motorja kezeli a kérelmeket az Edge-ben. A szabályok motor konfigurálása után, amikor egy kérelem eléri a bejárati ajtó végpontját, a rendszer először a WAF hajtja végre, majd ezt követi a előtér/tartományhoz társított szabályok motor konfigurációja. A szabályok motorjának konfigurálásakor az azt jelenti, hogy a szülő-útválasztási szabály már egyezik. Annak megadása, hogy az egyes szabályokban szereplő összes művelet végre van-e hajtva, a szabályon belül az összes egyezési feltétel teljesül. Ha egy kérelem nem felel meg a szabály-végrehajtó konfigurációjának egyik feltételének sem, akkor az alapértelmezett útválasztási szabály lesz végrehajtva. 

Az alábbi konfigurációban például a szabályok motorja úgy van konfigurálva, hogy hozzáfűzje a válasz fejlécét, amely megváltoztatja a gyorsítótár-vezérlő maximális életkorát, ha az egyeztetési feltétel teljesül. 

![Válasz fejlécének művelete](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Egy másik példában látható, hogy a szabályok motorja úgy van konfigurálva, hogy egy felhasználót a hely mobil verziójára küldjön, ha az egyeztetési feltétel, az eszköz típusa igaz. 

![útvonal konfigurációjának felülbírálása](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Mindkét példa esetében, ha egyetlen egyezési feltétel sem teljesül, a megadott útválasztási szabály az, amit végrehajt. 

## <a name="terminology"></a>Terminológia 

A AFD Rules Engine használatával több szabálykészlet-konfigurációt hozhat létre, amelyek mindegyike szabályokból áll. Az alábbiakban néhány hasznos terminológiát ismertetünk, amely a szabályok motorjának konfigurálásakor fog megjelenni. 

- *Rules Engine-konfiguráció*: egyetlen útvonalra vonatkozó szabályra alkalmazott szabályok halmaza. Mindegyik konfiguráció 5 szabályra van korlátozva. Akár 10 konfigurációt is létrehozhat. 
- *Rules Engine szabály*: egy legfeljebb 10 egyeztetési feltételből és 5 műveletből álló szabály.
- *Egyeztetési feltétel*: a beérkező kérések elemzéséhez számos egyeztetési feltétel használható. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltételek a **és** a operátorral vannak kiértékelve. Az egyeztetési feltételek teljes listája [itt](front-door-rules-engine-match-conditions.md)található. 
- *Művelet*: a műveletek azt írják elő, hogy mi történik a bejövő kérelmekkel – a kérés/válasz fejléc műveletei, a továbbítás, az átirányítások és az újraírások még ma elérhetők. Egy szabály legfeljebb 5 műveletet tartalmazhat. egy szabály azonban csak 1 útvonal-konfiguráció felülbírálását tartalmazhatja.  A műveletek teljes listája [itt](front-door-rules-engine-actions.md)található.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan állíthatja be az első [szabályok motorjának konfigurációját](front-door-tutorial-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
