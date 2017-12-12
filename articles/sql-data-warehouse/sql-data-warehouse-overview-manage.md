---
title: "Azure SQL Data Warehouse adatbázisának kezelése |} Microsoft Docs"
description: "SQL Data Warehouse-adatbázisokban kezelésének áttekintése. Felügyeleti eszközök, a dwu-k és kibővített teljesítmény, lekérdezési teljesítményt, a helyes biztonsági házirendek létrehozása, és egy adatbázis visszaállításához adatsérülés akár regionális kimaradás hibaelhárítási tartalmazza."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: d7b81c12c31fe7de40acca6baa8972e65c306ee0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse adatbázisok kezelése
Az SQL Data Warehouse automatizálja az adatbázisok kezelése sok aspektusait. Például a méretezhető teljesítmény csak szeretné módosítani kell fizetnie a számítási erőforrásokat megfelelő szintű és hagyja meg az SQL Data Warehouse kiterjesztése és a méretezés vissza a rendszergazdára.

Kétségtelenül érdemes a teljesítmény igények meghatározása, valamint a hosszan futó lekérdezések hibaelhárítása a számítási feladat figyeléséhez. Engedélyek egyes felhasználókhoz és bejelentkezések kezelése néhány biztonsági feladatok elvégzésére is kell.

Ez az Áttekintés ezeket az jellemzőket SQL Data Warehouse felügyeletét tárgyalja.

* Felügyeleti eszközök
* Skála számítási
* Szüneteltetése és folytatása
* Teljesítmény gyakorlati tanácsok
* Lekérdezés figyelése
* Biztonság
* Biztonsági mentés és visszaállítás

## <a name="management-tools"></a>Felügyeleti eszközök
Számos különféle eszközre segítségével kezelheti az SQL Data Warehouse-adatbázisokat. Adatbázisok kezelése közben, során elkészít eszköz beállításainak a feladatokat kell elvégeznie.

### <a name="azure-portal"></a>Azure Portal
A [Azure-portálon] [ Azure portal] egy webes portál, ahol létrehozása, frissítése, és törölni az adatbázisokat és adatbázis-erőforrások figyelése. Ez az eszköz kiváló akkor, ha csak használatának megkezdéséhez Azure, az adatraktár-adatbázisokat kis számú kezelése, vagy gyorsan foglalkozhat kell.

