---
title: Az Azure-alkalmazások teljesítményének javítása a Azure Advisorsal | Microsoft Docs
description: Az Advisor használatával optimalizálja az Azure-beli üzemelő példányok teljesítményét.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: 82c77568a2c62a0e02bf00c164f4600e221376da
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183120"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Az Azure-alkalmazások teljesítményének javítása Azure Advisor

Azure Advisor teljesítményre vonatkozó javaslatok segítenek az üzleti szempontból kritikus fontosságú alkalmazások sebességének és reagálásának javításában. Az Advisor teljesítményére vonatkozó javaslatokat az Advisor irányítópult **teljesítmény** lapján érheti el.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Csökkentse a DNS-időt a Traffic Manager-profilban való működésre, hogy gyorsabban átadja a feladatokat a megfelelő állapotú végpontoknak

Az Traffic Manager profil élettartama [(TTL) beállításai](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) lehetővé teszik annak megadását, hogy a végpontok milyen gyorsan legyenek átváltva, ha egy adott végpont nem válaszol a lekérdezésekre. A TTL-értékek csökkentése azt jelenti, hogy az ügyfelek gyorsabban lesznek átirányítva a működő végpontok működéséhez.

Azure Advisor azonosítja a hosszabb ÉLETTARTAMú Traffic Manager profilokat, és azt javasolja, hogy az ÉLETTARTAMot 20 másodpercre vagy 60 másodpercre konfigurálja, attól függően, hogy a profil konfigurálva van-e [Fast Failoverhoz](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Az adatbázis teljesítményének növelése az SQL DB Advisorral

Az Advisor egységes, összevont áttekintést nyújt az összes Azure-erőforrásra vonatkozó javaslatokról. A SQL Database Advisor a SQL Azure-adatbázis teljesítményének javítására vonatkozó javaslatok bevonásával integrálható. SQL Database Advisor a használati előzmények elemzésével értékeli a SQL Azure adatbázisok teljesítményét. Ezután ajánlásokat nyújt az adatbázis tipikus számítási feladatainak futtatásához.

> [!NOTE]
> A javaslatok beszerzéséhez egy adatbázisnak körülbelül egy hétig kell lennie, és ezen a héten belül bizonyos konzisztens tevékenységnek kell lennie. A SQL Database Advisor könnyebben optimalizálható a konzisztens lekérdezési mintáknál, mint a tevékenységek véletlenszerű kitörése esetén.

További információ a SQL Database Advisorről: [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>A App Service teljesítményének és megbízhatóságának javítása

A Azure Advisor az ajánlott eljárásokat ajánlja a App Services-élmény javításához és a megfelelő platform-képességek felfedezéséhez. Példák App Services javaslatokra:
* Azon példányok észlelése, amelyekben a memória-vagy a CPU-erőforrások kimerülnek a kockázatcsökkentő beállításokkal rendelkező app Runtimes használatával.
* Olyan példányok észlelése, amelyekben a collocating-erőforrások, például a webalkalmazások és az adatbázisok javítják a teljesítményt és az alacsonyabb költségeket.

App Services javaslatokkal kapcsolatos további információkért tekintse [meg a Azure app Service ajánlott eljárásai](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)című témakört.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>A lemez I/O-szabályozásának megakadályozása a Managed Disks használatával

Az Advisor azonosítja azokat a virtuális gépeket, amelyek egy olyan Storage-fiókhoz tartoznak, amely eléri a méretezhetőségi célját. Ez az állapot az I/O-szabályozásra fogékony virtuális gépeket tesz elérhetővé. Az Advisor azt javasolja, hogy Managed Disks használjanak a teljesítmény romlásának megelőzése érdekében.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A virtuális gépek lemezeinek teljesítményének és megbízhatóságának javítása Premium Storage használatával

Az Advisor a Storage-fiókban nagy mennyiségű tranzakcióval rendelkező, standard lemezekkel rendelkező virtuális gépeket azonosít, és a prémium szintű lemezekre történő frissítést javasolja. 

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az I/O-igényű számítási feladatokat futtató virtuális gépekhez. A Premium Storage-fiókokat használó virtuális gépek lemezei SSD-meghajtókon tárolják az adattárolást. Az alkalmazás legjobb teljesítményének érdekében javasoljuk, hogy a Premium Storage-hoz magas IOPS igénylő virtuálisgép-lemezeket telepítse át.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez távolítsa el az SQL-adattárház-táblázat adatait

Az adatok eldöntése szükségtelen adatáthelyezést vagy erőforrás-szűk keresztmetszetet okozhat a számítási feladatok futtatásakor. Az Advisor a 15%-nál nagyobb eloszlási adattorzítást deríti fel, és azt javasolja, hogy terjessze újra az adatait, és újra keresse meg a tábla terjesztési kulcsának beállításait. Ha többet szeretne megtudni a ferdeség azonosításáról és eltávolításáról, tekintse meg a következő témakört: [Hibaelhárítás](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez hozzon létre vagy frissítsen elavult tábla statisztikáit az SQL-adattárház táblában

Az Advisor olyan táblákat azonosít, amelyek nem rendelkeznek naprakész [táblázat statisztikával](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) , és a tábla statisztikáinak létrehozását és frissítését javasolja. Az SQL-adattárház-lekérdezés-optimalizáló a lekérdezési eredményben a naprakész statika értékkel becsüli meg, hogy a lekérdezés-optimalizáló magas színvonalú lekérdezési tervet hozzon létre a leggyorsabb teljesítmény érdekében.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Vertikális felskálázás a gyorsítótár kihasználtságának optimalizálásához a SQL Data Warehouse táblákon a lekérdezési teljesítmény növeléséhez

A Azure Advisor észleli, ha a SQL Data Warehouse nagy gyorsítótár-használatú, és alacsony találati aránya van. Ez az állapot nagy gyorsítótár-kizárást jelez, ami hatással lehet a SQL Data Warehouse teljesítményére. Az Advisor azt javasolja, hogy a SQL Data Warehouse vertikális felskálázásával gondoskodjon arról, hogy elegendő gyorsítótár-kapacitást foglaljon le a munkaterhelés számára.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez alakítsa át SQL Data Warehouse táblákat a replikált táblákba

Az Advisor azokat a táblákat azonosítja, amelyek nem replikált táblák, de a konvertálás előnyeit kihasználva kihasználhatják ezeket a táblákat. A javaslatok a replikált tábla méretétől, az oszlopok számának, a táblázat eloszlási típusának és a SQL Data Warehouse tábla partícióinak számától függenek. További heurisztikus információk is megadhatók a környezethez tartozó javaslatban. További információ a javaslat meghatározásáról: [SQL Data Warehouse javaslatok](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Telepítse át a Storage-fiókját a Azure Resource Managerra az összes legújabb Azure-funkció beszerzéséhez

Telepítse át a Storage-fiók telepítési modelljét Azure Resource Managerra (Resource Manager), hogy kihasználhassa a sablonok központi telepítését, a további biztonsági beállításokat, valamint az Azure Storage legújabb funkcióinak kihasználásához szükséges GPv2-fiókra való frissítést. Az Advisor azonosítja a klasszikus üzemi modellt használó önálló tárolási fiókokat, és javasolja a Resource Manager-alapú üzemi modellbe való áttelepítést.

> [!NOTE]
> A Azure Monitor klasszikus riasztásai a 2019 augusztusában lettek kivonva. Javasoljuk, hogy frissítse a klasszikus Storage-fiókját a Resource Manager használatára, hogy megőrizze a riasztási funkciókat az új platformmal. További információ: [klasszikus riasztások nyugdíjazása](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlátot

Egy Azure-régió legfeljebb 250 Storage-fiókot tud támogatni előfizetésre. Ha elérte a korlátot, nem fog tudni további Storage-fiókokat létrehozni az adott régióban/előfizetések kombinációjában. Az Advisor megkeresi az előfizetéseket és a felületre vonatkozó javaslatokat, hogy kevesebb Storage-fiókot tervezzen meg, amelyek közel állnak a maximális korlát eléréséhez.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB-kiszolgálók teljesítményének optimalizálása 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Javítsa ki az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB-kiszolgálók CPU-terhelését a CPU szűk keresztmetszetével
A CPU nagy kihasználtsága hosszabb időn keresztül lassú lekérdezési teljesítményt eredményezhet a munkaterhelés számára. A CPU méretének növelése segít az adatbázis-lekérdezések futtatókörnyezetének optimalizálásában és az általános teljesítmény növelésében. Azure Advisor a nagy CPU-kihasználtsággal rendelkező kiszolgálók azonosítására, amelyek valószínűleg a CPU által korlátozott munkaterheléseket futtatnak, és a számítási feladatok méretezését javasolják.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Csökkentheti az Azure MySQL, az Azure PostgreSQL és az Azure MariaDB-kiszolgálók memóriabeli korlátozásait, vagy áthelyezheti a memóriát optimalizált SKU-ra.
Az alacsony gyorsítótár-találatok aránya lassabb lekérdezési teljesítményt és nagyobb IOPS eredményezhet. Ennek oka lehet egy hibás lekérdezési csomag vagy a memória-igényes munkaterhelés futtatása. A lekérdezési terv vagy az Azure Database for PostgreSQL adatbázis-kiszolgáló, az Azure MySQL adatbázis-kiszolgáló vagy az Azure MariaDB-kiszolgáló [memóriájának](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) javítása segít optimalizálni az adatbázis-számítási feladatok végrehajtását. Azure Advisor azonosítja a nagy pufferméret miatt érintett kiszolgálókat, és a lekérdezési terv kijavítását, a több memóriával rendelkező magasabb SKU-ra való áttérést, vagy a tárterület méretének növelését javasolja, hogy további IOPS legyenek.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Az Azure MySQL vagy az Azure PostgreSQL olvasási replikájának használata az olvasási igényű számítási feladatokhoz
Azure Advisor kihasználja a munkaterhelés-alapú heurisztikus műveleteket, például az olvasások arányát a kiszolgálón az elmúlt hét napban, az olvasási igényű számítási feladatok azonosítása érdekében. Az Azure Database for PostgreSQL-erőforrás vagy az Azure Database for MySQL-erőforrás nagyon magas olvasási/írási aránnyal eredményezheti a CPU-és/vagy memória-tartalmakat, ami lelassítja a lekérdezési teljesítményt. A [replika](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) hozzáadásával a rendszer kibővíti az olvasási adatokat a replika-kiszolgálóra, megakadályozva a processzor és/vagy a memória korlátozásait az elsődleges kiszolgálón. Az Advisor olyan kiszolgálókat azonosít, amelyek ilyen nagy olvasási igényű számítási feladatokkal rendelkeznek, és javasolt egy [olvasási replika](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) hozzáadását a beolvasott számítási feladatok kiszervezéséhez.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Az Azure MySQL, az Azure PostgreSQL vagy az Azure MariaDB-kiszolgáló méretezése magasabb SKU-ra a kapcsolatok megkötésének megakadályozása érdekében
Az adatbázis-kiszolgáló minden új kapcsolódása memóriát foglal le. Az adatbázis-kiszolgáló teljesítménye romlik, ha a kiszolgálóhoz való kapcsolódás a memória [felső korlátja](https://docs.microsoft.com/azure/postgresql/concepts-limits) miatt meghiúsul. A Azure Advisor számos kapcsolódási hibával rendelkező kiszolgálókat azonosít, és javasolja, hogy frissítse a kiszolgáló kapcsolatainak korlátait, hogy több memóriát biztosítson a kiszolgálónak a számítási vagy a memóriára optimalizált SKU-k használatával, amelyek több számítási kapacitással rendelkeznek.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>A gyorsítótár és az alkalmazások teljesítményének növelése érdekében méretezheti a gyorsítótárat egy másik méretre vagy SKU-ra.

A gyorsítótár-példányok akkor működnek a legjobban, ha nem a nagy mennyiségű, a kiszolgáló terhelése vagy a nagy hálózati sávszélesség alatt futnak, ami azt okozhatja, hogy nem válaszol, az adatvesztés vagy elérhetetlenné válik. Az Advisor azonosítja a gyorsítótár-példányokat ezekben a feltételekben, és javaslatot tesz az ajánlott eljárások alkalmazására, hogy csökkentse a memória terhelését, a kiszolgáló terhelését vagy a hálózati sávszélességet, vagy méretezést egy másik méretre vagy SKU-ra nagyobb kapacitással.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Régiókat adhat hozzá a Azure Cosmos DB-fiókjához tartozó adatforgalomhoz

Az Advisor felismeri Azure Cosmos DB olyan fiókokat, amelyek egy jelenleg nem konfigurált régióból származó forgalommal rendelkeznek, és a régió hozzáadását javasolja. Ez növeli a régióból érkező kérelmek késését, és a régió leállása esetén biztosítja a rendelkezésre állást. [További információ a globális adatterjesztésről Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Az Azure Cosmos DB indexelési házirend konfigurálása az ügyfelek által tartalmazott vagy kizárt elérési utakkal

Azure Advisor azonosítja Cosmos DB tárolókat, amelyek az alapértelmezett indexelési házirendet használják, de a munkaterhelés-minta alapján kihasználhatják az egyéni indexelési szabályzatot. Az alapértelmezett indexelési házirend indexeli az összes tulajdonságot, de a lekérdezési szűrőben használt explicit vagy kizárt elérési úttal rendelkező egyéni indexelési házirend használatával csökkentheti az indexeléshez felhasznált RUs-t és tárterületet. [További információ az index-házirendek módosításáról](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Az Azure Cosmos DB lekérdezési lapméretének (MaxItemCount) beállítása -1 értékre 

A Azure Advisor azonosítja Azure Cosmos DB tárolókat, amelyek a 100-es lekérdezési oldal méretét használják, és a gyorsabb keresés érdekében javasolt az oldal mérete (1). [További információ az elemek maximális számáról](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Teljesítménnyel kapcsolatos javaslatok elérése az Advisorban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor Irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
