---
title: Monitor és hangolás - Hiperskála (Citus) - Azure Database for PostgreSQL
description: Ez a cikk az Azure Database for PostgreSQL – Hyperscale (Citus) figyelési és hangolási funkcióit ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975516"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Az Azure Database monitorozása és finomhangolása a PostgreSQL-hez – Hiperskála (Citus)

A kiszolgálók adatainak figyelése segít a számítási feladatok hibaelhárításával és optimalizálásával. A nagy kapacitású (Citus) különböző figyelési lehetőségeket biztosít a kiszolgálócsoport csomópontjainak viselkedésébe.

## <a name="metrics"></a>Mérőszámok

A nagy kapacitású (Citus) a kiszolgálócsoport minden egyes csomópontjának metrikáját biztosítja. A mérőszámok betekintést nyújtanak a támogató erőforrások viselkedésébe. Minden metrika egy perces gyakorisággal kerül kibocsátásra, és legfeljebb 30 napos előzményeket.

A mérőszámok grafikonjainak megtekintése mellett riasztásokat is konfigurálhat. A részletes útmutatásról a [Riasztások beállítása témakörben talál.](howto-hyperscale-alert-on-metric.md)  Egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések futtatása és az előzmények archiválása. További információt az [Azure Metrics áttekintése című témakörben talál.](../monitoring-and-diagnostics/monitoring-overview-metrics.md)

### <a name="list-of-metrics"></a>Mérőszámok listája

Ezek a metrikák a nagy méretű (Citus) csomópontokhoz érhetők el:

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Leírás|
|---|---|---|---|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|cpu_percent|PROCESSZOR százalék|Százalék|A használt processzor százalékos aránya.|
|iops|IOPS|Darabszám|Tekintse meg az [IOPS-definíciót](../virtual-machines/linux/premium-storage-performance.md#iops) és a [nagy kapacitású átviteli](concepts-hyperscale-configuration-options.md)|
|memory_percent|Memória százaléka|Százalék|A használt memória százalékos aránya.|
|network_bytes_ingress|Bejövő hálózat|Bájt|Hálózati kapcsolat az aktív kapcsolatok között.|
|network_bytes_egress|Kimenő hálózat|Bájt|Hálózat kikapcsolása az aktív kapcsolatok között.|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális tárhelyén kívüli tárhely százalékos aránya.|
|storage_used|Használt tároló|Bájt|A használatban lévő tárhely mennyisége. A szolgáltatás által használt tároló tartalmazhat adatbázisfájlokat, tranzakciónaplókat és kiszolgálónaplókat.|

Az Azure nem biztosít összesített metrikákat a fürt egészére vonatkozóan, de több csomópont metrikák at lehet helyezni ugyanazon a grafikonon.

## <a name="next-steps"></a>További lépések

- [Tekintse meg, hogyan állíthat be riasztásokat](howto-hyperscale-alert-on-metric.md) útmutatást egy metrika riasztásának létrehozásához.
