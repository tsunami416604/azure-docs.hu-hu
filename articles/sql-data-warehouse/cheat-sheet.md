---
title: Azure SQL Data Warehouse lap cheat |} Microsoft Docs
description: "Hivatkozások, bevált gyakorlatokat, amelyekkel gyorsan létrehozása az Azure SQL Data Warehouse megoldások keresése."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse lap cheat
Ezen az oldalon kell alakítsa ki a fő használati esetekben a data warehouse megoldás. Ez formátumához egy kiváló támogatást a használatában világszínvonalú adatraktár létrehozásához kell lennie, de erősen ajánlott minden lépése részletek többet. Először ajánlott a kiváló cikk milyen SQL Data warehouse elolvasása  **[és nem]**.

Az alábbiakban az a folyamat, kövesse az SQL Data Warehouse tervezési, hogy megkezdhesse vázlatot.

![Vázlat]

## <a name="queries-and-operations-across-tables"></a>Lekérdezések és műveletek táblák között

Valóban fontos ismerni a legfontosabb műveletek és az adatraktár zajló lekérdezések. A data warehouse architektúrája élveznek, hogy ezek a műveletek. Gyakori példák műveletek lehet:
* Egy vagy két tény tábla dimenziót tartalmazó táblák, ez a táblázat szűrést az adott időszakban, és az eredmények hozzáfűzése egy adatpiacot az
* Az a tény értékesítési nagy vagy kis frissítések végrehajtása
* A táblák csak adatok hozzáfűzése

Működés ismerete segít a táblák kialakításának optimalizálásához.

## <a name="data-migration"></a>Adatok áttelepítése

Ajánlott első betöltés az adatok  **[az ADLS]**  vagy Azure Blob Storage tárolóban. Ezt követően az adatok betöltése az SQL Data Warehouse egy átmeneti tárolási tábla Polybase kell használnia. Azt javasoljuk, hogy a következő konfigurációt:

| Tervezés | Ajánlás |
|:--- |:--- |
| Disztribúció | Ciklikus időszeletelés |
| Indexelés | Halommemória |
| Particionálás | None |
| Erőforrásosztály | largerc vagy xlargerc |

További információ  **[adatáttelepítés], [az adatok betöltése]**  rendelkező  **[mélyebb útmutatást] betöltése a**. 

## <a name="distributed-or-replicated-tables"></a>Elosztott vagy replikált táblák

Azt javasoljuk, hogy a következő stratégiák attól függően, hogy a táblázat tulajdonságai:

| Típus | A nagy méretezése | Figyelje meg, hogy ha...|
|:--- |:--- |:--- |
| Replikált | • Kis dimenziótáblák a tároló (~ 5 x tömörítés) tömörítés után legalább 2 GB csillagséma |• A tábla tranzakciók számos írjanak (pl.: beszúrásához upsert, a törlés, a frissítés)<br></br>• Módosítsa kiépítés gyakran Data Warehouse egységek (DWU)<br></br>• 2-3 oszlopok és a tábla csak használhatja sok oszlopot tartalmaz.<br></br>A replikált tábla indexeli • |
| Ciklikus multiplexelés (alapértelmezett) | • Ideiglenes/átmeneti tárolási tábla<br></br> • Nem egyértelmű kulcs, vagy nem jó jelölt oszlop csatlakoztatása |• Teljesítménycsökkenést adatok mozgása miatt |
| Kivonat | • A ténytáblák<br></br>• Nagy dimenziótáblák |• A terjesztési kulcs nem lehet frissíteni. |

