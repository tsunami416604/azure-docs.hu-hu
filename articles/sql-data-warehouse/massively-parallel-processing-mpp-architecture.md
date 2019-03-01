---
title: Az Azure SQL Data Warehouse - MPP-architektúra |} A Microsoft Docs
description: Ismerje meg, hogyan kombinálja az Azure SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) és az Azure storage nagy teljesítménye és skálázhatósága eléréséhez.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 236009a3292f26d7fda73013c022f40535aa1ecb
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992328"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Az Azure SQL Data Warehouse - nagymértékben párhuzamos feldolgozási (MPP) architektúra
Ismerje meg, hogyan kombinálja az Azure SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) és az Azure storage nagy teljesítménye és skálázhatósága eléréséhez. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Az MPP architektúra összetevői
Az SQL Data Warehouse egy horizontális felskálázási architektúra szét a számítási feldolgozás az adatok több csomópont használja. A skálázási egység a számítási teljesítményt, az úgynevezett absztrakciós egy [adattárházegység](what-is-a-data-warehouse-unit-dwu-cdwu.md). Az SQL Data Warehouse elkülöníti a számítási tárolóból, mely lehetővé teszi, hogy skálázható számítási függetlenül az adatok a rendszerben.

![Az SQL Data Warehouse architektúrája](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Az SQL Data Warehouse egy csomópont-alapú architektúrát használ. Alkalmazások csatlakoztatása és a T-SQL-parancsokat kiadni a vezérlő csomópont, amely az adatraktár-bejegyzést a hibaérzékeny pont. Az MPP-motor, amely optimalizálja a lekérdezések párhuzamos feldolgozásra, majd átadja a műveleteket párhuzamosan munkájuk számítási csomópontok a vezérlő csomópont fut. A számítási csomópontok az összes felhasználói adatok tárolása az Azure Storage-ban, és a párhuzamos lekérdezéseket. Az adatátviteli szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely helyez át adatokat ad vissza pontos eredményeket és a lekérdezések párhuzamos futtatásához szükség szerint a csomópontok között. 

Az SQL Data Warehouse a tárterület és a számítási műveletek elkülönítésével a következőkre képes:

* Egymástól függetlenül a méret számítási teljesítményt attól függetlenül, a szükséges tárhelyet.
* Növelheti vagy csökkentheti a számítási kapacitást az adatok áthelyezése nélkül.
* A számítási kapacitás az adatok érintetlenül hagyásával, így csak fizetnie storage.
* A működési időn belül folytatni tudja a számítási kapacitást.

### <a name="azure-storage"></a>Azure Storage tárterület
Az SQL Data warehouse-bA az Azure storage segítségével a felhasználói adatok biztonsága.  Mivel az adatok tárolásának és kezeli az Azure storage, SQL Data Warehouse külön-külön tárhelyet díjat. Az adat, szilánkokra osztott **disztribúciók** a rendszer a teljesítmény optimalizálása érdekében. Kiválaszthatja, melyik horizontális skálázási minta használatával ossza el az adatokat, ha a tábla meghatározása. Az SQL Data Warehouse a horizontális skálázási mintát támogat:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

### <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont a data warehouse az agy. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az MPP-motor a vezérlő csomópont optimalizálása és koordinálja a párhuzamos lekérdezéseket futtat. Amikor egy T-SQL-lekérdezést küld az SQL Data Warehouse, a vezérlő csomópont lekérdezésekké alakítja, lekérdezések, amelyek az egyes terjesztési párhuzamosan futtatható.

### <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok a számítási teljesítményt nyújtanak. Számítási csomópontok feldolgozási disztribúciók térképet. További számítási erőforrásokat kell fizetnie, mint az SQL Data Warehouse újra leképezi a disztribúciók elérhető számítási csomópontjain. Számítási csomópontok tartomány 1 és 60, és határozza meg a szolgáltatási szint a data warehouse-hoz.

Minden számítási csomópont van egy csomópont-azonosító, amely a rendszer láthatók. A számítási csomópont-azonosító alapján keres a sys.pdw_nodes kezdődő rendszernézetek $node_id oszlopban megjelenik. Ezek rendszernézetek listáját lásd: [MPP rendszernézetek](http://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Adatátviteli szolgáltatás
Az adatátviteli szolgáltatás (DMS) az, hogy koordinálja a számítási csomópontok közötti adatáthelyezés adatok átviteli technológiát. Néhány lekérdezés annak érdekében, hogy a párhuzamos lekérdezések pontos eredményeket adjon vissza adatáthelyezés van szükség. Amikor szükség az adatmozgatás, a DMS biztosítja, hogy a megfelelő adatokat lekérdezi a megfelelő helyre. 

## <a name="distributions"></a>Felosztások

A terjesztés az tárolása és feldolgozása a párhuzamos lekérdezések elosztott adatokon a Futtatás alapvető egysége. Amikor az SQL Data Warehouse egy lekérdezés fut, a munkahelyi 60 kisebb lekérdezések párhuzamos futtatását van felosztva. A 60 kisebb lekérdezésekre mindegyike futtat az adatok disztribúciók egyik. Minden számítási csomópont egy vagy több a 60 elosztás kezeli. Maximális számítási erőforrásokat az adattárház számítási csomópontok egy terjesztési rendelkezik. Minimális számítási erőforrásokat az adattárház rendelkezik minden disztribúcióján használhatók egy számítási csomóponton.  

## <a name="hash-distributed-tables"></a>Kivonatoló elosztott táblák
Egy kivonat alapján elosztott tábla nagy táblák a lehető legjobb lekérdezési teljesítmény összekapcsolásokhoz és az összesítések biztosít alkalmazhatók. 

Bonthatók az adatok egy kivonatot elosztott táblába az SQL Data Warehouse determinisztikus módon rendel minden sor egy terjesztési használja a kivonatoló függvényt. A tábla definíciójában az oszlopok egyikének elsődlegesként lett megjelölve az oszlopot. A kivonatolási függvény értékeket használja a terjesztési oszlopban minden sor hozzárendelése egy terjesztési.

A következő ábra szemlélteti, hogyan (nem elosztott tábla) teljes memóriaállapota tárolva van, kivonatoló elosztott táblázatként. 

![Elosztott tábla](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "elosztott tábla")  

* Minden sor egy terjesztési tartozik.  
* Determinisztikus kivonatoló algoritmust rendeli minden sor egy terjesztési.  
* Tábla sorok száma terjesztési száma eltérő, ahogy azt a különböző méretű táblák.

Nincsenek a teljesítménnyel kapcsolatos megfontolások kiválasztását az elosztási oszlop, például megkülönböztethetőség Adateltérés és a rendszeren futó lekérdezések típusú.

## <a name="round-robin-distributed-tables"></a>Elosztott táblák ciklikus időszeletelés
Ciklikus időszeleteléses tábla a legegyszerűbb tábla létrehozásához és használat átmeneti táblaként terhelés esetén gyors teljesítményt biztosít.

Ciklikus időszeleteléses elosztott tábla egyenletesen elosztja az adatokat a táblázat között, de minden további optimalizálás nélkül. Egy terjesztési először véletlenszerűen kiválasztott és majd a sorok pufferek hozzá vannak rendelve disztribúciók egymás után. Gyors táblába való betöltéséhez adatokat egy Ciklikus időszeleteléses, de a lekérdezési teljesítmény gyakran hatékonyabb munkavégzés az elosztott kivonattáblák is lehet. Azokon a táblákon, Ciklikus időszeleteléses illesztések szükséges adatok reshuffling, és ez további időt vesz igénybe.


## <a name="replicated-tables"></a>Replikált táblák
Egy replikált tábla biztosít a leggyorsabb lekérdezési teljesítmény, kis méretű táblák esetében.

Egy replikált tábla gyorsítótárazza a tábla minden számítási csomóponton teljes másolata. Ebből következően replikál egy táblát eltávolítása előtt egy összesítés vagy a számítási csomópontok közötti adatátvitelhez szükség. Replikált táblák kisméretű táblák leghasznosabbnak vannak. Extra tárterület megadása kötelező, és győződjön meg arról, nagy táblák praktikus, amely az adatok írásakor felmerülő további erőforrásokra van.  

Az alábbi ábrán látható egy replikált tábla. Az SQL Data warehouse-hoz a replikált tábla tárolja a rendszer az első terjesztési minden számítási csomóponton.  

![Replikált tábla](media/sql-data-warehouse-distributed-data/replicated-table.png "replikált tábla") 

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data Warehouse szolgáltatást, tudjon meg többet az [SQL Data Warehouse gyors létrehozásáról][create a SQL Data Warehouse] és a [mintaadatok betöltéséről][load sample data]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Támogatási jegy létrehozása]
* [MSDN-fórum]
* [Stack Overflow-fórum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
