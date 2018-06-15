---
title: Az Azure-adatbázis PostgreSQL figyelése
description: Ez a cikk ismerteti a figyelés és riasztás Azure-adatbázis PostgreSQL, beleértve a CPU, a tároló és a kapcsolat statisztika metrikáit.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d0a57fe6d7b1040c32f6d67e2bf0259176c72099
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687608"
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Az Azure-adatbázis PostgreSQL figyelése
A kiszolgálók adatainak segít hibaelhárítása, és optimalizálja a munkaterhelés számára. Azure-adatbázis PostgreSQL különböző metrikákat, amelyek segítségével a PostgreSQL-kiszolgáló támogató erőforrásokban viselkedését betekintést biztosít. 

## <a name="metrics"></a>Mérőszámok
Minden Azure metrikák rendelkezik egy perces gyakoriságot, és mindegyik metrikát előzmények 30 napos biztosít. Riasztások a metrikák a konfigurálható. Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). Egyéb feladatok közé tartozik a beállítása automatikus műveleteket, speciális elemzés végrehajtásához, és az archiválásra előzmények. További információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Metrikák listája
A metrikák PostgreSQL Azure-adatbázis érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|A százalékos CPU használja.|
|memory_percent|Memória százaléka|Százalék|A használt memória aránya.|
|io_consumption_percent|IO-százaléka|Százalék|A százalékos aránya IO használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgáló külön tárolási százaléka által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használt tárolási mennyisége. A szolgáltatás által használt tároló tartalmazza, az adatbázisfájlokat, a tranzakciós naplók és a kiszolgáló naplóiban.|
|storage_limit|Tárolási kapacitása|Bájt|A maximális tárolási ehhez a kiszolgálóhoz.|
|active_connections|Aktív kapcsolatok száma|Darabszám|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|


## <a name="next-steps"></a>További lépések
- Lásd: [riasztások beállításával](howto-alert-on-metric.md) metrika riasztást hoz létre a segítségükkel.
- A hozzáférés, és az Azure-portálon, a REST API-t vagy a CLI metrikák exportálása további információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
