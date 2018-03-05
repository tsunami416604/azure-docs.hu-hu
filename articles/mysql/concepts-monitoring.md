---
title: "A MySQL az Azure-adatbázis figyelése"
description: "Ez a cikk ismerteti a figyelés és riasztás Azure-adatbázis MySQL, beleértve a CPU, a korlátozások, a tároló és a kapcsolat statisztika metrikáit."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7ecfb8151cd81fb588f964fdfa3a74aacab24874
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>A MySQL az Azure-adatbázis figyelése
A kiszolgálók adatainak segít hibaelhárítása, és optimalizálja a munkaterhelés számára. Azure MySQL-adatbázis különböző metrikákat, amelyek segítségével a MySQL-kiszolgáló támogató erőforrásokban viselkedését betekintést nyújt. 

## <a name="metrics"></a>Mérőszámok
Minden Azure metrikák rendelkezik egy perces gyakoriságot, és mindegyik metrikát előzmények 30 napos biztosít. 

Riasztások a metrikák a konfigurálható. Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). 

Egyéb feladatok közé tartozik a beállítása automatikus műveleteket, speciális elemzés végrehajtásához, és az archiválásra előzmények. További információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Metrikák listája
A metrikák MySQL az Azure-adatbázis érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|A százalékos CPU használja.|
|compute_limit|Számítási egység korlátot|Darabszám|Ez a kiszolgáló számítási egységek maximális száma|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|A százalékos számítási egységek külön a kiszolgáló által legnagyobb.|
|memory_percent|Memória százaléka|Százalék|A használt memória aránya.|
|io_consumption_percent|IO-százaléka|Százalék|A százalékos aránya IO használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgáló külön tárolási százaléka által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használt tárolási mennyisége. A szolgáltatás által használt tároló tartalmazza, az adatbázisfájlokat, a tranzakciós naplók és a kiszolgáló naplóiban.|
|storage_limit|Tárolási kapacitása|Bájt|A maximális tárolási ehhez a kiszolgálóhoz.|
|active_connections|Aktív kapcsolatok száma|Darabszám|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|


## <a name="next-steps"></a>További lépések
- Lépésenkénti útmutatásért lásd: [riasztások beállításával](howto-alert-on-metric.md). 
- A hozzáférés, és az Azure-portálon, a REST API-t vagy a CLI metrikák exportálása további információkért lásd: a [Azure metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
