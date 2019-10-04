---
title: Figyelése és finomhangolása az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk ismerteti a figyelési és hangolási funkciók az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c69ffb30a37de8e6dc3e15aa1f7dcd6a9311d614
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274302"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Figyelése és finomhangolása az Azure Database for PostgreSQL - kiszolgáló egyetlen
A kiszolgálók adatainak segítségével hibaelhárítása és optimalizálhatja a számítási feladatok számára. Azure Database for PostgreSQL nyújt betekintést a kiszolgáló viselkedését a különböző megfigyelési lehetőségeket biztosít.

## <a name="metrics"></a>Mérőszámok
Azure Database for postgresql-hez különböző mérőszámokat, amelyek a PostgreSQL-kiszolgáló támogató erőforrásokban viselkedését betekintést biztosít. Mindegyik metrikát bocsásson ki egy egy perces gyakorisággal, és akár 30 napig előzmények rendelkezik. A metrikákhoz riasztásokat is beállíthatja. Lépésenkénti útmutatásért lásd: [riasztások beállítása](howto-alert-on-metric.md). Egyéb feladatok közé tartozik, automatizált műveletek, speciális analitikai végrehajtása és archiváláshoz előzmények. További információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a metrikák érhetők el az Azure Database for postgresql-hez:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Processzorhasználat százalékos használja.|
|memory_percent|Memória százalékos aránya|Százalék|Használt memória aránya.|
|io_consumption_percent|I/o-százalék|Százalék|Százalékos i/o-használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgálón kívül felhasznált tárterület százalékos által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mérete. A szolgáltatás által használt tárterület tartalmazhatják az adatbázisfájlokat, tranzakciós naplókhoz és a webkiszolgáló-naplókkal.|
|storage_limit|Tárolási kapacitása|Bájt|Ez a kiszolgáló maximális tárterülete.|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|A százalékos aránya ki a kiszolgáló maximális kiszolgálói naplók tárolására használt kiszolgálói naplók tárolásához.|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Kiszolgálói naplók tárolására használt mennyisége.|
|serverlog_storage_limit|Log storage maximális|Bájt|A maximális kiszolgálói naplók tárolásához, ehhez a kiszolgálóhoz.|
|active_connections|Az aktív kapcsolatok|Darabszám|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Count|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|Kimenő hálózati forgalom az aktív kapcsolatok között.|
|network_bytes_ingress|Bejövő hálózat|Bájt|Hálózati az aktív kapcsolatok között.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tárterület mennyisége.|
|pg_replica_log_delay_in_bytes|Maximális késés replika között|Bájt|A lag a master és a legtöbb elmaradt replika közötti bájtban. Ez a metrika csak a fő kiszolgálón érhető el.|
|pg_replica_log_delay_in_seconds|Replika késés|másodperc|Az idő, mert az utolsó játssza vissza a tranzakciót. Ez a metrika csak a replikakiszolgálót érhető el.|

## <a name="server-logs"></a>Kiszolgálói naplók
Engedélyezheti a naplózást a kiszolgálón. Ezek a naplók is elérhetők az Azure-diagnosztikai naplók keresztül [naplózza az Azure Monitor](../azure-monitor/log-query/log-query-overview.md), az Event Hubs és a Storage-fiókot. Naplózásával kapcsolatos további tudnivalókért látogasson el a [kiszolgálónaplók](concepts-server-logs.md) lapot.

## <a name="query-store"></a>Lekérdezéstár
[Query Store](concepts-query-store.md) nyomon követi a lekérdezési teljesítményt idő többek között lekérdezés futásidejének statisztikai adatait, és várjon eseményeket. A funkció továbbra is fennáll, lekérdezés futásidejű teljesítményadatait nevű rendszer-adatbázisok **azure_sys** a query_store séma alapján. Szabályozhatja, hogy a gyűjtemény és az adatok különböző konfigurációs belül keresztül.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
[Lekérdezési Terheléselemző](concepts-query-performance-insight.md) Query Store az Azure Portalon elérhető képi megjelenítésekhez együtt működik. Ezekbe a diagramokba engedélyezése elsődlegeskulcs-lekérdezések azonosíthatja, hogy hatással lehet a teljesítményre. Lekérdezési teljesítmény Insightis elérhető a **támogatás + hibaelhárítás** az Azure Database for PostgreSQL-kiszolgáló portáloldalán szakaszában.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) szolgáltatás lehetőségeket biztosít a számítási feladatok teljesítményének javítása azonosítja. Teljesítménnyel kapcsolatos javaslatok, amelyek a számítási feladatok teljesítményének javítása érdekében új indexek létrehozására vonatkozó javaslatokat nyújt. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában. 

## <a name="next-steps"></a>További lépések
- Lásd: [riasztások beállítása](howto-alert-on-metric.md) riasztás létrehozása a metrika az útmutatást.
- Elérése és exportálása a metrikák az Azure Portalon, a REST API vagy a parancssori felület használatával kapcsolatos további információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Blogunkból a [ajánlott eljárások a server figyelési](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