**Tipp:**
* Ciklikus multiplexelés kezdődnie, de egy kivonatoló terjesztési stratégia számára, hogy kihasználja a nagy párhuzamos architektúra aspire
* Győződjön meg arról, hogy közös kivonatoló kulcsok rendelkezik-e a ugyanazt az adatformátumot
* A varchar formátum nem terjesztése
* Közös kivonatoló kulccsal, hogy egy ténytábla gyakori összekapcsolási műveletek a dimenziótáblák lehet elosztott kivonata
* Használjon  *[sys.dm_pdw_nodes_db_partition_stats]*  bármely döntés az adatok elemzéséhez
* Használjon  *[sys.dm_pdw_request_steps]*  elemzése adatok áthelyezések lekérdezések mögött, az idő adás figyelése és a véletlen műveletek egy. Tekintse át a telepítési stratégia hasznos lehet.

További információ  **[replikált táblák] és [táblák elosztott]**.

## <a name="indexing-your-table"></a>A tábla indexelő

Az indexelő **kiváló** gyorsan táblák olvasásához. Nincs olyan egyedi technológiák is használhatja a igények alapján:

| Típus | A nagy méretezése | Figyelje meg, hogy ha...|
|:--- |:--- |:--- |
| Halommemória | • Átmeneti/ideiglenes tábla<br></br>• Kis táblákra kis |• A keresési megvizsgálja a teljes táblázat |
| Fürtözött Index | • Legfeljebb 100-m sorok tábla<br></br>• Nagy (több mint 100 millió sort) oszloppal rendelkező táblákon csak 1-2 fokozottan szolgálnak. |A replikált tábla használt •<br></br>• Összetett lekérdezések több illesztési, Group By műveletek használata esetén<br></br>• Ellenőrizze frissítések az indexelt oszlopokon, szükséges memória |
| Fürtözött Oszlopcentrikus Index (közösségi koordináló intézet) (alapértelmezés) | • Nagy táblák (több mint 100 millió sort) | A replikált tábla használt •<br></br>• Nagy elvégezte frissítési műveleteket a tábla<br></br>• Túlzott a tábla particionálása: sorcsoportok nem fedik másik terjesztési csomópontok és a partíciók |

**Tipp:**
* Egy fürtözött indexek fölött érdemes szűrő fokozottan használt oszlophoz nem fürtözött Index hozzáadása. 
* Ügyeljen arra, hogyan kezelheti a memória, a közösségi koordináló intézet tartalmazó tábla. Adatok betöltése, ha azt szeretné, nagy erőforrásosztály kihasználják a felhasználó (vagy a lekérdezés). Akkor ügyeljen arra, hogy kerülje a tisztítás és a sok kisméretű tömörített sor eszközcsoportok létrehozása
* A közösségi koordináló intézet számítási rétegben sziklák optimalizálva
* A közösségi koordináló intézet lassú teljesítmény gyenge tömörítés a sor csoportok miatt fordulhat elő, érdemes újbóli létrehozásához vagy átrendezéséhez a közösségi koordináló intézet. Azt szeretné, hogy a tömörített sorcsoportok legalább 100 k sorszám. Az ideális egy sor csoport 1-m sorainak.
* A növekményes terhelés gyakoriságát és mérete alapján átrendezéséhez, vagy az indexek újraépítése automatizálni kívánt. Mindig hasznos a rugó tisztítás.
* Ha meg szeretné egy sorcsoport trim stratégiai kell: hogyan nagy vannak a nyitott sorcsoportok? Adatok betöltése a következő napon várhatók?

További információ  **[indexek]**.

## <a name="partitioning"></a>Particionálás
Előfordulhat, hogy partícióazonosító a táblázatban, ha nagy ténytáblák (> 1B sor tábla). 99 % az esetek a partíciós kulcs alapján dátum. Ügyeljen arra, hogy nem túlzott partíció, különösen, ha a fürtözött Oszlopcentrikus indexet.
Az átmeneti tárolási táblák ETL igénylő, révén kihasználhatja a particionálást. Elősegíti a adatok életciklusának kezelésére.
Ügyeljen arra, hogy az adatok, különösen ha a fürtözött Oszlopcentrikus Index overpartition.

További információ  **[partíciók]**.

## <a name="incremental-load"></a>Növekményes betöltése

