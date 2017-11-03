---
title: "Megfigyelés az Azure-adatbázis PostgreSQL |} Microsoft Docs"
description: "Ez a cikk ismerteti a figyelés és riasztás Azure-adatbázis PostgreSQL, beleértve a CPU, a korlátozások, a tároló és a kapcsolat statisztika metrikáit."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: d48159fbc5e52bf1916a744e3912ca7ceb0ab60f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Az Azure-adatbázis PostgreSQL figyelése
A kiszolgálók adatainak segít hibaelhárítása, és optimalizálja a munkaterhelés számára. Azure-adatbázis PostgreSQL különböző metrikákat, amelyek segítségével a PostgreSQL-kiszolgáló támogató erőforrásokban viselkedését betekintést biztosít. 

## <a name="metrics"></a>Mérőszámok
Minden Azure metrikák rendelkezik egy perces gyakoriságot, és mindegyik metrikát előzmények 30 napos biztosít. 

Riasztások a metrikák a konfigurálható. Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). 

Egyéb feladatok közé tartozik a beállítása automatikus műveleteket, speciális elemzés végrehajtásához, és az archiválásra előzmények. További információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Metrikák listája
A metrikák PostgreSQL Azure-adatbázis érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|A százalékos CPU használja.|
|compute_limit|Számítási egység korlátot|Darabszám|Ez a kiszolgáló számítási egységek maximális száma|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|A százalékos számítási egységek külön a kiszolgáló által legnagyobb.|
|memory_percent|Memória százaléka|Százalék|A használt memória aránya.|
|io_consumption_percent|IO-százaléka|Százalék|A százalékos aránya IO használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgáló külön tárolási százaléka által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájtok|A használt tárolási mennyisége. A szolgáltatás által használt tároló tartalmazza, az adatbázisfájlokat, a tranzakciós naplók és a kiszolgáló naplóiban.|
|storage_limit|Tárolási kapacitása|Bájtok|A maximális tárolási ehhez a kiszolgálóhoz.|
|active_connections|Aktív kapcsolatok száma|Darabszám|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|


> [!NOTE]
> Számítási egység áll memória és CPU-e. A számítási egység százalékos értéke maximális (memória %-ot, cpu %). Vizsgálja meg a rögzítési ponthoz, amely számítási egység százalékos módosítások még hozzájárul a memória- és diagramokat. További információkért lásd: [számítási egység](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Következő lépések
- Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). 
- A hozzáférés, és az Azure-portálon, a REST API-t vagy a CLI metrikák exportálása további információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
