---
title: Az Azure Advisorsegítségével javíthatja az Azure-alkalmazások teljesítményét
description: Az Advisor használatával optimalizálhatja az Azure-telepítések teljesítményét.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443060"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Az Azure Advisorsegítségével javíthatja az Azure-alkalmazások teljesítményét

Az Azure Advisor teljesítményre vonatkozó javaslatai segítenek az üzleti legkritikusabb alkalmazások sebességének és válaszképességének javításában. Teljesítményjavaslatokat kaphat az Advisor-irányítópult Teljesítmény lapján az Advisor-irányítópult **teljesítményéről.**

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Csökkentse a TRAFFIC Manager-profilon való élő DNS-idő csökkentését, hogy gyorsabban átváltson az egészséges végpontokra

A Traffic Manager-profil [Live (TTL) beállításai](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) lehetővé teszik annak megadását, hogy milyen gyorsan váltson végpontok között, ha egy adott végpont nem válaszol a lekérdezésekre. A TTL-értékek csökkentése azt jelenti, hogy az ügyfelek gyorsabban lesznek irányítva a működő végpontokhoz.

Az Azure Advisor azonosítja a Traffic Manager-profilokat hosszabb TTL-beállítással, és azt javasolja, hogy a TTL-t 20 másodpercre vagy 60 másodpercre konfigurálja attól függően, hogy a profil [gyors feladatátvételhez](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)van-e konfigurálva.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Az adatbázis teljesítményének növelése az SQL DB Advisorral

Az Advisor egységes, összevont nézetet biztosít az összes Azure-erőforrással kapcsolatos javaslatokról. Az SQL Database Advisor használatával integrálható, hogy javaslatokat tegyen az SQL Azure-adatbázis teljesítményének javítására.Az SQL Database Advisor a használati előzmények elemzésével értékeli az SQL Azure-adatbázisok teljesítményét. Ezután javaslatokat, amelyek a legalkalmasabbak az adatbázis tipikus számítási feladatának futtatásához.

> [!NOTE]
> Javaslatok beszerezéséhez egy adatbázisnak körülbelül egy héthasználattal kell rendelkeznie, és ezen a héten belül konzisztens tevékenységnek kell lennie. Az SQL Database Advisor könnyebben optimalizálhat konzisztens lekérdezési mintákhoz, mint a véletlenszerű tevékenységsorozatokhoz.

Az SQL Database Advisor ról további információt az [SQL Database Advisor című témakörben talál.](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)

## <a name="improve-app-service-performance-and-reliability"></a>Az App Service teljesítményének és megbízhatóságának javítása

Az Azure Advisor integrálja az ajánlott eljárásokat az App Services felhasználói élményének javítására és a megfelelő platformképességek felfedezésére vonatkozó javaslatokban. Példák az App Services-javaslatokra:
* Olyan példányok észlelése, ahol a memória- vagy cpu-erőforrások kimerültek az alkalmazás futási lehetőségei által, kockázatcsökkentési beállításokkal.
* Olyan példányok észlelése, ahol az erőforrások, például a webalkalmazások és adatbázisok lefoglalása javíthatja a teljesítményt és csökkentheti a költségeket.

Az App Services-javaslatokról az [Azure App Service gyakorlati tanácsok](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)című témakörben talál további információt.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>A felügyelt lemezek használata a lemez I/O-szabályozásának megakadályozására

Az Advisor azonosítja azokat a virtuális gépeket, amelyek egy olyan tárfiókhoz tartoznak, amely eléri a méretezhetőségi célt. Ez az állapot a virtuális gépeket i/o-szabályozásra érzékenysé teszi. Az Advisor azt javasolja, hogy a teljesítményromlás megelőzése érdekében használjon felügyelt lemezeket.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A virtuálisgép-lemezek teljesítményének és megbízhatóságának javítása a Premium Storage használatával

Az Advisor azonosítja azokat a szabványos lemezekkel rendelkező virtuális gépeket, amelyek nagy mennyiségű tranzakcióval rendelkeznek a tárfiókban, és prémium szintű lemezekre való frissítést javasol. 

Az Azure Premium Storage nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújt az I/O-igényes számítási feladatokat futtató virtuális gépekszámára. A prémium szintű tárfiókokat használó virtuálisgép-lemezek adatokat tárolnak ssd-meghajtókon. Az alkalmazás legjobb teljesítménye érdekében azt javasoljuk, hogy telepítse át a magas IOPS-t igénylő virtuálisgép-lemezeket a prémium szintű tárhelyre.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Adatdöntés eltávolítása az SQL adattárház tábláján a lekérdezési teljesítmény növelése érdekében

Az adatdöntés szükségtelen adatmozgást vagy erőforrás-szűk keresztmetszeteket okozhat a munkaterhelés futtatásakor. Az Advisor észleli a 15%-nál nagyobb terjesztési adatok at, és javasolja az adatok újraterjesztését, majd a táblaterjesztési kulcs kiválasztásának újbóli átgondolását. A döntés azonosításáról és eltávolításáról a [Hibaelhárítás című](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)témakörben olvashat bővebben.

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Elavult táblastatisztikák létrehozása vagy frissítése az SQL adattárház táblában a lekérdezési teljesítmény növelése érdekében

