---
title: A MySQL az Azure-adatbázis figyelése
description: Ez a cikk ismerteti a figyelés és riasztás Azure-adatbázis MySQL, beleértve a CPU, a tároló és a kapcsolat statisztika metrikáit.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: af90fb4c89cf552d4c5637db08ef0acd9984b31b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264697"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>A MySQL az Azure-adatbázis figyelése
A kiszolgálók adatainak segít hibaelhárítása, és optimalizálja a munkaterhelés számára. Azure MySQL-adatbázis különböző metrikákat, amelyek segítségével a MySQL-kiszolgáló támogató erőforrásokban viselkedését betekintést nyújt. 

## <a name="metrics"></a>Mérőszámok
Minden Azure metrikák rendelkezik egy perces gyakoriságot, és mindegyik metrikát előzmények 30 napos biztosít. Riasztások a metrikák a konfigurálható. Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). Egyéb feladatok közé tartozik a beállítása automatikus műveleteket, speciális elemzés végrehajtásához, és az archiválásra előzmények. További információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Metrikák listája
A metrikák MySQL az Azure-adatbázis érhetők el:

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
