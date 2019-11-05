---
title: Az Azure szinapszis Analytics (korábbi nevén SQL DW) architektúrája | Microsoft Docs
description: Ismerje meg, hogy az Azure szinapszis Analytics (korábbi nevén SQL DW) hogyan ötvözi a nagymértékben párhuzamos feldolgozást (MPP) az Azure Storage szolgáltatással a nagy teljesítmény és méretezhetőség érdekében.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b463b0806d39ba20ae714c8785e5c0d227ce481b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466397"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure szinapszis Analytics (korábban SQL DW) architektúrája 

Az Azure szinapszis egy korlátlan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a Big adatelemzést. Lehetővé teszi, hogy a használati feltételek alapján, akár kiszolgáló nélküli igény szerinti vagy kiépített erőforrásokkal is lekérdezze az adatait. Az Azure szinapszis az azonnali BI-és gépi tanulási igényekhez kapcsolódóan egységes felhasználói élményt nyújt az adatgyűjtéshez, előkészítéséhez, kezeléséhez és kiszolgálásához.

 Az Azure szinapszis négy összetevőből áll:
- SQL Analytics: teljes T-SQL-alapú elemzés 
    - SQL-készlet (fizetés/DWU kiépítve) – általánosan elérhető
    - Igény szerinti SQL-szolgáltatás (fizetés/TB feldolgozott) – (előzetes verzió)
- Spark: mélyen integrált Apache Spark (előzetes verzió) 
- Adatintegráció: hibrid Adatintegráció (előzetes verzió)
- Studio: egyesített felhasználói élmény.  (Előzetes verzió)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>SQL Analytics MPP architektúra-összetevők

Az [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) egy kibővíthető architektúrát használ a számítási folyamatok több csomóponton történő elosztásához. A skála egysége az [adattárház-egységként](what-is-a-data-warehouse-unit-dwu-cdwu.md)ismert számítási teljesítmény absztrakciója. A számítás elkülönül a tárterülettől, amely lehetővé teszi a számítások egymástól független méretezését a rendszeren lévő adatoktól függetlenül.

