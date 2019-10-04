---
title: Figyelés az Azure Database for MariaDB
description: Ez a cikk ismerteti a monitorozási és riasztási az Azure Database for MariaDB, többek között a Processzor, a storage és a kapcsolat statisztikai mérőszámait.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: fb998edffed290bb7bc59945163f0fd48c55cbf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612531"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Figyelés az Azure Database for MariaDB
A kiszolgálók adatainak segítségével hibaelhárítása és optimalizálhatja a számítási feladatok számára. Azure Database for MariaDB különböző mérőszámokat, amelyek a kiszolgáló viselkedését betekintést biztosít.

## <a name="metrics"></a>Mérőszámok
Az összes Azure-metrikagyűjtéshez rendelkezik egy egy perces gyakoriságot, és mindegyik metrikát biztosít előzmények 30 nap. A metrikákhoz riasztásokat is beállíthatja. Egyéb feladatok közé tartozik, automatizált műveletek, speciális analitikai végrehajtása és archiváláshoz előzmények. További információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Lépésenkénti útmutatásért lásd: [riasztások beállítása](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a metrikák az Azure Database for MariaDB érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Percent|Processzorhasználat százalékos használja.|
|memory_percent|Memória százalékos aránya|Percent|Használt memória aránya.|
|io_consumption_percent|I/o-százalék|Percent|Százalékos i/o-használja.|
|storage_percent|Tárolási százalékos aránya|Percent|A kiszolgálón kívül felhasznált tárterület százalékos által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mérete. A szolgáltatás által használt tárterület tartalmazhatják az adatbázisfájlokat, tranzakciós naplókhoz és a webkiszolgáló-naplókkal.|
|serverlog_storage_percent|Server Log storage százalékban|Percent|A százalékos aránya ki a kiszolgáló maximális kiszolgálói naplók tárolására használt kiszolgálói naplók tárolásához.|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Kiszolgálói naplók tárolására használt mennyisége.|
|serverlog_storage_limit|Log storage maximális|Bájt|A maximális kiszolgálói naplók tárolásához, ehhez a kiszolgálóhoz.|
|storage_limit|Tárolási kapacitása|Bájt|Ez a kiszolgáló maximális tárterülete.|
|active_connections|Az aktív kapcsolatok|Count|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Count|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|Kimenő hálózati forgalom az aktív kapcsolatok között.|
|network_bytes_ingress|Bejövő hálózat|Bájt|Hálózati az aktív kapcsolatok között.|

## <a name="server-logs"></a>Kiszolgálói naplók

Engedélyezheti a naplózást a kiszolgáló a lassú lekérdezések. Ezek a naplók keresztül is elérhetőek az Azure diagnosztikai naplók a Azure Monitor naplók, az Event Hubs és a Storage-fiókot. Naplózásával kapcsolatos további tudnivalókért látogasson el a [kiszolgálónaplók](concepts-server-logs.md) lapot.

## <a name="query-store"></a>Lekérdezéstár

[Query Store](concepts-query-store.md) nyilvános előzetes verziójú funkció, amely nyomon követi a lekérdezési teljesítményt idő többek között lekérdezés futásidejének statisztikai adatait, és várjon eseményeket. A funkció továbbra is fennáll, lekérdezés futásidejű teljesítményadatait a a **mysql** séma. Szabályozhatja, hogy a gyűjtemény és az adatok különböző konfigurációs belül keresztül.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző

[Lekérdezési Terheléselemző](concepts-query-performance-insight.md) Query Store az Azure Portalon elérhető képi megjelenítésekhez együtt működik. Ezekbe a diagramokba engedélyezése elsődlegeskulcs-lekérdezések azonosíthatja, hogy hatással lehet a teljesítményre. Lekérdezési Terheléselemző nyilvános előzetes verzióban érhető el, és elérhető a **intelligens teljesítmény** az Azure Database for MariaDB kiszolgáló portáloldalán szakaszában.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) szolgáltatás lehetőségeket biztosít a számítási feladatok teljesítményének javítása azonosítja. Teljesítménnyel kapcsolatos javaslatok a nyilvános előzetes verziója biztosít, amelyek a számítási feladatok teljesítményének javítása érdekében új indexek létrehozására vonatkozó javaslatok. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában.

## <a name="next-steps"></a>További lépések

- Elérése és exportálása a metrikák az Azure Portalon, a REST API vagy a parancssori felület használatával kapcsolatos további információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Lásd: [riasztások beállítása](howto-alert-metric.md) riasztás létrehozása a metrika az útmutatást.