Először meg kell győződnie arról, hogy az adatok betöltése nagyobb erőforrás osztályok osszon ki. Az ETL-folyamatok automatizálása SQL DW a Polybase és ADF V2 használatát javasoljuk.

A nagy kötegelt frissítések előzményadatait ajánlott, először az érintett adatok törlése. Majd végezhet a tömeges Beszúrás az új adatok. A 2. lépés megoldás, hatékonyabb.

## <a name="maintain-statistics"></a>Statisztikák karbantartása
Automatikus-statisztikák fog hamarosan általánosan elérhető. Addig SQL Data Warehouse igényel manuális karbantartást statisztikai adat. Fontos, statisztikai adatainak frissítése **jelentős** módosítások fordulhat elő, az adatokhoz. Ez segít a lekérdezésterveket optimalizálása. Ha az összes, a statisztikai karbantartása túl sokáig tart, érdemes lehet több mérlegelni oszlopok statisztika rendelkeznie kell. A frissítési gyakoriságának is meghatározhat. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. A legtöbb juttatás nyerhet statisztika rendelkező oszlopokon érintett csatlakozik, a WHERE záradékban használt oszlopok és a GROUP BY található oszlopok.

További információ  **[statisztika]**.

## <a name="resource-class"></a>Erőforrásosztály
Az SQL Data Warehouse erőforráscsoportokat használ arra, hogy memóriát foglaljon le a lekérdezésekhez. Ha a lekérdezés vagy betöltése sebesség növelése érdekében több memóriára van szüksége, magasabb erőforrás osztályok kell lefoglalni. A tükrözés oldalon nagyobb erőforrás osztályokat párhuzamossági hatással van. Minden felhasználó egy nagy erőforrásosztály helyezése előtt figyelembe kell venni az szeretne.

Ha azt észleli, hogy a lekérdezések túl hosszúak, ellenőrizze, hogy a felhasználók nagy erőforrás osztályok nem működnek. Nagy erőforrás osztályok sok egyidejű üzembe helyezési ponti felhasználását. Más lekérdezések sorba is okozhatják.

Végül a számítási optimalizált csomagot használja, ha minden erőforrásosztály lekérdezi 2,5 x több memóriát, mint a rugalmas optimalizált rétegen.

Munkavégzés további  **[erőforrás osztályok és feldolgozási]**.

## <a name="lower-your-cost"></a>A költségek csökkentése
Az SQL Data Warehouse egyik legfőbb jellemzője, hogy ha nem használja, felfüggesztheti a szolgáltatást, ezzel leállítva a számítási erőforrások számlázását. Egy másik fontos jellemző az erőforrások méretezhetősége. A felfüggesztés és a méretezés az Azure Portalon keresztül vagy PowerShell-parancsok használatával lehetséges.

Az Azure Functions szeretné automatikus skálázása most időpontjában:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>Architektúra a teljesítmény optimalizálása

Javasoljuk, hogy az SQL-adatbázis és az Azure Analysis Services Hub és küllők architektúra figyelembe véve. Ez különböző felhasználói csoportok közben is kihasználva néhány speciális biztonsági szolgáltatásokat az SQL-Adatbázisból és az Azure Analysis Services munkaterhelés elkülönítését biztosíthat. Azt is adja meg a felhasználóknak korlátlan párhuzamossági módot.

További információ  **[SQL DW kihasználva tipikus architektúrák]**.

Egy kattintással központi telepítése az SQL-adatbázis SQL DW-adatbázisok a küllők:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Vázlat]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[az adatok betöltése]:./design-elt-data-loading.md
[mélyebb útmutatást]: ./guidance-for-loading-data.md
[indexek]:./sql-data-warehouse-tables-index.md
[partíciók]:./sql-data-warehouse-tables-partition.md
[statisztika]:./sql-data-warehouse-tables-statistics.md
[erőforrás osztályok és feldolgozási]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[SQL DW kihasználva tipikus architektúrák]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[és nem]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[adatáttelepítés]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[replikált táblák]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[táblák elosztott]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[az ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