Ismerkedés az Azure portálon, lásd: [(Azure-portál) az SQL Data Warehouse létrehozása][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>A Visual Studio SQL Server Data Tools összetevővel
[SQL Server Data Tools] [ SQL Server Data Tools] (SSDT) a Visual Studio lehetővé teszi, amelyhez csatlakozni kíván, kezelése és az adatbázis. Ha ismeri a Visual Studio vagy más integrált fejlesztési környezetekben (IDEs) alkalmazásfejlesztő, próbálkozzon az SSDT a Visual Studióban.

Az SSDT tartalmazza az SQL Server Object Explorert, amely lehetővé teszi a megjelenítése, csatlakozást, és az SQL Data Warehouse-adatbázisokban parancsprogramok hajtható végre. Gyorsan csatlakozhat az SQL Data Warehouse, egyszerűen kattintson a **Megnyitás Visual Studio** gombra a parancssávon, ha az adatbázis megtekintése részletezi az Azure portálon.  

A Visual Studióban az SSDT megkezdéséhez, lásd: [lekérdezés Azure SQL Data Warehouse a Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Parancssori eszközök
A parancssori eszközök alkalmasak a feladatok automatizálásához.  PowerShell és sqlcmd kétféleképpen kiváló a folyamatok automatizálásához.  Azt javasoljuk, hogy ezek az eszközök nagy számú logikai kiszolgáló kezelése és központi telepítése éles környezetben az erőforrás-változása a szükséges feladatok parancsprogrammal létrehozva, és majd automatikus.

### <a name="dynamic-management-views"></a>Dinamikus felügyeleti nézetekkel
Dinamikus felügyeleti nézetek SQL Data Warehouse kezelési be és vaj. Szinte minden olyan információt, amely felfedi a portálon dinamikus felügyeleti nézetek támaszkodik. SQL Data Warehouse dinamikus felügyeleti nézetek listájának megtekintéséhez lásd: [SQL Data Warehouse rendszernézetek][SQL Data Warehouse system views].

Első lépésként tekintse meg a [kapcsolódás és lekérdezés az Sqlcmd][Connect and query with sqlcmd], és [hozzon létre egy adatbázist (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Skála számítási
Az SQL Data Warehouse teljesítményét, vagy vissza bővítése vagy csökkentése a számítási erőforrásokat Processzor, memória és i/o műveletek sávszélességétől gyors vertikális. A méretezhető teljesítmény, mindössze annyit kell tennie: adattárházegységek (dwu-k) által az adatbázishoz lefoglalt SQL Data Warehouse számának. Az SQL Data Warehouse gyorsan a módosítást, és a mögöttes módosítások a hardver- vagy kezeli.

Dwu-k méretezésével kapcsolatos további tudnivalókért lásd: [méretezhető teljesítmény].

## <a name="pause-and-resume"></a>Szünet és folytatás
Költségek csökkentése érdekében szüneteltetése, és folytassa a számítási erőforrások igény. Például nem fogja használni az adatbázis éjszaka és hétvégén, akkor is ilyen alkalmakkor szünetelteti, és folytatásához azt a nap folyamán. Ön nem számlázni dwu-k, amíg az adatbázis fel van függesztve.

További információkért lásd: [számítási szüneteltetése][Pause compute], és [folytathatja a számítást][Resume compute].

## <a name="performance-best-practices"></a>Teljesítmény gyakorlati tanácsok
Ha az első lépések egy új technológiával, felderíti a tippek és trükkök, amelyek működnek a kezdetektől legjobb jobb takaríthat meg rengeteg időt.  Gyakorlati tanácsok a témakörök számos teljes találja.

Számos összegzését a legfontosabb szempont a számítási feladatok fejlesztésekor, olvassa el [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Lekérdezés figyelése
Néha egy lekérdezése túl hosszú, de még nem meg arról, hogy melyik az a probléma oka. Az SQL Data Warehouse rendelkezik dinamikus felügyeleti nézetekkel (dinamikus felügyeleti nézetek), amely segítségével mérje fel, melyik lekérdezés túl sokáig tart.

Hosszan futó lekérdezések megkereséséhez lásd: [a dinamikus felügyeleti nézetek használatával számítási feladat figyeléséhez][Monitor your workload using DMVs].

## <a name="security"></a>Biztonság
Biztonságos rendszer fenntartására, kell lennie a riasztás, és bármilyen típusú jogosulatlan hozzáférés elleni védelmet. Gondoskodjon arról, hogy a tűzfalszabályok helyen, hogy csak engedélyezett IP-címek kapcsolódhatnak egy biztonsági rendszert kell. A felhasználói hitelesítő adatok a megfelelő hitelesítés szükséges. Miután egy felhasználó csatlakozott az adatbázishoz, a felhasználó csak kell végre műveletek minimális száma. Adatok védelme érdekében titkosítási is használhat. Szintén fontos, hogy a naplózás, és nyomon követését, események is végig, ha a gyanús tevékenységeket.

Biztonsági kezelésével kapcsolatos információkért látogasson el a [biztonsági áttekintése][Security overview].

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Az adatok megbízható backps, akkor minden éles adatbázis nagyon fontos részét képezik. Az SQL Data Warehouse biztosítja az adatok biztonságos automatikusan biztonsági mentést kell készíteni a aktív adatbázisainak rendszeres időközönként. Ezek a biztonsági másolatok teszik forgatókönyvek, ahol korábban az adatok sérült, vagy véletlenül eldobott az adatok vagy az adatbázis helyreállítása.  Az adatok biztonsági mentés ütemezése adatmegőrzési és egy adatbázis visszaállítása: [a pillanatkép-visszaállítás][Restore from snapshot].

## <a name="next-steps"></a>Következő lépések
Használatával a helyes adatbázis tervezési alapelvek könnyebben kezelheti az SQL Data Warehouse az adatbázisokat. További információkért látogasson el a [fejlesztői áttekintés][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[méretezhető teljesítmény]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