![SQL Analytics-architektúra](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Az SQL Analytics node-alapú architektúrát használ. Az alkalmazások a T-SQL-parancsokat egy vezérlő csomóponthoz csatlakoznak, amely az SQL Analytics egyszeri belépési pontja. A vezérlő csomópontja futtatja az MPP motort, amely optimalizálja a párhuzamos feldolgozásra irányuló lekérdezéseket, majd a műveleteket a számítási csomópontokon továbbítja a munkájukat párhuzamosan. 

A számítási csomópontok az Azure Storage-ban tárolják az összes felhasználói adatforrást, és futtatják a párhuzamos lekérdezéseket. Az adatátviteli szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely a lekérdezések párhuzamos futtatásához szükséges módon helyezi át az adatait, és pontos eredményeket ad vissza. 

A leválasztott tárolással és számítással az SQL Analytics használata esetén a következőket teheti:

* A tárolási igényektől függetlenül a számítási kapacitás egymástól függetlenül méretezhető.
* Az adatok áthelyezése nélkül növelheti vagy csökkentheti a számítási teljesítményt egy SQL-készleten (adatraktáron) belül.
* Szüneteltetheti a számítási kapacitást az adatok érintetlenül hagyásával, így csak a tárterületért kell fizetnie.
* A működési időn belül folytatni tudja a számítási kapacitást.

### <a name="azure-storage"></a>Azure Storage-tárterület

Az SQL Analytics kihasználja az Azure Storage-t, hogy a felhasználói adatai biztonságban maradjanak.  Mivel az Azure Storage tárolja és kezeli az adatait, külön díjat számítunk fel a tárterület-felhasználásért. Maga az adat **eloszlásba** van osztva a rendszer teljesítményének optimalizálása érdekében. Kiválaszthatja, hogy melyik horizontális Felskálázási mintát kell használnia az adatterjesztéshez a tábla meghatározásakor. Ezek a horizontális skálázási minták támogatottak:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

### <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az MPP-motor a vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Ha T-SQL-lekérdezést küld az SQL Analyticsnek, a vezérlési csomópont átalakítja azokat a lekérdezéseket, amelyek párhuzamosan futnak az egyes eloszlásokon.

### <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. A disztribúciók a számítási csomópontokat dolgozzák fel feldolgozásra. A további számítási erőforrásokért az SQL Analytics újra leképezi a disztribúciókat a rendelkezésre álló számítási csomópontokra. A számítási csomópontok száma 1 és 60 közötti tartományba esik, és az SQL Analytics szolgáltatási szintje határozza meg.

Minden számítási csomóponthoz tartozik egy csomópont-azonosító, amely a rendszernézetekben látható. A számítási csomópont AZONOSÍTÓját úgy tekintheti meg, hogy megkeresi a node_id oszlopot a rendszernézetekben, amelyek nevei a sys. PDW _nodes kezdődnek. A rendszernézetek listáját a következő témakörben tekintheti meg: [MPP rendszernézetek](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Adatátviteli szolgáltatás
Az adatátviteli szolgáltatás (DMS) az adatátviteli technológia, amely koordinálja az adatátvitelt a számítási csomópontok között. Egyes lekérdezések adatáthelyezést igényelnek annak biztosításához, hogy a párhuzamos lekérdezések pontos eredményeket állítsanak vissza. Ha adatáthelyezésre van szükség, a DMS biztosítja a megfelelő adatelérést a megfelelő helyre. 

## <a name="distributions"></a>Disztribúciók

A disztribúció az elosztott adatokon futó párhuzamos lekérdezések tárolásának és feldolgozásának alapvető egysége. Ha az SQL Analytics egy lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésre van osztva. 

Az 60-es kisebb lekérdezések az egyik adateloszláson futnak. Minden számítási csomópont egy vagy több 60-disztribúciót kezel. A maximális számítási erőforrásokkal rendelkező SQL-készletekhez számítási csomópontok egyetlen eloszlással rendelkeznek. A minimális számítási erőforrásokkal rendelkező SQL-készletek egy számítási csomóponton lévő összes disztribúcióval rendelkeznek.  

## <a name="hash-distributed-tables"></a>Kivonat – elosztott táblák
A kivonatok elosztott táblája a legnagyobb lekérdezési teljesítményt nyújtja a nagyméretű táblákhoz való illesztések és összesítések számára. 

Az adatokat egy kivonatoló eloszlású táblázatba az SQL Analytics kivonatoló függvénnyel determinisztikus módon az egyes sorok hozzárendelését egy adott eloszláshoz. A tábla definíciójában az egyik oszlop a terjesztési oszlopként van kijelölve. A kivonatoló függvény a eloszlás oszlopban található értékeket használja az egyes sorok eloszláshoz rendeléséhez.

Az alábbi ábra azt szemlélteti, hogyan történik a teljes (nem elosztott) táblázat tárolása kivonatként elosztott táblaként. 

![Elosztott tábla](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Elosztott tábla")  

* Minden sor egy eloszláshoz tartozik.  
* A determinisztikus kivonatoló algoritmus az egyes sorokat egyetlen eloszláshoz rendeli.  
* A tábla sorainak száma eloszlása a különböző méretű táblákban látható módon változik.

A terjesztési oszlop kiválasztásához teljesítménnyel kapcsolatos szempontokat kell figyelembe venni, például a különbségtételt, az adattorzítást, valamint a rendszeren futó lekérdezések típusait.

## <a name="round-robin-distributed-tables"></a>Ciklikus multiplexelés – elosztott táblák
A betöltések előkészítési táblája, amely gyors teljesítményt nyújt, és rövid idő alatt készíti el a táblázatot.

A Round-Robin elosztott tábla egyenletesen osztja el az adategységeket a táblázatban, de további optimalizálás nélkül. A rendszer először véletlenszerűen választja ki a eloszlást, majd a sorok puffereit egymás utáni eloszláshoz rendeli. Gyorsan betöltheti az adatokat egy ciklikus multiplexelés-táblázatba, de a lekérdezési teljesítmény gyakran jobb lehet a kivonatoló elosztott táblákkal. A ciklikus időszeletelésű táblákhoz való kapcsolódáshoz szükség van az adatkeverésre, és ez további időt vesz igénybe.


## <a name="replicated-tables"></a>Replikált táblák
A replikált táblák a leggyorsabb lekérdezési teljesítményt biztosítják a kis táblákhoz.

A replikált tábla a tábla teljes másolatát gyorsítótárazza az egyes számítási csomópontokon. Ennek következtében a táblázatok replikálásával megszűnik az adatok átvitele a számítási csomópontok között a csatlakozás vagy összesítés előtt. A replikált táblákat a legjobban kis táblák használják. További tárterületre van szükség, és a nagyméretű táblázatokat nem használó adatírás során további terhelést kell fizetni.  

Az alábbi ábrán egy olyan replikált tábla látható, amely az egyes számítási csomópontok első eloszlásában van gyorsítótárazva.  

![Replikált tábla](media/sql-data-warehouse-distributed-data/replicated-table.png "Replikált tábla") 

## <a name="next-steps"></a>További lépések
Most, hogy már ismeri az Azure Szinapszisot, megtudhatja, hogyan [hozhat létre gyorsan SQL-készletet][create a SQL pool] , és hogyan [tölthető be a mintaadatok][load sample data]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintse meg a többi Azure szinapszis-erőforrást.  

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
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
