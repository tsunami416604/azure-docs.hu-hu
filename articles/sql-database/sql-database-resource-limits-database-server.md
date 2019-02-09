---
title: Az Azure SQL Database-kiszolgálóhoz erőforráskorlátok |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure SQL Database-kiszolgálóhoz erőforráskorlátok önálló adatbázisokhoz és rugalmas készleteket. Mi történik, ha ezek erőforráskorlátok találati vagy túllépte a vonatkozó információkat is biztosít.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 189b6bc3de4cc17d16c18baf046c5ede9b6363a2
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962656"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Az Azure SQL Database-kiszolgálóhoz tartozó SQL Database erőforráskorlátok

Ez a cikk egy SQL Database-kiszolgáló, amely felügyeli az önálló adatbázisok és rugalmas készletek az SQL Database erőforrás-korlátozások áttekintést nyújt. Mi történik, ha ezek erőforráskorlátok találati vagy túllépte a vonatkozó információkat is biztosít.

> [!NOTE]
> Felügyelt példányok korlátozásairól lásd: [SQL Database erőforrás-korlátozások felügyelt példányok](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximális erőforráskorlátok

| Erőforrás | Korlát |
| :--- | :--- |
| Adatbázisok kiszolgálónként | 5000 |
| Kiszolgálók száma előfizetésenként bármelyik régióban alapértelmezett száma | 20 |
| Kiszolgálók száma előfizetésenként bármelyik régióban maximális száma | 200 |  
| Dtu-k vagy eDTU-kvóta kiszolgálónként | 54,000 |  
| virtuális mag kvóta server-példány | 540 |
| Maximális készletek kiszolgálónként | A dtu-k vagy virtuális magok száma korlátozott. Ha például minden készlet 1000 dtu-k használata, majd egy kiszolgáló támogatják-e 54 készletek.|
||||

> [!NOTE]
> További /eDTU DTU-kvótába, virtuális mag kvóta vagy további kiszolgálókat, mint az alapértelmezett érték beszerzéséhez egy új támogatási kérelmet az Azure Portalon, a probléma típusa "Kvóta" az előfizetés beküldhető. A dtu-k / eDTU kvóta- és adatbázis-korlát kiszolgálónként kiszolgálónként rugalmas készletek száma korlátozza.
> [!IMPORTANT]
> Adatbázisok száma megközelíti a korlátot, egy SQL Database-kiszolgálón, ahogy az alábbi fordulhat elő:
> - Növelje a várakozási ideje a lekérdezések futtatása a master adatbázison.  Ez magában foglalja az erőforrás-kihasználtsági statisztikáinak például sys.resource_stats nézetét.
> - A felügyeleti műveletek késése növelése, és a renderelési enumerálni a kiszolgálón lévő adatbázisokat érintő portál modelladatok között.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik, ha az adatbázis erőforrás-korlátozások elérésekor

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

- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- További információ a dtu-król és edtu-k: [dtu-król és edtu-k](sql-database-service-tiers.md#dtu-based-purchasing-model).
- A tempdb méretbeli korlátokat kapcsolatos információkért lásd: [Azure SQL Database-ben a TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