Az Advisor azonosítja azokat a táblákat, amelyek nem rendelkeznek naprakész [táblastatisztikákkal,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) és javasolja a táblastatisztikák létrehozását vagy frissítését. Az SQL adattárház-lekérdezés-optimalizáló naprakész statikáksegítségével becsüli meg a lekérdezés eredményében lévő számosságot vagy sorok számát, amely lehetővé teszi, hogy a lekérdezésoptimalizáló kiváló minőségű lekérdezési tervet hozzon létre a leggyorsabb teljesítmény érdekében.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skálázás az SQL Data Warehouse-táblák gyorsítótár-kihasználtságának optimalizálásához a lekérdezési teljesítmény növelése érdekében

Az Azure Advisor észleli, ha az SQL Data Warehouse magas gyorsítótár-használt százalékos és alacsony találati százalék. Ez a feltétel magas gyorsítótár-kilakoltatást jelez, ami hatással lehet az SQL Data Warehouse teljesítményére. Az Advisor azt javasolja, hogy skálázza fel az SQL Data Warehouse-t, hogy elegendő gyorsítótár-kapacitást biztosítson a számítási feladatokhoz.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse-táblák átalakítása replikált táblákká a lekérdezési teljesítmény növelése érdekében

Az Advisor azonosítja azokat a táblákat, amelyek nem replikált táblák, de előnyös lenne a konvertálás, és javasolja a táblák konvertálását. A javaslatok a replikált tábla méretén, az oszlopok számán, a táblaterjesztési típusán és az SQL Data Warehouse tábla partícióinak számán alapulnak. A kontextusra vonatkozó ajánlásban további heurisztikák is rendelkezésre állhatnak. Ha többet szeretne tudni arról, hogyan határozza meg ezt a javaslatot, olvassa el az SQL Data Warehouse javaslatok című [témakört.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables) 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>A tárfiók áttelepítése az Azure Resource Managerbe az Azure legújabb Azure-funkcióinak beszerezéséhez

Telepítse át a tárfiók üzembe helyezési modelljét az Azure Resource Managerbe (Resource Manager) a sablontelepítések, a további biztonsági beállítások és a GPv2-fiókra való frissítés előnyeinek kihasználásához az Azure Storage legújabb funkcióinak kihasználásához. Az Advisor azonosítja a klasszikus üzembe helyezési modellt használó önálló tárfiókokat, és javasolja az Erőforrás-kezelő telepítési modelljére való áttelepítést.

