---
title: Az Azure-alkalmazások teljesítményének javítása az Advisor szolgáltatással
description: Az üzleti szempontból kritikus fontosságú alkalmazások sebességének és reagálásának javítása érdekében Azure Advisor teljesítményre vonatkozó javaslatokat használhat.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 7ecd6a45dc255f4748ed5074a3adb3d948f4122e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057579"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Az Azure-alkalmazások teljesítményének növelése Azure Advisor használatával

A Azure Advisor teljesítményével kapcsolatos javaslatok segíthetnek az üzleti szempontból kritikus fontosságú alkalmazások sebességének és reagálásának javításában. Az Advisor teljesítményére vonatkozó javaslatokat az Advisor irányítópult **teljesítmény** lapján érheti el.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Csökkentse a DNS-alapú élettartamot a Traffic Manager-profilon, hogy gyorsabban átadja a feladatokat a gyors végpontoknak

Az Azure Traffic Manager profiljában használhat [élettartam (TTL) beállításokat](../traffic-manager/traffic-manager-performance-considerations.md) annak megadásához, hogy a végpontok milyen gyorsan legyenek átváltva, ha egy adott végpont nem válaszol a lekérdezésekre. Ha csökkenti a TTL-értékeket, az ügyfelek gyorsabban lesznek irányítva a működő végpontokra.

