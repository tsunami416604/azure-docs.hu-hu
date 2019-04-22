---
title: Az Azure-alkalmazások az Azure Advisor szolgáltatással a teljesítmény javítása |} A Microsoft Docs
description: Az Advisor használatával az Azure-környezetek teljesítményének optimalizálásához.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 0b2d242519e7e8981a905d6adb1f3c0f091afe38
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698945"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Az Azure Advisorral az Azure-alkalmazások teljesítményének növelése

Az Azure Advisor-teljesítményajánlások sebesség és az üzleti szempontból kritikus fontosságú alkalmazások válaszképességét javítása érdekében. Advisor javaslatainak teljesítménnyel kapcsolatos javaslatok is kap a **teljesítmény** az Advisor irányítópult lapon.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>A Traffic Manager-profilt, hogy gyorsabban átadása a kifogástalan állapotú végpontok DNS idejének csökkentése

[Élettartam (TTL) beállításainak idő](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) a Traffic Manager-profilok lehetővé teszik, hogy adja meg, hogyan lehet gyorsan végpontok váltani, ha a megadott végpont nem válaszol, a lekérdezéseket. Csökkenti az élettartam-értékek, az azt jelenti, hogy az ügyfelek gyorsabban működő végpontok lesznek átirányítva.

Az Azure Advisor azonosítja a Traffic Manager-profilok konfigurált hosszabb TTL és 20 másodperc és 60 másodperc, attól függően, hogy az élettartam konfigurálása javasolja a profilhoz van beállítva [gyors feladatátvétel](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Adatbázis-teljesítmény javítása az SQL DB Advisor szolgáltatással

Az Advisor-javaslatok az Azure-erőforrások egységes, összevont nézetének biztosít. Integrálható az SQL Database Advisor viszi, az SQL Azure adatbázis teljesítményének javítására vonatkozó javaslatokat. Az SQL Database Advisor az SQL Azure-adatbázisok teljesítményét értékeli a használati előzmények elemzésével. Javaslatok, amelyek a leginkább kihasználni az adatbázis jellemző számítási feladatot futtat majd kínál.

> [!NOTE]
> Javaslatokat beolvasni egy adatbázisnak rendelkeznie kell egy hét használati kapcsolatban, és a hét belül kell lennie konzisztens tevékenységet észleltünk a fiókjában. Az SQL Database Advisor a lekérdezés konzisztens minták, mint a tevékenység véletlenszerű csúcsforgalomra könnyebben optimalizálható.

Az SQL Database Advisor kapcsolatos további információkért lásd: [az SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Az App Service teljesítményének és megbízhatóságának javítása

Az Azure Advisor integrálja, ajánlott eljárásait az App Services élmény javítása és a megfelelő platform képességei felderítése. Példák az App Services ajánlások a következők:
* Ahol memória vagy a Processzor-erőforrások elfogytak, kockázatcsökkentési lehetőségek alkalmazás modulok által példányok felismerése.
* Észlelését, ahol helymegosztást erőforrások, például a web apps és az adatbázisok példányai javíthatja a teljesítményt és alacsony költségek mellett.

App Services javaslatok kapcsolatos további információkért lásd: [ajánlott eljárások az Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Felügyelt lemezek használata a lemez i/o szabályozásának megelőzése érdekében

Az Advisor azonosítja, amely eléri-e méretezhetőségi célértékét tárfiókhoz tartozó virtuális gépek. Ez a feltétel teszi azokat, virtuális gépek ki vannak téve az i/o-szabályozásra. Az Advisor javasolni fogja a felügyelt lemezeket használnak teljesítményromlás megelőzése érdekében.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A teljesítmény és a virtuálisgép-lemezek megbízhatóságának javítása a Premium Storage segítségével

Advisor azonosít, amelyek nagy mennyiségű tranzakciót a tárfiók a standard szintű lemezek, virtuális gépeket, és javasolja, hogy prémium szintű lemezek telepítse. 

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokat futtató virtuális gépek tesz lehetővé. Premium storage-fiókok használó virtuálisgép-lemezek tartós állapotú meghajtókhoz (SSD-kkel) adatokat tárolja. Az alkalmazás a legjobb teljesítmény érdekében javasoljuk, hogy a virtuális gép lemezei a premium storage magas iops-t igénylő telepített át.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Döntés az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az adatok eltávolítása

Adatok torzulása szükségtelen adatok mozgását vagy az erőforrás szűk keresztmetszeteket okozhat, a számítási feladatok futtatásakor. Az Advisor észleli terjesztési adatok tevékenységdiagramon nagyobb, mint 15 %, és javasoljuk, hogy az adatok újraterjesztése, és nyissa meg újra az a tábla terjesztési kulcs beállításokat. További információk azonosítása és torzulása eltávolításával kapcsolatban lásd: [hibaelhárítási torzulása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az elavult tábla statisztikák létrehozása vagy frissítése

Az Advisor azonosítja, amelyek nem rendelkeznek naprakész táblák [táblastatisztikák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) és táblastatisztikák javasolja létrehozása vagy frissítése. Az SQL data warehouse-optimalizáló naprakész ukazatelé segítségével megbecsülheti a számosság vagy a lekérdezés eredményét, amely lehetővé teszi a lekérdezésoptimalizáló hozhat létre a leggyorsabb teljesítmény magas színvonalú lekérdezésterv sorainak lekérdezés.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Vertikális felskálázás a lekérdezési teljesítmény érdekében az SQL Data Warehouse tábláiba gyorsítótár a kihasználtság optimalizálása

Az Azure Advisor észleli az SQL Data Warehouse-e magas gyorsítótár használt százalékos aránya, és a egy alacsony találati százalékot. Ez az állapot azt jelzi, hogy magas gyorsítótár-kiürítés, ami hatással lehet az SQL Data Warehouse teljesítményét. Az Advisor javasolja, hogy az SQL Data Warehouse biztosításához lefoglalni elegendő gyorsítótár kapacitás a számítási feladatok vertikális.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Replikált táblák lekérdezési teljesítmény érdekében az SQL Data Warehouse-táblákat átalakítása

Az Advisor táblákat, amelyek nem szerepelnek a replikált táblák, de kiaknázhatják a konvertálás azonosítja, és javasolja, hogy ezek a táblázatok konvertálása. Javaslatok a replikált tábla méretét, az oszlopok, tábla terjesztési típust és az SQL Data Warehouse-tábla partícióinak száma száma alapulnak. További heurisztika az ajánlás környezet áll rendelkezésre. Hogyan határozza meg, ez a javaslat kapcsolatos további információkért lásd: [SQL Data Warehouse javaslatok](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>A Tárfiók migrálása az Azure Resource Managerhez való csatlakoztatásával minden, a legújabb Azure-funkciók

Áttelepítése a Storage-fiók üzembehelyezési modellje Azure Resource Manager-(Resource Manager) sablon-üzembehelyezések, a további biztonsági beállításokat és a lehet majd frissíteni GPv2-fiókra kihasználtságát az Azure Storage a legújabb funkciók előnyeinek kihasználása érdekében. Az Advisor összes önálló tárfiókot a klasszikus üzemi modellt használó azonosítja, és javasolja a Resource Manager-alapú üzemi modellbe való migrálás.

> [!NOTE]
> Klasszikus riasztások az Azure monitorban ütemezett kivonása a június 2019. Azt javasoljuk, hogy a riasztási funkciók és az új platform megőrzése a Resource Manager használatával a klasszikus tárfiók frissítése. További információkért lásd: [klasszikus riasztások használatból való kivonást egyaránt](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>A tárfiókok maximális előfizetési lenyomásával elkerülése érdekében tervezése

Egy Azure-régióban is támogatja a legfeljebb 250 tárfiókot előfizetésenként. A korlát elérésekor lesz nem hozható létre minden olyan további tárfiókok régió/előfizetés kombinációt. Advisor ellenőrzi az előfizetések, és felület javaslatok is tervezhet kevesebb storage-fiókok a bármely, amely megközelítette a maximális korlát elérése.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>A Azure MariaDB, Azure MySQL és Azure PostgreSQL-kiszolgálók a teljesítmény optimalizálása 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Javítsa ki a CPU nyomás a Azure MariaDB, Azure MySQL és Azure PostgreSQL-kiszolgálók a Processzorok jelentette szűk keresztmetszetek
Hosszabb időn keresztül a processzor nagyon magas kihasználtság okozhat a lassú lekérdezések teljesítményét, a számítási feladatok számára. CPU méretének növelése a futtatókörnyezet, az adatbázis-lekérdezések optimalizálása a Súgó és teljesítményjavítás általános. Az Azure Advisor egy magas CPU-kihasználtság, amely valószínűleg futtatja korlátozott CPU-számítási feladatokhoz, és javasolja a skálázási a számítási erőforrásokat az azonosítja azokat a kiszolgálókat.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>A Azure MariaDB, Azure MySQL és Azure PostgreSQL-kiszolgálók memória korlátozásaihoz csökkentése, vagy helyezze át a memória optimalizált SKU
Egy alacsony gyorsítótárának találati aránya lassabb lekérdezési teljesítmény és a megnövekedett IOPS eredményezhet. Ezt okozhatja egy hibás lekérdezési terv vagy a memória-intenzív számítási feladatot futtat. A lekérdezésterv kijavítása vagy [a memória bővítését](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) az Azure Database for PostgreSQL adatbázis-kiszolgáló, az Azure-beli MySQL-adatbázis-kiszolgáló vagy az Azure MariaDB-kiszolgáló segítségével optimalizálhatja az adatbázis számítási feladatok végrehajtását. Az Azure Advisor azonosítja az alkalmazáskészlet magas puffer kihagyásával miatt az érintett kiszolgálókon, és javasolja, kijavítása vagy a lekérdezésterv áthelyezése egy magasabb szintű termékváltozatra, több memória, vagy tárolási megoldást, hogy további iops-érték beolvasása.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Egy Azure-beli MySQL vagy az Azure PostgreSQL írásvédett Replikapéldány használatával horizontális felskálázása olvasási olvasási-igényű számítási feladatokhoz
Az Azure Advisor például olvasási és írási a kiszolgálón az elmúlt hét napban olvasásigényű munkaterhelésekhez azonosításához aránya a munkaterhelés-alapú heurisztika használja. Az Azure database for PostgreSQL erőforrás vagy az Azure database MySQL erőforrás egy nagyon magas olvasási/írási arány a lekérdezési teljesítmény lassú vezető Processzor-és/vagy memória versenyt eredményezhet. Hozzáadás egy [replika](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) segít a horizontális felskálázás olvasási és a replikakiszolgáló az elsődleges kiszolgálón a Processzor-és/vagy memória megkötések miatt. Az Advisor azonosítja azokat a kiszolgálókat az ilyen nagy olvasásigényű munkaterhelésekhez és ajánlott hozzáadása egy [olvasható replika](https://docs.microsoft.com/en-us/azure/postgresql/concepts-read-replicas) kiszervezésére olvasási tevékenységprofilok egy része.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Egy magasabb szintű termékváltozatra, hogy a kapcsolati korlátozások az Azure-beli MySQL, Azure PostgreSQL vagy Azure MariaDB kiszolgáló méretezése
Minden egyes új kapcsolatot az adatbázis-kiszolgáló által elfoglalt memória. Az adatbázis-kiszolgáló teljesítményét csökkenti, ha a kiszolgáló felé irányuló kapcsolatot miatt nem működik egy [felső korlátja](https://docs.microsoft.com/en-us/azure/postgresql/concepts-limits) a memóriában. Az Azure Advisor azonosítja azokat a számos csatlakozási hibák és futtató kiszolgálók, és frissíti a kiszolgálói kapcsolatok korlátok a kiszolgálóhoz több memóriát biztosít számítási vertikális felskálázásával, vagy a memória optimalizált SKU-k, amelyek magonként további számítási használatával javasoljuk.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Az Advisor teljesítményajánlásainak elérése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatok kapcsolatos további információkért lásd:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-performance-recommendations.md)
* [Az Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)
