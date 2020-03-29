---
title: Komplex veszélyforrások elleni védelem - Azure-adatbázis a PostgreSQL-hez - Egykiszolgálós
description: Ismerje meg, hogy használatával észlelheti az adatbázispotenciális biztonsági fenyegetéseit jelző rendellenes adatbázis-tevékenységeket.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768741"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Komplex veszélyforrások elleni védelem a PostgreSQL Azure-adatbázisában – egyetlen kiszolgáló

Az Advanced Threat Protection for Azure Database for PostgreSQL észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak.

> [!NOTE]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el.

A veszélyforrások elleni védelem része a komplex veszélyforrások elleni védelem (ADVANCED Threat Protection, ATP) ajánlatnak, amely egy egységes csomag a fejlett biztonsági képességekhez. A komplex veszélyforrások elleni védelem az [Azure Portalon](https://portal.azure.com) vagy a [REST API használatával](/rest/api/postgresql/serversecurityalertpolicies)érhető el és kezelhető. A szolgáltatás általános célú és memóriaoptimalizált kiszolgálókon érhető el.

> [!NOTE]
> Az Advanced Threat Protection funkció **nem** érhető el a következő Azure-kormányzat- és felhőrégiókban: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Kérjük, látogasson el [a termékek rendelkezésre álló régió általános](https://azure.microsoft.com/global-infrastructure/services/) termék elérhetőségét.

## <a name="what-is-advanced-threat-protection"></a>Mi az advanced threat protection?

Az Azure Database for PostgreSQL komplex veszélyforrások elleni védelem egy új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy észleljék és reagáljanak a potenciális fenyegetésekre, amikor azok előfordulnak a rendellenes tevékenységekre vonatkozó biztonsági riasztások biztosításával. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről és a potenciális biztonsági résekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. Az Azure Database for PostgreSQL komplex veszélyforrások elleni védelem integrálja a riasztásokat az [Azure Security Centerrel,](https://azure.microsoft.com/services/security-center/)amely tartalmazza a gyanús tevékenységek részleteit, és javaslatot tesz a fenyegetés kivizsgálására és csökkentésére. Az Azure Database for PostgreSQL komplex veszélyforrások elleni védelem egyszerűvé teszi az adatbázispotenciális fenyegetéseinek kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, vagy fejlett biztonsági figyelőrendszereket kell kezelnie. 

![Komplex veszélyforrások elleni védelem koncepciója](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni riasztások 
Az Azure Database for PostgreSQL komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket, és a következő riasztásokat indíthatja el:
- **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha a PostgreSQL-kiszolgálóhoz való Azure Database hozzáférési minta megváltozik, ahol valaki szokatlan földrajzi helyről jelentkezett be a PostgreSQL-kiszolgálóhoz készült Azure Database for PostgreSQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból:** Ez a riasztás akkor aktiválódik, ha a PostgreSQL-kiszolgálóhoz való Azure Database hozzáférési minta megváltozik, ahol valaki egy szokatlan Azure-adatközpontból jelentkezett be a kiszolgálóra, amely az elmúlt időszakban a kiszolgálón volt látható. Bizonyos esetekben a riasztás észleli a jogos művelet (az új alkalmazás az Azure, Power BI, Azure Database for PostgreSQL Query Editor). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés ismeretlen egyszerű:** Ez a riasztás akkor aktiválódik, ha a hozzáférési minta az Azure Database for PostgreSQL-kiszolgáló, ahol valaki bejelentkezett a kiszolgálóra egy szokatlan egyszerű (Azure-adatbázis PostgreSQL-felhasználó). Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos Azure-adatbázis postgreSQL hitelesítő adatok:** Ez a riasztás akkor aktiválódik, ha a bnormálisan nagy számú sikertelen bejelentkezések különböző hitelesítő adatokkal. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Az árképzéssel kapcsolatos további információkért tekintse meg az [Azure Database for PostgreSQL díjszabási oldalát.](https://azure.microsoft.com/pricing/details/postgresql/) 
* Az Azure Database konfigurálása [a PostgreSQL komplex veszélyforrások elleni védelemhez](howto-database-threat-protection-portal.md) az Azure portal használatával  
