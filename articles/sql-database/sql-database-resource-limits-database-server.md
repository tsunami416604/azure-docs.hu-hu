---
title: Azure SQL Database kiszolgálói erőforrás korlátai | Microsoft Docs
description: Ez a cikk áttekintést nyújt az önálló adatbázisok és a rugalmas készletek Azure SQL Database kiszolgálói erőforrásának korlátairól. Emellett tájékoztatást nyújt arról is, hogy mi történik, ha az erőforrás korlátai elérik vagy túllépik azokat.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: 175f694cbe46f871349136c9ce91888b6de48d21
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566852"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database-kiszolgáló erőforrás-korlátainak SQL Database

Ez a cikk áttekintést nyújt az önálló adatbázisokat és rugalmas készleteket kezelő SQL Database kiszolgálók SQL Database erőforrás-korlátairól. Emellett tájékoztatást nyújt arról is, hogy mi történik, ha az erőforrás korlátai elérik vagy túllépik azokat.

> [!NOTE]
> A felügyelt példányok korlátaival kapcsolatban lásd: [a felügyelt példányok erőforrás-korlátainak SQL Database](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Erőforrás-korlátok maximális száma

| Resource | Korlát |
| :--- | :--- |
| Adatbázisok száma kiszolgálónként | 5000 |
| Az előfizetéshez tartozó kiszolgálók alapértelmezett száma bármely régióban | 20 |
| Kiszolgálók/előfizetés maximális száma bármely régióban | 200 |  
| DTU/eDTU kvóta kiszolgálónkénti bontásban | 54,000 |  
| Virtuális mag-kvóta kiszolgálónkénti/példányon | 540 |
| Készletek maximális száma kiszolgálónként | Korlátozott számú DTU vagy virtuális mag. Ha például az egyes készletek 1000 DTU, akkor a kiszolgáló támogatja az 54-es készleteket.|
|||

> [!NOTE]
> Ha további DTU/eDTU-kvótát, virtuális mag-kvótát vagy több kiszolgálót szeretne beszerezni az alapértelmezett értéknél, új támogatási kérést küldhet a "kvóta" típusú előfizetéshez tartozó Azure Portalban. A DTU/eDTU kvóta és az adatbázis-korlát kiszolgálónként korlátozza a rugalmas készletek másodpercenkénti számát.
> [!IMPORTANT]
> Mivel az adatbázisok száma SQL Database kiszolgálón megközelíti a korlátot, a következő műveletek végezhetők el:
> - Növekvő késés a főadatbázison futó lekérdezések futtatásakor.  Ide tartoznak az erőforrás-kihasználtsági statisztikák, például a sys. resource_stats nézetei.
> - Növekvő késés a felügyeleti műveletekben és a portálon olyan nézőpontok, amelyek a kiszolgáló adatbázisainak számbavételét foglalják magukban.

### <a name="storage-size"></a>Tároló mérete
- Önálló adatbázisok esetén a rources a [DTU-alapú erőforrás-korlátokat](sql-database-dtu-resource-limits-single-databases.md) vagy a [virtuális mag-alapú erőforrás-](sql-database-vcore-resource-limits-single-databases.md) korlátokat tekintheti meg.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis-erőforrások korlátainak elérésekor

### <a name="compute-dtus-and-edtus--vcores"></a>Számítás (DTU és Edtu/virtuális mag)

Ha az adatbázis számítási kihasználtsága (DTU és Edtu, illetve virtuális mag alapján mérve) magas, a lekérdezés késése megnő, és akár időtúllépés is lehet. Ilyen körülmények között előfordulhat, hogy a szolgáltatás várólistára helyezi a lekérdezéseket, és erőforrásokat biztosít a végrehajtáshoz, mivel az erőforrás ingyenesvé válik.
Ha magas számítási kihasználtságot tapasztal, a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet számítási méretének növelése az adatbázis további számítási erőforrásokkal való biztosításához. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében. További információ: a [lekérdezés finomhangolása/célzása](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Ha az adatbázis-terület eléri a maximális méretkorlátot, az adatbázis-beszúrások és az adatméretet növelő frissítések sikertelenek [](sql-database-develop-error-messages.md)lesznek, és az ügyfelek hibaüzenetet kapnak. Az adatbázis kiválasztása és törlése a folytatás sikeres lesz.

A magas lemezterület-használat során a megoldás a következőkre terjed ki:

- Az adatbázis vagy a rugalmas készlet maximális méretének növelése, vagy további tárhely hozzáadása. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- Ha az adatbázis egy rugalmas készletben található, akkor azt is megteheti, hogy az adatbázist a készleten kívülre helyezi, hogy a tárolóhelye ne legyen megosztva más adatbázisokkal.
- Adatbázis zsugorítása a nem használt terület felszabadításához. További információ: [a tárterület kezelése a Azure SQL Databaseban](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Munkamenetek és feldolgozók (kérelmek)

A munkamenetek és a feldolgozók maximális számát a szolgáltatási szintek és a számítási méret (DTU és Edtu) határozzák meg. A rendszer elutasítja az új kérelmeket, ha a munkamenet-vagy feldolgozói korlátok elérésekor az ügyfelek hibaüzenetet kapnak. Míg az elérhető kapcsolatok száma az alkalmazás által szabályozható, az egyidejű feldolgozók száma gyakran nehezebb a becsléshez és a szabályozáshoz. Ez különösen igaz a maximális betöltési időszakok során, amikor az adatbázis-erőforrások korlátai elérik, és a feldolgozók a hosszabb ideig futó lekérdezések miatt halmoznak fel.

A magas munkamenet vagy munkavégző kihasználtsága esetén a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet szolgáltatási szintjeinek vagy számítási méretének növelése. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében, ha a munkavégzők nagyobb kihasználtságának oka a számítási erőforrások miatti kihasználása. További információ: a [lekérdezés finomhangolása/célzása](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Tranzakciós naplók arányának szabályozása 
A tranzakciós napló arányának szabályozása Azure SQL Database folyamat, amellyel korlátozható a nagy mennyiségű betöltési arány a számítási feladatok, például a tömeges Beszúrás, a kiválasztás és az index-buildek esetében. Ezeket a korlátokat nyomon követheti és érvényesítheti az almásodik szinten a naplózási rekordok létrehozásának arányában, az átviteli sebességet pedig attól függetlenül, hogy hány IOs-t lehet kiadni az adatfájlok ellen.  A tranzakciónapló-generálási díjak jelenleg lineárisan méretezhetők egy olyan pontra, amely a hardvertől függ, és a maximálisan megengedett naplózási sebesség 96 MB/s a virtuális mag beszerzési modellel. 

> [!NOTE]
> A tényleges fizikai IOs – tranzakciós naplófájlok nem szabályozottak vagy korlátozottak. 

A naplózási díjszabás úgy van beállítva, hogy különböző forgatókönyvekben legyenek elérhetők és fenntarthatók, míg a teljes rendszer a felhasználói terhelésnek való lehető legkisebbre csökkentheti a funkcionalitását. A naplózási sebesség szabályozása biztosítja, hogy a tranzakciónapló biztonsági mentései a közzétett helyreállító SLA-ban maradjanak.  Ez a szabályozás a másodlagos replikák túlzott lemaradását is megakadályozza.

A naplóbejegyzések létrehozásakor a rendszer minden egyes műveletet kiértékel és értékel ki, hogy késleltetve legyen-e a maximálisan szükséges naplózási sebesség (MB/s/másodperc) fenntartása érdekében. A rendszer nem adja hozzá a késéseket a naplófájlok tárolóba történő kiürítéséhez, hanem a naplózási sebesség szabályozására a naplózási arány létrehozásakor.

A tényleges log-generálási sebességet a futtatási időszakban is befolyásolhatja a visszajelzési mechanizmusok, ami átmenetileg csökkenti az engedélyezett naplók sebességét, így a rendszer képes stabilizálni. A naplófájlok kezelése, amelyekkel elkerülhető, hogy a naplózási terület feltételeit és a rendelkezésre állási csoport replikálási mechanizmusa átmenetileg csökkentse a teljes rendszerkorlátot. 

A log Rate kormányzó Traffic Shaping a következő várakozási típusok (a [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV) felületén keresztül van felszínben:

| Várakozás típusa | Megjegyzések |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Adatbázis korlátozása |
| POOL_LOG_RATE_GOVERNOR | Készlet korlátozása |
| INSTANCE_LOG_RATE_GOVERNOR | Példány szintjének korlátozása |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Visszajelzés-vezérlés, rendelkezésre állási csoport fizikai replikálása prémium/üzletileg kritikus nem kell tartani |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Visszajelzés-vezérlés, a díjszabás korlátozása, hogy elkerülje a naplón kívüli hely feltételeit |
|||

Ha egy, a kívánt skálázhatóságot akadályozó naplózási sebességre vonatkozó korlátot tapasztal, vegye figyelembe a következő lehetőségeket:
- A maximális 96 MB/s naplózási arány megszerzéséhez akár nagyobb szintet is felnagyíthat. 
- Ha a betöltés alatt álló adatmennyiség átmeneti, azaz egy ETL-folyamatban lévő átmeneti állapotú, akkor a tempdb-be (amely minimálisan naplózva van). 
- Elemzési forgatókönyvek esetén helyezzen betöltést egy fürtözött oszlopcentrikus-táblázatba. Ez csökkenti a szükséges naplózási sebességet a tömörítés miatt. Ezzel a technikával növelheti a CPU-kihasználtságot, és csak olyan adatkészletekre alkalmazható, amelyek kihasználják a fürtözött oszlopcentrikus indexeket. 

## <a name="next-steps"></a>További lépések

- Az általános Azure-korlátokkal kapcsolatos információkért lásd: Azure-előfizetések [és-szolgáltatások korlátai, kvótái és](../azure-subscription-service-limits.md)megkötései.
- További információ a DTU és a Edtu: [DTU és edtu](sql-database-purchase-models.md#dtu-based-purchasing-model).
- További információ a tempdb méretéről: [tempdb Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
