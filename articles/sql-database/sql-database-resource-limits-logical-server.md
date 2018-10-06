---
title: Az Azure SQL Database erőforrás-korlátozások - logikai kiszolgáló |} A Microsoft Docs
description: Ez a cikk az Azure SQL Database erőforráskorlátok áttekintést nyújt az önálló adatbázisokhoz és rugalmas készleteket használó készletezett adatbázisok. Mi történik, ha ezek erőforráskorlátok találati vagy túllépte a vonatkozó információkat is biztosít.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b48c090cc67d4557140b5734f1a5e1f763b271ab
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829563"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases-on-a-logical-server"></a>Egy logikai kiszolgálón egyetlen vagy készletezett adatbázisok SQL Database erőforrás-korlátozások 

Ez a cikk egy logikai kiszolgálón egyetlen vagy készletezett adatbázisok az SQL Database erőforrás-korlátozások áttekintést nyújt. Mi történik, ha ezek erőforráskorlátok találati vagy túllépte a vonatkozó információkat is biztosít.

> [!NOTE]
> Felügyelt példány korlátozásairól lásd: [SQL Database erőforrás-korlátozások felügyelt példányok](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximális erőforráskorlátok

| Erőforrás | Korlát |
| :--- | :--- |
| Adatbázisok kiszolgálónként | 5000 |
| Kiszolgálók száma előfizetésenként bármelyik régióban alapértelmezett száma | 20 |
| Kiszolgálók száma előfizetésenként bármelyik régióban maximális száma | 200 |  
| Dtu-k vagy eDTU-kvóta kiszolgálónként | 54,000 |  
| virtuális mag kvóta server-példány | 540 |
| Maximális készletek kiszolgálónként | attól függ, dtu-k vagy virtuális magok száma |
||||

> [!NOTE]
> További /eDTU DTU-kvótába, virtuális mag kvóta vagy további kiszolgálókat, mint az alapértelmezett érték beszerzéséhez egy új támogatási kérelmet az Azure Portalon, a probléma típusa "Kvóta" az előfizetés beküldhető. A dtu-k / eDTU kvóta- és adatbázis-korlát kiszolgálónként kiszolgálónként rugalmas készletek száma korlátozza. 

> [!IMPORTANT]
> Adatbázisok száma megközelíti a korlátot, egy logikai kiszolgálón, mert a következő fordulhat elő:
> - Növelje a várakozási ideje a lekérdezések futtatása a master adatbázison.  Ez magában foglalja az erőforrás-kihasználtsági statisztikáinak például sys.resource_stats nézetét.
> - A felügyeleti műveletek késése növelése, és a renderelési enumerálni a kiszolgálón lévő adatbázisokat érintő portál modelladatok között.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis erőforrás-korlátok elérésekor?

### <a name="compute-dtus-and-edtus--vcores"></a>COMPUTE (dtu-król és edtu-k / virtuális mag)

(Dtu-k és edtu-k vagy virtuális magok szerint mért) adatbázis számítási mértéke túlságosan megnő, a késés növekedése lekérdezése és még akkor is, időtúllépés is. Ezen feltételek mellett a lekérdezések előfordulhat, hogy a szolgáltatás várólistára kerül, és biztosítják az erőforrások erőforrásként végrehajtás ingyenes válnak.
Amikor magas számítási kihasználtságát, kockázatcsökkentési lehetőségek a következők:

- Az adatbázis vagy az adatbázis további számítási erőforrásokat biztosít a rugalmas készlet számítási méretének növelését. Lásd: [egyetlen adatbázis-erőforrások skálázása](sql-database-single-database-scale.md) és [méretezhető rugalmas adatbáziskészlet erőforrásainak](sql-database-elastic-pool-scale.md).
- Az erőforrás-használatot, az egyes lekérdezések csökkentése érdekében a lekérdezések optimalizálását. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Ha használt adatbázis-terület eléri a maximális méretkorlátot, adatbázis szúr be és frissítéseket, amelyek az adatok méretének növelése sikertelen, és azok az ügyfelek kapnak egy [hibaüzenet](sql-database-develop-error-messages.md). Adatbázis-KIVÁLASZTJA és TÖRLÉSEK továbbra is sikeres legyen.

Amikor magas lemezterület-kihasználás, kockázatcsökkentési lehetőségek a következők:

- Az adatbázisához vagy rugalmas maximális méretének növelését tárolókészlet, vagy vegyen fel további tárolókat. Lásd: [egyetlen adatbázis-erőforrások skálázása](sql-database-single-database-scale.md) és [méretezhető rugalmas adatbáziskészlet erőforrásainak](sql-database-elastic-pool-scale.md).
- Ha az adatbázis egy rugalmas készletben, majd azt is megteheti az adatbázis áthelyezhetők a készlet kívül, hogy a tárolóhely nincsenek megosztva, más adatbázisok.
- Az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [területe az Azure SQL Database kezelése](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>A munkamenetek és feldolgozók (kérelmek) 

A munkamenetek és a feldolgozók maximális száma határozza meg a szolgáltatási rétegben, és a számítási méret (dtu-król és edtu-k). Új kérelmek azért lettek elutasítva munkamenet vagy feldolgozói korlát elérésekor, és az ügyfelek hibaüzenetet kaphat. Bár a rendelkezésre álló kapcsolatok számát az alkalmazás által szabályozható, egyidejű feldolgozók száma nehezebb, gyakran becslése és ellenőrzését. Ez különösen igaz csúcsidőszakokban terhelés amikor adatbázis erőforráskorlátok eléri és feldolgozók egymásra hosszabb ideig futó lekérdezések miatt. 

Amikor magas munkamenet vagy feldolgozói kihasználtság, kockázatcsökkentési lehetőségek a következők:
- Növelése a szolgáltatási szint, vagy számítási az adatbázisához vagy rugalmas készlet mérete. Lásd: [egyetlen adatbázis-erőforrások skálázása](sql-database-single-database-scale.md) és [méretezhető rugalmas adatbáziskészlet erőforrásainak](sql-database-elastic-pool-scale.md).
- A számítási erőforrások optimalizálása minden egyes lekérdezés az erőforrás-használat csökkentésére, ha a megnövekedett feldolgozó kihasználtsági oka miatt a versengés a lekérdezéseket. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>További lépések

- Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat.
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- További információ a dtu-król és edtu-k: [dtu-król és edtu-k](sql-database-service-tiers.md#dtu-based-purchasing-model).
- A tempdb méretbeli korlátokat kapcsolatos információkért lásd: [Azure SQL Database-ben a TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