A Azure Advisor olyan Traffic Manager-profilokat azonosít, amelyekhez már van beállítva TTL. Azt javasolja, hogy az ÉLETTARTAMot 20 másodpercre vagy 60 másodpercre konfigurálja, attól függően, hogy a profil konfigurálva van-e a [Fast Failoverra](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor"></a>Az adatbázis teljesítményének javítása SQL Database Advisor használatával

Azure Advisor egységes, összevont áttekintést nyújt az összes Azure-erőforrásra vonatkozó javaslatokról. A SQL Database Advisor integrálható az adatbázisok teljesítményének javítására vonatkozó javaslatok bevezetéséhez.SQL Database Advisor a használati előzmények elemzésével értékeli az adatbázisok teljesítményét. Ezután ajánlásokat nyújt az adatbázis tipikus számítási feladatainak futtatásához.

> [!NOTE]
> A javaslatok beszerzése előtt az adatbázist körülbelül egy hétig kell használni, és ezen a héten belül bizonyos konzisztens tevékenységre van szükség. A SQL Database Advisor könnyebben optimalizálható a konzisztens lekérdezési mintáknál, mint a tevékenységek véletlenszerű kitörése esetén.

További információ: [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>A jobb megbízhatóság és teljesítmény érdekében frissítse a Storage ügyféloldali kódtárat a legújabb verzióra

A Storage ügyféloldali kódtár SDK legújabb verziója az ügyfelek által jelentett problémákra vonatkozó javításokat tartalmaz, és proaktív módon azonosítja a MINŐSÉGBIZTOSÍTÁSi folyamaton keresztül. A legújabb verzió megbízhatóságot és teljesítmény-optimalizálást is biztosít az Azure Storage használatának általános élményét javító új funkciókkal együtt. Az Advisor olyan javaslatokat és lépéseket tartalmaz, amelyek szükségesek ahhoz, hogy az SDK legújabb verziójára frissítsen, ha elavult verziót használ. A javaslatok a következő támogatott nyelvekre vonatkoznak: C++ és .NET.

## <a name="improve-app-service-performance-and-reliability"></a>A App Service teljesítményének és megbízhatóságának javítása

A Azure Advisor a App Service élményének javítására és a kapcsolódó platform képességeinek felfedezésére vonatkozó javaslatokat integrál. Példák App Service javaslatokra:
* Azon példányok észlelése, amelyekben a memória-vagy a CPU-erőforrások kimerültek az alkalmazás-futtatókörnyezetek által, a kockázatcsökkentő beállításokkal.
* Azon példányok észlelése, amelyekben az erőforrások, például a webalkalmazások és az adatbázisok közös elhelyezésével javíthatja a teljesítményt és csökkentheti a költségeket.

További információ: [ajánlott eljárások Azure app Servicehoz](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Felügyelt lemezek használata a lemez I/O-szabályozásának megakadályozása érdekében

Az Advisor azokat a virtuális gépeket azonosítja, amelyek egy olyan Storage-fiókhoz tartoznak, amely eléri a méretezhetőségi célját. Ez az állapot az I/O-szabályozásra fogékony virtuális gépeket tesz elérhetővé. Az Advisor azt javasolja, hogy felügyelt lemezeket használjanak a teljesítmény romlásának megelőzése érdekében.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A virtuális gépek lemezeinek teljesítményének és megbízhatóságának javítása Premium Storage használatával

Az Advisor a szabványos lemezekkel rendelkező virtuális gépeket azonosítja, amelyek nagy mennyiségű tranzakcióval rendelkeznek a Storage-fiókban. Azt javasolja, hogy a prémium szintű lemezekre frissítsen. 

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az I/O-igényű számítási feladatokat futtató virtuális gépekhez. A Premium Storage fiókokat használó virtuális gépek lemezei SSD-meghajtókon tárolják az adattárolást. Az alkalmazás legjobb teljesítményének érdekében javasoljuk, hogy telepítse át a Premium Storage magas IOPS igénylő virtuálisgép-lemezeket.

## <a name="remove-data-skew-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez távolítsa el az adattorzítást a SQL Data Warehouse táblákon

Az adatok eldöntése szükségtelen adatáthelyezést vagy erőforrás-szűk keresztmetszetet okozhat a számítási feladatok futtatásakor. Az Advisor több mint 15%-os eloszlási adatmennyiséget észlel. Azt javasolja, hogy terjessze újra az adatait, és nyissa meg újra a tábla terjesztési kulcsának beállításait. Ha többet szeretne megtudni a ferdeség azonosításáról és eltávolításáról, tekintse meg a következő témakört: [Hibaelhárítás](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-sql-data-warehouse-tables-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez hozzon létre vagy frissítsen elavult tábla statisztikáit a SQL Data Warehouse tábláiban

Az Advisor olyan táblákat azonosít, amelyek nem rendelkeznek naprakész [táblázat statisztikával](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) , és a statisztikák létrehozását és frissítését javasolja. A Azure SQL Data Warehouse lekérdezés-optimalizáló naprakész statisztikát használ a lekérdezési eredményekben található sorok többségének vagy számának becsléséhez. Ezek a becslések lehetővé teszik a lekérdezés-optimalizáló számára, hogy lekérdezési tervet hozzon létre a leggyorsabb teljesítmény biztosításához.

## <a name="improve-mysql-connection-management"></a>A MySQL-kapcsolatkezelés fejlesztése

Az Advisor Analysis azt jelezheti, hogy az alkalmazás MySQL-kiszolgálóhoz való csatlakozása nem hatékony a kapcsolatok kezeléséhez. Ez az állapot szükségtelen erőforrás-felhasználást és általános, magasabb alkalmazások késését eredményezheti. A kapcsolatkezelés javítása érdekében javasoljuk, hogy csökkentse a rövid élettartamú kapcsolatok számát, valamint szüntesse meg a felesleges tétlen kapcsolatokat. Ezeket a funkciókat kiszolgálóoldali kapcsolatok Pooler, például a ProxySQL konfigurálásával végezheti el.


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Vertikális felskálázás a gyorsítótár kihasználtságának optimalizálásához a SQL Data Warehouse táblákon a lekérdezési teljesítmény növeléséhez

Azure Advisor észleli, hogy a SQL Data Warehouse-táblák nagy gyorsítótárban használt százalékos arányban és alacsony találati százalékban vannak-e. Ez az állapot nagy gyorsítótár-kizárást jelez, ami hatással lehet a SQL Data Warehouse-példány teljesítményére. Az Advisor azt javasolja, hogy a SQL Data Warehouse-példány vertikális felskálázásával biztosítsa, hogy elegendő gyorsítótári kapacitást foglaljon le a munkaterhelés számára.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez alakítsa át SQL Data Warehouse táblákat a replikált táblákba

Az Advisor olyan táblákat azonosít, amelyek nem replikálnak táblákat, de a konverzió hasznát vennék. Azt javasolja, hogy konvertálja ezeket a táblákat. A javaslatok alapja:
- A replikált tábla mérete 
- Az oszlopok száma. 
- A tábla terjesztési típusa 
- A SQL Data Warehouse táblán lévő partíciók száma. 

További heurisztikus információk is megadhatók a környezethez tartozó javaslatban. További információ a javaslat meghatározásáról: [SQL Data Warehouse javaslatok](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Telepítse át a Storage-fiókját Azure Resource Managerre a legújabb Azure-funkciók beszerzéséhez

Telepítse át a Storage-fiók központi telepítési modelljét, hogy Azure Resource Manager a következő előnyök kihasználásához:
- Sablon központi telepítései.
- További biztonsági beállítások.
- Lehetőség a GPv2-fiókra való frissítésre, hogy használhassa a legújabb Azure Storage-funkciókat. 

Az Advisor azonosítja a klasszikus üzemi modellt használó önálló tárolási fiókokat, és javasolja a Resource Manager-alapú üzemi modellbe való áttelepítést.

> [!NOTE]
> A klasszikus riasztások Azure Monitor augusztusban 2019 augusztusában lettek kivonva. Javasoljuk, hogy frissítse a klasszikus Storage-fiókját a Resource Manager használatára, hogy megőrizze a riasztási funkciókat az új platformmal. További információ: [klasszikus riasztások nyugdíjazása](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlát elérését

Az Azure-régiók előfizetése legfeljebb 250 Storage-fiókot támogat. Ha elérte ezt a korlátot, a rendszer nem tudja létrehozni a tárolási fiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket, és javaslatokat tesz arra, hogy kevesebb Storage-fiókot hozzon igénybe olyan előfizetések/régiók esetében, amelyek közel állnak a maximális korlát eléréséhez.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Érdemes megfontolni a VPN Gateway SKU méretének növelését a magas P2S-használat érdekében

Minden Azure VPN Gateway SKU csak a megadott számú egyidejű P2S-kapcsolatot képes támogatni. Ha a kapcsolatok száma megközelíti az átjáró korlátját, a további kapcsolódási kísérletek sikertelenek lehetnek. Az átjáró méretének növelésével több párhuzamos P2S-felhasználót is támogatni tud. Az Advisor ajánlásokat és útmutatást nyújt az átjáró méretének növeléséhez.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Vegye fontolóra VPN Gateway SKU méretének növelését a magas CPU-ra

A nagy forgalmú terhelés alatt a VPN-átjáró magas CPU miatt eldobást eredményezhet a csomagok számára. Érdemes lehet frissíteni a VPN Gateway SKU-t. A VPN-átjáró méretének növelésével biztosíthatja, hogy a kapcsolatok ne legyenek elvetve a magas CPU miatt. Az Advisor ajánlásokat biztosít a probléma proaktív megoldásához. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>A köteg méretének betöltéskor történő növelése a terhelésátvitel, az adattömörítés és a lekérdezési teljesítmény maximalizálása érdekében

Az Advisor észleli, hogy növelhető-e a terhelési teljesítmény és az átviteli sebesség a Batch méretének növelésével az adatbázisba való betöltéskor. Érdemes lehet a COPY utasítást használni. Ha nem tudja használni a COPY utasítást, érdemes lehet növelni a köteg méretét, ha olyan betöltési segédprogramokat használ, mint például a SQLBulkCopy API vagy a BCP. Az általános szabály a 100 000 és 1 000 000 közötti méretű kötegek használata. A Batch méretének növelése növeli a terhelési sebességet, az adattömörítést és a lekérdezési teljesítményt.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Az azonos régióban található Storage-fiók megkeresése a késés minimalizálásához a betöltéskor

Az Advisor észleli, hogy az SQL-készlettől eltérő régióból töltődik-e be. Érdemes lehet betölteni egy olyan Storage-fiókból, amely ugyanabban a régióban található, mint az SQL-készlet, hogy csökkentse a késést az adatok betöltésekor. Ez a változás segít csökkenteni a késést, és növeli a terhelési teljesítményt.

## <a name="use-a-supported-kubernetes-version"></a>Támogatott Kubernetes-verzió használata

Az Advisor észleli a Kubernetes nem támogatott verzióit.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Azure Database for MySQL, Azure Database for PostgreSQL és Azure Database for MariaDB kiszolgálók teljesítményének optimalizálása

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Javítsa ki a processzor szűk keresztmetszeteit a Azure Database for MySQL, Azure Database for PostgreSQL és Azure Database for MariaDB-kiszolgálók CPU-terhelésével
A CPU hosszú időn keresztül történő magas kihasználtsága lassú lekérdezési teljesítményt eredményezhet a munkaterhelés számára. A CPU méretének növelésével optimalizálható az adatbázis-lekérdezések futtatókörnyezete, és javítható a teljes teljesítmény. Az Advisor magas CPU-kihasználtsággal rendelkező kiszolgálókat azonosít, amelyek valószínűleg a CPU által korlátozott munkaterheléseket futtatják, és a számítási feladatok skálázását ajánlják.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Csökkentse a memória korlátozásait a Azure Database for MySQL-, Azure Database for PostgreSQL-és Azure Database for MariaDB-kiszolgálókon, vagy térjen át a memóriára optimalizált SKU-ra.
Az alacsony gyorsítótár-találatok aránya lassabb lekérdezési teljesítményt és nagyobb IOPS eredményezhet. Ezt az állapotot egy hibás lekérdezési csomag vagy egy nagy mennyiségű számítási feladat okozhatja. A lekérdezési terv kijavítása vagy a Azure Database for PostgreSQL, Azure Database for MySQL vagy Azure Database for MariaDB kiszolgáló [memóriájának növelésével](../postgresql/concepts-pricing-tiers.md) optimalizálható az adatbázis-számítási feladatok végrehajtása. A Azure Advisor azonosítja a nagy puffer-készlet által érintett kiszolgálókat. Azt javasolja, hogy a következő műveletek egyikét végezze el: 
- A lekérdezési terv kijavítása
- Olyan SKU-ra váltson, amely több memóriát tartalmaz 
- Növelje a tárterület méretét, hogy minél több IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Azure Database for MySQL vagy Azure Database for PostgreSQL olvasási replika használata az olvasási igényű számítási feladatok felskálázásához
Az Advisor munkaterhelés-alapú heurisztikus adatokat használ, például az olvasások arányát a kiszolgálón az elmúlt hét napban az olvasási igényű számítási feladatok azonosítására. A magas olvasási/írási aránnyal rendelkező Azure Database for PostgreSQL vagy Azure Database for MySQL erőforrás CPU-vagy memória-tartalmakat eredményezhet, és lassú lekérdezési teljesítményt eredményezhet. A [replika](../postgresql/howto-read-replicas-portal.md) hozzáadásával kibővítheti a replika-kiszolgáló olvasási felskálázását, és megakadályozhatja a CPU-vagy memória-korlátozásokat az elsődleges kiszolgálón. Az Advisor az olvasási igényű munkaterhelésekkel rendelkező kiszolgálókat azonosítja, és azt javasolja, hogy adjon hozzá egy [olvasási replikát](../postgresql/concepts-read-replicas.md)   a beolvasott számítási feladatok kiszervezéséhez.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Azure Database for MySQL, Azure Database for PostgreSQL vagy Azure Database for MariaDB kiszolgáló méretezése magasabb SKU-ra a kapcsolódási kényszerek megelőzése érdekében
Az adatbázis-kiszolgáló minden új kapcsolódása memóriát foglal le. Az adatbázis-kiszolgáló teljesítménye romlik, ha a kiszolgálóhoz való kapcsolódás a memória [felső korlátja](../postgresql/concepts-limits.md) miatt meghiúsul. A Azure Advisor számos sikertelen kapcsolatfelvételi hibát használó kiszolgálókat azonosít. Azt javasolja, hogy a kiszolgáló kapcsolódási korlátait úgy frissítse, hogy több memóriát biztosítson a kiszolgálónak a következő műveletek egyikének megkezdésével:
- A számítási kapacitás vertikális felskálázása. 
- Használjon a memóriához optimalizált SKU-ket, amelyek több számítási egységenként vannak.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>A gyorsítótár és az alkalmazások teljesítményének növelése érdekében méretezheti a gyorsítótárat egy másik méretre vagy SKU-ra.

A cache-példányok akkor működnek a legjobban, ha nem a nagy memória, a kiszolgáló terhelése vagy a nagy hálózati sávszélesség alatt futnak. Ezek a feltételek nem válaszolnak, az adatvesztés vagy elérhetetlenné válnak. Az Advisor ezekben a feltételekben azonosítja a gyorsítótár-példányokat. Azt javasolja, hogy a következő műveletek egyikét végezze el:
- Az ajánlott eljárások alkalmazásával csökkentheti a memória terhelését, a kiszolgáló terhelését vagy a hálózati sávszélességet.
- Méretezés más méretre vagy SKU-ra, amely nagyobb kapacitással rendelkezik.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Régiókat adhat hozzá a Azure Cosmos DB-fiókjához tartozó adatforgalomhoz

Az Advisor olyan Azure Cosmos DB-fiókokat észlel, amelyeknek jelenleg nincs konfigurált régiója a forgalma. A régió hozzáadását javasolja. Ezzel növeli az adott régiótól érkező kérelmek késését, és biztosítja a rendelkezésre állást a régiók kiesése esetén. [További információ a globális adatterjesztésről Azure Cosmos DB.](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Az Azure Cosmos DB indexelési házirend konfigurálása egyéni, belefoglalt vagy kizárt elérési utak használatával

Az Advisor azonosítja Azure Cosmos DB tárolókat, amelyek az alapértelmezett indexelési házirendet használják, de az egyéni indexelési házirendek előnyeit is kihasználhatják. Ez a meghatározás a számítási feladatok mintáján alapul. Az alapértelmezett indexelési házirend indexeli az összes tulajdonságot. A lekérdezési szűrőben használt explicit belefoglalt vagy kizárt elérési úttal rendelkező egyéni indexelési házirend csökkentheti az indexeléshez felhasznált RUs-t és tárterületet. [További információ az index-házirendek módosításáról.](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB lekérdezési oldal méretének (MaxItemCount) beállítása a-1 értékre 

A Azure Advisor a 100-es lekérdezési oldal méretét használó Azure Cosmos DB tárolókat azonosítja. Azt javasolja, hogy az oldal mérete (1) legyen a gyorsabb keresésekhez. [További információ a MaxItemCount.](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Teljesítménnyel kapcsolatos javaslatok elérése az Advisorban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor Irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
