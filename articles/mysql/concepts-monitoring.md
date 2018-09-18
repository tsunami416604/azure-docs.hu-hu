---
title: Figyelés az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a monitorozási és riasztási az Azure Database for MySQL, többek között a Processzor, a storage és a kapcsolat statisztikai mérőszámait.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: 3c9e0acfe9f8ffb159cacf50923f249be175ea77
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981934"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Figyelés az Azure Database for MySQL-hez
A kiszolgálók adatainak segítségével hibaelhárítása és optimalizálhatja a számítási feladatok számára. Azure Database for MySQL-hez különböző mérőszámokat, amelyek a MySQL-kiszolgáló támogató erőforrásokban viselkedését betekintést biztosít. 

## <a name="metrics"></a>Mérőszámok
Az összes Azure-metrikagyűjtéshez rendelkezik egy egy perces gyakoriságot, és mindegyik metrikát biztosít előzmények 30 nap. A metrikákhoz riasztásokat is beállíthatja. Lépésenkénti útmutatásért lásd: [riasztások beállítása](howto-alert-on-metric.md). Egyéb feladatok közé tartozik, automatizált műveletek, speciális analitikai végrehajtása és archiváláshoz előzmények. További információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a metrikák érhetők el az Azure Database for MySQL-hez:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Processzorhasználat százalékos használja.|
|memory_percent|Memória százalékos aránya|Százalék|Használt memória aránya.|
|io_consumption_percent|I/o-százalék|Százalék|Százalékos i/o-használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgálón kívül felhasznált tárterület százalékos által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mérete. A szolgáltatás által használt tárterület tartalmazhatják az adatbázisfájlokat, tranzakciós naplókhoz és a webkiszolgáló-naplókkal.|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|A százalékos aránya ki a kiszolgáló maximális kiszolgálói naplók tárolására használt kiszolgálói naplók tárolásához.|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Kiszolgálói naplók tárolására használt mennyisége.|
|serverlog_storage_limit|Log storage maximális|Bájt|A maximális kiszolgálói naplók tárolásához, ehhez a kiszolgálóhoz.|
|storage_limit|Tárolási kapacitása|Bájt|Ez a kiszolgáló maximális tárterülete.|
|active_connections|Az aktív kapcsolatok|Darabszám|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|
|seconds_behind_master|Replikációs késés másodpercben|Darabszám|Az adatbázisreplika-kiszolgálót a fő kiszolgálón van elmaradt másodpercek számát.|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Kimenő hálózati forgalom az aktív kapcsolatok között.|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Hálózati az aktív kapcsolatok között.|

## <a name="next-steps"></a>További lépések
- Lásd: [riasztások beállítása](howto-alert-on-metric.md) riasztás létrehozása a metrika az útmutatást.
- Elérése és exportálása a metrikák az Azure Portalon, a REST API vagy a parancssori felület használatával kapcsolatos további információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
