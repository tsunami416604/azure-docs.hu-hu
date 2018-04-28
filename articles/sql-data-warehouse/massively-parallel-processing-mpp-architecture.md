---
title: Az SQL Data Warehouse - MPP architektúra |} Microsoft Docs
description: Ismerje meg, hogyan egyesíti az Azure SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) az Azure storage magas teljesítmény és méretezhetőség eléréséhez.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e8fef156f4b78c9f7241c9eb9623e061f5a31fe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Az SQL Data Warehouse - nagymértékben párhuzamos feldolgozási (MPP) architektúra
Ismerje meg, hogyan egyesíti az Azure SQL Data Warehouse a nagymértékben párhuzamos feldolgozási (MPP) az Azure storage magas teljesítmény és méretezhetőség eléréséhez. 

## <a name="mpp-architecture-components"></a>Az MPP architektúra összetevők
Az SQL Data Warehouse egy számítási adatok feldolgozása szét több csomópont architektúra kibővítési kihasználja. A skálázási egység szerint egy adatraktáregység számítási teljesítményt absztrakciós. Az SQL Data Warehouse elkülönítésére szolgál számítási, tárolási, mely lehetővé teszi, hogy méretezhető számítási függetlenül az adatok a rendszer a.

![Az SQL Data Warehouse architektúrája](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Az SQL Data Warehouse csomópont-alapú architektúrát használ. Alkalmazások csatlakozni, és T-SQL parancsokkal kiadni a vezérlő csomópont, amely a központi hely, az adatraktár. A vezérlő csomópont az MPP motor, amely optimalizálja a lekérdezések párhuzamos feldolgozásra, és majd átadja a műveleteket számítási csomópontok a munkájuk párhuzamosan futtatja. A számítási csomópontok összes felhasználói adatot az Azure Storage tárolja, és a párhuzamos lekérdezések futtatása. Az adatátviteli szolgáltatás (DMS) egy olyan rendszerszintű belső szolgáltatás, amely a párhuzamos lekérdezések futtatása és ad vissza pontos eredményeket, szükség szerint a csomópontok között mozgatja az adatokat. 

Az SQL Data Warehouse a tárterület és a számítási műveletek elkülönítésével a következőkre képes:

* Függetlenül a méret számítási teljesítményt tárolási igényei függetlenül.
* Növelheti vagy csökkentheti a számítási kapacitást az adatok áthelyezése nélkül.
* A számítási kapacitás az adatok érintetlenül maradnak, miközben, csak kell fizetnie a tároláshoz.
* A működési időn belül folytatni tudja a számítási kapacitást.

### <a name="azure-storage"></a>Azure Storage tárterület
Az SQL Data Warehouse az Azure storage használatával a felhasználói adatok biztonságát.  Mivel az adatok tárolásának és az Azure storage kezeli, az SQL Data Warehouse külön-külön a tárhelyhasználati díja. Maga az adat a szilánkos **terjesztéseket** a rendszer teljesítményének optimalizálása érdekében. Kiválaszthatja, hogy mely horizontális mintát juttassa el az adatokat, ha a tábla. Az SQL Data Warehouse támogatja ezeket a horizontális mintákat:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

### <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az adatraktár agy. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az MPP motor a párhuzamos lekérdezések optimalizálása és a vezérlő csomópont fut. Amikor az SQL Data Warehouse-T-SQL-lekérdezést, a vezérlő csomópont alakítja minden terjesztési párhuzamosan futtathat lekérdezések.

### <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok adja meg a számítási teljesítménnyel rendelkezik. Képezze le az azokat a terjesztéseket számítási csomópontok feldolgozásra. További számítási erőforrásokat kell fizetnie, mert az SQL Data Warehouse újra leképezi a terjesztéseket, a rendelkezésre álló számítási csomópontokhoz. A számítási csomópontok tartomány 1 és 60, és határozza meg a szolgáltatási szint az adatraktár.

Minden számítási csomópont van a csomópont-Azonosítót, amely a rendszer láthatók. A számítási csomópont-azonosító keresi, sys.pdw_nodes kezdődő rendszernézetek node_id oszlopban tekintheti meg. Az alábbi rendszer nézet listájáért lásd: [MPP rendszernézetek](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Adatátviteli szolgáltatás
Adatok adatátviteli szolgáltatás (DMS) az adatátvitelt jelölik a számítási csomópontok közötti koordinálja adatok átviteli technológiát. Egyes lekérdezések esetében meg kell annak érdekében, hogy a párhuzamos lekérdezések ad vissza pontos eredményeket adatátvitelt jelölik. Adatátvitel szükség, ha a DMS biztosítja a megfelelő adatok beolvasása a megfelelő helyre. 

## <a name="distributions"></a>Felosztások

A terjesztés az tárolása és feldolgozása párhuzamos lekérdezések elosztott adatokon rendszert futtató alapvető egysége. Az SQL Data Warehouse-lekérdezés futtatása, ha a munkahelyi 60 kisebb lekérdezésekre párhuzamosan futó van osztva. A 60 kisebb lekérdezésekre mindegyikének fut, az adatok terjesztéseket egyik. Minden számítási csomópont egy vagy több 60 terjesztéseket kezeli. Maximális számítási erőforrással adatraktár rendelkezik egy terjesztési egyes számítási csomópontjain. Minimális számítási erőforrással adatraktár rendelkezik a megfelelő kiadásának egy számítási csomóponton.  

## <a name="hash-distributed-tables"></a>Kivonatoló elosztott táblák
Egy kivonattáblát elosztott biztosíthat a lehető legjobb lekérdezési teljesítmény az összekapcsolásokhoz és az összesítések nagy táblákon. 

Részekre bonthatók az adatok egy ujjlenyomat-elosztott táblába az SQL Data Warehouse minden egyes sorára deterministically hozzárendelése egy terjesztési használja a kivonatoló függvényt. A tábla definíciójában egyik oszlop van kijelölve a terjesztési oszlopként. A kivonatoló függvényt a terjesztési oszlopban szereplő értékeket használja minden egyes sorára hozzárendelése egy terjesztési.

A következő ábra szemlélteti, hogy egy teljes (tábla nem osztott) lekérdezi tárolási módját kivonatoló elosztott táblázatként. 

![Elosztott tábla](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "elosztott tábla")  

* Minden sor egy terjesztési tartozik.  
* Determinisztikus kivonatoló algoritmus minden sor egy terjesztési rendeli hozzá.  
* ) Eloszlása feladatonként (a tábla sorainak száma változó által a különböző méretű táblák látható módon.

Nincsenek a teljesítménnyel kapcsolatos szempontok terjesztési oszlop, például megkülönböztethetőség adatok döntés vagy a rendszer futó lekérdezések típusú kijelölés.

## <a name="round-robin-distributed-tables"></a>Ciklikus multiplexelés elosztott táblák
Ciklikus multiplexelés tábla a legegyszerűbb tábla létrehozásához, és betölti a átmeneti tárolási tábla használatakor gyors teljesítményt nyújt.

Ciklikus multiplexelés elosztott tábla adatok a tábla között, de minden további optimalizálás nélkül egyenlően osztja el. Egy terjesztési először véletlenszerűen kiválasztott majd sorok pufferek kerül és azokat a terjesztéseket egymás után. Gyors adatainak betöltése egy ciklikus multiplexelés táblába, de a lekérdezési teljesítmény gyakran lehet jobb elosztott kivonatoló táblákkal. Ciklikus multiplexelés táblákon illesztések szükséges adatok reshuffling, és ez további időt vesz igénybe.


## <a name="replicated-tables"></a>Replikált táblák
A replikált tábla kis táblák a leggyorsabb lekérdezési teljesítményt biztosít.

A replikált tábla gyorsítótárazza a tábla minden számítási csomóponton teljes másolata. Ennek következtében a tábla replikálása szükségtelenné join vagy összesítés előtt a számítási csomópontok közötti adattovábbításra. Replikált táblák leginkább kis táblákkal ki van használva. Extra tárhelyre szükség, és nincsenek további terhelés és sem merülnek fel, amikor nagy adatok írásakor táblák praktikus.  

Az alábbi ábrán látható, a replikált tábla. Az SQL Data Warehouse a replikált tábla gyorsítótárazza, az első terjesztési minden számítási csomóponton.  

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
