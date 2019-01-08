---
title: Az Azure Advisor-teljesítményajánlások |} A Microsoft Docs
description: Az Advisor használatával az Azure-környezetek teljesítményének optimalizálásához.
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 93757c9f589ec1a6d5065d32740831dac922a015
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079068"
---
# <a name="advisor-performance-recommendations"></a>Advisor-teljesítményajánlások

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

Az Advisor azonosítja, amely eléri-e méretezhetőségi célértékét tárfiókhoz tartozó virtuális gépek. Így ki vannak téve az i/o-szabályozásra. Az Advisor javasolni fogja, hogy ezek a virtuális gépek felügyelt lemezeket használni teljesítményromlás megelőzése érdekében.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A teljesítmény és a virtuálisgép-lemezek megbízhatóságának javítása a Premium Storage segítségével

Advisor azonosít, amelyek nagy mennyiségű transations, a tárfiók a standard szintű lemezek, virtuális gépeket, és javasolja, hogy prémium szintű lemezek telepítse. 

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokat futtató virtuális gépek tesz lehetővé. Premium storage-fiókok használó virtuálisgép-lemezek tartós állapotú meghajtókhoz (SSD-kkel) adatokat tárolja. Az alkalmazás a legjobb teljesítmény érdekében javasoljuk, hogy a virtuális gép lemezei a premium storage magas iops-t igénylő telepített át.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Döntés az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az adatok eltávolítása

Adatok torzulása szükségtelen adatok mozgását vagy az erőforrás szűk keresztmetszeteket okozhat, a számítási feladatok futtatásakor. Az Advisor észleli terjesztési adatok tevékenységdiagramon nagyobb, mint 15 %, és javasoljuk, hogy az adatok újraterjesztése, és nyissa meg újra az a tábla terjesztési kulcs beállításokat. További információk azonosítása és torzulása eltávolításával kapcsolatban lásd: [hibaelhárítási torzulása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az elavult tábla statisztikák létrehozása vagy frissítése

Az Advisor azonosítja, amelyek nem rendelkeznek naprakész táblák [táblastatisztikák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) és táblastatisztikák javasolja létrehozása vagy frissítése. Az SQL data warehouse-optimalizáló naprakész ukazatelé segítségével megbecsülheti a számosság vagy a lekérdezés eredményét, amely lehetővé teszi a lekérdezésoptimalizáló hozhat létre kiváló minőségű lekérdezésterv a leggyorsabb teljesítmény sorainak lekérdezés.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Vertikális felskálázás a lekérdezési teljesítmény érdekében az SQL Data Warehouse tábláiba gyorsítótár a kihasználtság optimalizálása

Az Azure Advisor észleli az SQL Data Warehouse-e magas gyorsítótár használt százalékos aránya, és a egy alacsony találati százalékot. Ez azt jelzi, hogy magas gyorsítótár kiürítési, ami hatással lehet az SQL Data Warehouse teljesítményét. Az Advisor javasolja, hogy az SQL Data Warehouse biztosításához lefoglalni elegendő gyorsítótár kapacitás a számítási feladatok vertikális.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Replikált táblák lekérdezési teljesítmény érdekében az SQL Data Warehouse-táblákat átalakítása

Az Advisor táblákat, amelyek nem szerepelnek a replikált táblák, de kiaknázhatják a konvertálás azonosítja, és javasolja, hogy ezek a táblázatok konvertálása. Javaslatok a replikált tábla méretét, az oszlopok, tábla terjesztési típust és az SQL Data Warehouse-tábla partícióinak száma száma alapulnak. További heurisitics az ajánlás környezet áll rendelkezésre. Hogyan határozza meg, ez a javaslat kapcsolatos további információkért lásd: [SQL Data Warehouse javaslatok](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>A Tárfiók migrálása az Azure Resource Managerhez való csatlakoztatásával minden, a legújabb Azure-funkciók

Telepítse át a Storage-fiók üzembe helyezési modellben az Azure Resource Manager (ARM) sablon-üzembehelyezések, a további biztonsági beállításokat és a lehet majd frissíteni GPv2-fiókra kihasználtságát az Azure Storage a legújabb funkciók előnyeinek kihasználása érdekében. Az Advisor összes önálló tárfiókot a klasszikus üzemi modellt használó azonosítja, és javasolja, hogy az ARM-alapú üzemi modellbe való migrálás. 

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