> [!NOTE]
> Az Azure Monitor klasszikus riasztásai 2019 augusztusában kilettek vonva. Azt javasoljuk, hogy frissítse a klasszikus tárfiókot, hogy az Erőforrás-kezelő használatával megőrizze a riasztási funkciót az új platformmal. További információ: [Classic Alerts Retirement](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Tervezze meg tárfiókjait, hogy ne üsse meg a maximális előfizetési korlátot

Egy Azure-régió előfizetésenként legfeljebb 250 tárfiókot támogathat. A korlát elérése után nem hozhat létre további tárfiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket és a felületi javaslatokat, hogy kevesebb tárfiókot tervezhessen olyan fiókokhoz, amelyek közel vannak a maximális korlát eléréséhez.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimalizálja az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB-kiszolgálók teljesítményét 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Javítsa ki az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB-kiszolgálók CPU-terhelését a CPU-szűk keresztmetszetekkel
A processzor hosszú időn keresztül inagyon magas kihasználtsága lassú lekérdezési teljesítményt okozhat a számítási feladatokhoz. A processzor méretének növelése segít az adatbázis-lekérdezések futásidejének optimalizálásában és az általános teljesítmény javításában. Az Azure Advisor azonosítja a magas CPU-kihasználtsítással rendelkező kiszolgálókat, amelyek valószínűleg cpu-korlátozott számítási feladatokat futtatnak, és a számítási feladatok méretezését javasolja.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Csökkentse a memóriakorlátokat az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB kiszolgálókon, vagy helyezze át a memóriára optimalizált termékváltozatra
Az alacsony gyorsítótár-leolvasási arány lassabb lekérdezési teljesítményt és megnövekedett IOPS-t eredményezhet. Ennek oka lehet egy rossz lekérdezési terv, vagy egy memóriaigényes munkaterhelés futtatása. A lekérdezési terv javítása vagy az Azure Database for PostgreSQL adatbázis-kiszolgáló, az Azure MySQL adatbázis-kiszolgáló vagy az Azure MariaDB-kiszolgáló [memóriájának növelése](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) segít optimalizálni az adatbázis-munkaterhelés végrehajtását. Az Azure Advisor azonosítja a magas pufferkészlet-lemorzsolódás miatt érintett kiszolgálókat, és azt javasolja, hogy javítsa ki a lekérdezési tervet, és egy nagyobb memóriával rendelkező termékváltozatra kerüljön, vagy növelje a tárhely méretét, hogy több IOPS-t kapjon.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Azure MySQL vagy Azure PostgreSQL olvasási replika használata az olvasási igényű munkaterhelések horizontális felskálázásához
Az Azure Advisor kihasználja a számítási feladatokon alapuló heurisztikát, például az olvasások és a kiszolgálón az írások arányát az elmúlt hét napban az olvasásigényes számítási feladatok azonosításához. Az Azure-adatbázis PostgreSQL-erőforrás vagy Azure-adatbázis MySQL-erőforrás nagyon magas olvasási/írási arány vezethet CPU és/vagy memória versengések, ami lassú lekérdezési teljesítmény. Egy [replika](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) hozzáadása segít a replikakiszolgáló olvasásainak horizontális felskálázásában, megakadályozva a PROCESSZOR és/vagy a memória megszorítását az elsődleges kiszolgálón. Az Advisor azonosítja az ilyen nagy olvasási igényű számítási feladatokat tartalmazó kiszolgálókat, és javasolja egy [olvasási replika](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) hozzáadását az olvasási számítási feladatok egy részének kiszervezéséhez.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Az Azure MySQL, az Azure PostgreSQL vagy az Azure MariaDB kiszolgáló méretezése magasabb termékváltozatra a kapcsolatkorlátozások megelőzése érdekében
Az adatbázis-kiszolgálóval létesített minden új kapcsolat helyet foglal el. Az adatbázis-kiszolgáló teljesítménye romlik, ha a kiszolgálóval létesített kapcsolatok a memória [felső határa](https://docs.microsoft.com/azure/postgresql/concepts-limits) miatt megszakadnak. Az Azure Advisor azonosítja a számos csatlakozási hibával működő kiszolgálókat, és javasolja a kiszolgáló kapcsolati korlátainak frissítését, hogy több memóriát biztosítson a kiszolgálónak a számítási kapacitás skálázásával vagy a memóriaoptimalizált sk-ek használatával, amelyek magonként több számítással rendelkeznek.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>A gyorsítótár méretezése más méretre vagy termékváltozatra a gyorsítótár és az alkalmazások teljesítményének javítása érdekében

A gyorsítótár-példányok akkor teljesítenek a legjobban, ha nem futnak nagy memórianyomás, nagy kiszolgálóterhelés vagy nagy hálózati sávszélesség alatt, ami miatt nem válaszolnak, adatvesztést tapasztalnak, vagy elérhetetlenné válnak. Az Advisor ezekben a feltételekben azonosítja a gyorsítótár-példányokat, és ajánlott eljárásokat javasol a memórianyomás, a kiszolgáló terhelésének vagy a hálózati sávszélesség csökkentéséhez vagy más méretűre vagy nagyobb kapacitású termékváltozatra történő méretezése érdekében.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Régiók hozzáadása az Azure Cosmos DB-fiókjához forgalommal

Az Advisor észleli az Azure Cosmos DB-fiókokat, amelyek forgalma t egy régióból, amely jelenleg nincs konfigurálva, és azt javasolja, hogy adja hozzá a régióban. Ez javítja az adott régióból érkező kérelmek késését, és biztosítja a rendelkezésre állást a régió kimaradása esetén. [További információ az Azure Cosmos DB globális adatterjesztéséről](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurálja az Azure Cosmos DB indexelési szabályzatát az ügyfél által biztosított vagy kizárt elérési utakkal

Az Azure Advisor azonosítja a Cosmos DB-tárolókat, amelyek az alapértelmezett indexelési szabályzatot használják, de a számítási feladatok minta alapján egyéni indexelési szabályzat előnyeit élvezhetik. Az alapértelmezett indexelési házirend indexeli az összes tulajdonságot, de egy egyéni indexelési házirend használatával explicit foglalt vagy kizárt elérési utak használt lekérdezési szűrők csökkentheti a R.Us és a tárolás használt indexelés. [További információ az indexházirendek módosításáról](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Az Azure Cosmos DB lekérdezési lapméretének (MaxItemCount) beállítása -1 értékre 

Az Azure Advisor azonosítja az Azure Cosmos DB-tárolókat, amelyek a 100-as lekérdezési oldalméretet használják, és a gyorsabb vizsgálatok érdekében -1 oldalméretet fog használni. [További információ a maximális cikkszámról](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>A teljesítményre vonatkozó ajánlások elérése az Advisorban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

2.  Az Advisor irányítópultján kattintson a **Teljesítmény** fülre.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Advisor-ajánlásokról, olvassa el a következő témakört:

* [Bevezetés a tanácsadó](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
* [Tanácsadó magas rendelkezésre állásra vonatkozó ajánlásai](advisor-high-availability-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Az Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md)
